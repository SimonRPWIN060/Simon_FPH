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
2. 