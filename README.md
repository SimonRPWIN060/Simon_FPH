# Simon_FPH
## Catagory: SQL Notes:
###
- Icon图标网站：https://www.flaticon.com/search/3?word=filter
- PowerBI Icon Name可用作表格中: https://radacad.com/power-bi-icon-names-for-conditional-formatting-using-dax
- 背景图片自定义生成网站：https://codioful.com/editor
  
###
<!-- placeholder -->



## Catagory: DAX Syntax and Notes:
1. Show Y Axis Constant Line only if the user selects a material ID - 特定情况下显示线
- Reorder Line = IF(
    HASONEVALUE('Material Base'[materialID]),
    CALCULATE(
    SUM('Material Base'[ReorderPoint]),
        ALLEXCEPT('Rolling Calendar','Rolling Calendar'[Date])
        ), BLANK()
        )
2. Similar to IfNull, return 0 if calculation is null.
- for example:
 - RowsCardCount = COALESCE(
        CALCULATE( ......
    ),0
    )


3. Return the max consumption for the past 12 months or your logic. 根据逻辑返回一个范围内的最大值
Max_Consumption_By_Month = 
MAXX(
    FILTER(
        SUMMARIZE(
            FILTER(
                'Movement Sum',
                'Movement Sum'[Date] >= TODAY() - 90 && 'Movement Sum'[Date] <= TODAY()
            ),
            'Movement Sum'[Month], 
            "Total Consumption", SUM('Movement Sum'[TotalConsumption])
        ),
        [Total Consumption] <> BLANK()
    ),
    [Total Consumption]
)

4. 验证slicer是不是被multi-selected 需要用count计数，不能只用isfiltered （使用isfiltered、countrows(values()) 同时进行判断slicer选中情况）
   例子：COUNTROWS(VALUES('DimCalendar'[dateFiscalYear])) > 1

5. 做parameter排序时，用rankx先给列表排虚拟号
   例子：Material Rank = 
    CALCULATE(
        RANKX(
            FILTER(
                ALL('Dim Material'[MaterialDescription]), 
                'Dim Material'[MaterialDescription] <> BLANK()
            ),
            [Total_Spend_inNZD_onlymaterial]
        )
    )
   然后在显示数值DAX中：Selected Year Material Spend = CALCULATE(
                        [Total_Spend_inNZD_onlymaterial], ALL('DimCalendar'[dateFiscalYear]),'DimCalendar'[dateFiscalYear] = VALUES('Year Selection'[Parameter])
                        , FILTER(VALUES('Dim Material'[MaterialDescription]), 
                        [Material Rank] <= SELECTEDVALUE('Ranking Parameter'[Parameter]) && 'Dim Material'[MaterialDescription] <> BLANK())
                    ) 将显示的rankx值和parameter进行对比即可
   
6. Calculate Ratio: (直接吧需要分类的类别放在calculate中进行筛选)
   Total_Percentage_by_StockType = 
        DIVIDE(
            [Total_Current_startofmonth],
            CALCULATE(
                [Total_Current_startofmonth],
                ALL(DimStockType[StockTypeDescription])
            ),
            0
        )

7. 有时候算average直接写会计算错误，例如，要算不同vendors的average，powerbi会把数据全部加起来然后算一个平均数，但是有时候这不是我们要的逻辑，需要分别算每个vendor的数，然后求平均。
   例supplier scorecard中：Average Operational Weighted 2 = 
        CALCULATE(
            AVERAGEX(Vendor, [Operational Weighted 2])
        )

8. 求两个或者多个data souce的交集和并集：
   交集：
       VAR CombinedUniqueValues = 
            COUNTROWS(
                EXCEPT (
                SELECTCOLUMNS(EDW_JIT_DTL_Closeddel, "Combined_Column", EDW_JIT_DTL_Closeddel[Total_JIT_JoinKey]),
                SELECTCOLUMNS(EDW_JIT_DTL, "Combined_Column", EDW_JIT_DTL[Total_JIT_JoinKey])
            ))
    并集：
           COUNTROWS(
            DISTINCT (
            UNION (
                SELECTCOLUMNS(EDW_JIT_DTL, "Combined_Column", EDW_JIT_DTL[Total_JIT_JoinKey]),
                SELECTCOLUMNS(EDW_JIT_DTL_Closeddel, "Combined_Column", EDW_JIT_DTL_Closeddel[Total_JIT_JoinKey])
            )))
       
9. 计数时间区间：
    DAYS_BETWEEN = COUNTROWS(
            DATESBETWEEN('Rolling Calendar'[dateFullDate],
            MIN('Rolling Calendar'[dateFullDate]),
            MAX('Rolling Calendar'[dateFullDate])
        ))

10. 每个visual的title灵活使用变化title，
    自己例子1：（标题中进行formating定义）
            Avg Card = 
            VAR AvgSpend = ROUND([Average Spend in past years], 2)
            VAR Card_string =
                CONCATENATE("Average Spend Past 5 Years: ", FORMAT(AvgSpend, "$#,##0.00"))
                
            return  
            IF(
                ISFILTERED('Dim Material'[MaterialID]), Card_string,"Select a Material to show Average Spend"
            )
    
    自己例子2（普通示例）：-这里注意和countrows同时使用，如果需要验证只有一个slicer被选中
        Order UoM Card = IF(
                ISFILTERED('Dim Material'[MaterialID]), VALUES(PIR[OrderUOM]), "Select Material"
            )

11. PowerBI Loopup DAX: - 简单版
    FiscalYear_lookup = 
    LOOKUPVALUE(
        'DimCalendar'[dateFiscalYear], 
        'DimCalendar'[dateFullDate], TODAY()
    )

12. Table 同一列，小于1显示小数，大于1显示整数：
    加入formating "0.##;(0.##);0.##" 到dynamic 格式中
