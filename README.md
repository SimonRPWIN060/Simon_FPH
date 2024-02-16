# Simon_FPH

## Catagory: SQL Notes:
###
<!-- placeholder -->



## Catagory: DAX Syntax and Notes:
1. Show Y Axis 
- Reorder Line = IF(
    HASONEVALUE('Material Base'[materialID]),
    CALCULATE(
    SUM('Material Base'[ReorderPoint]),
        ALLEXCEPT('Rolling Calendar','Rolling Calendar'[Date])
        ), BLANK()
        )