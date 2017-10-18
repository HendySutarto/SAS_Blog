# Interview Question

You are given this table:

![](https://i.imgur.com/g36wB06.png)

Note the gap between 7Mar15 and 10Mar15, also between 11Mar15 and 20Mar15, and between 20Mar15 and 31Mar15.

Further breaking down for filling the gap, this is the next step you want to achieve (before completing the fills) as below. Note the date has been filled using "Datemaster" field, while the original date still empty. 

![](https://i.imgur.com/ModWulT.png)

Eventually, all the row gaps are filled. The final table should look like this:

![](https://i.imgur.com/HhA3NLO.png)

# Solution

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
