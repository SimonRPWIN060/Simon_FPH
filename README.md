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
