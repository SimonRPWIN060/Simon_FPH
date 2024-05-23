# Simon_FPH

## Catagory: SQL Notes:
###
<!-- placeholder -->



## Catagory: DAX Syntax and Notes:
1. Show Y Axis Constant Line only if the user selects a material ID
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


3. Return the max consumption for the past 12 months or your logic.
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

4. 验证slicer是不是被multi-selected 需要用count计数，不能只用isfiltered
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
   

