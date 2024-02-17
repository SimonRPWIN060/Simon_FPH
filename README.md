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
        CALCULATE(
        COUNTROWS(
            FILTER(
                VALUES('Material Base'[MaterialPlant]),
                'Measure Table'[PreviousY Daily Consumption] <> 0 &&
                (
                    [CurrentDaySOH2] + [OpenPOQty] < 'MRP Forecast'[ForecastReq] ||
                    [Days Cover] < [Planned Delivery Days] ||
                    [CurrentDaySOH2] + [OpenPOQty] < calculate(SUM('Material Base'[ReorderPoint]))
                )
            ) 
        )
    ),0
    )