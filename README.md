# SAS_Blog

```sas
data    sourcedata2 ;
    infile datalines ;
    informat SourceCURRENCY  $3. TargetCurrency  $3. Date date7.   Rate 6.4 ;
    input SourceCURRENCY    TargetCurrency  Date    Rate;
format Date date7. ;
datalines;
AUD	USD	06Mar15	0.7125
AUD	USD	07Mar15	0.7135
AUD	USD	10Mar15	0.7195
AUD	USD	11Mar15	0.7185
AUD	USD	20Mar15	0.7375
run;





/* Create Date Master  */
data  datemaster ;
  format    datemaster date9. ;
  informat  datemaster date9. ;
  do datemaster = '6MAR2015'd to '31MAR2015'd; 
      output;
  end;
run;

proc sql;
create table table_result as 
select
                a.datemaster
            ,   b.*
from            datemaster a
left join       sourcedata b
            on  a.datemaster = b.date
order by        a.datemaster
;
quit;

data    table_result_2a  ;
    set table_result    ;

    by datemaster ;

    retain 
        retained_Rate
        retained_SourceCurrency
        retained_TargetCurrency
        retained_Date
        ;


    if Rate ne . then do;
        retained_Rate           = Rate            ;            
        retained_SourceCurrency = SourceCurrency  ;
        retained_TargetCurrency = TargetCurrency  ;
        retained_Date           = Date            ;
    end;
    else do;
         Rate           = retained_Rate           ;            
         SourceCurrency = retained_SourceCurrency ;
         TargetCurrency = retained_TargetCurrency ;
         Date           = retained_Date + 1       ;
         retained_Date  = Date ;
    end;

    drop
        retained_Rate
        retained_SourceCurrency
        retained_TargetCurrency
        retained_Date
        ;
run;
```
