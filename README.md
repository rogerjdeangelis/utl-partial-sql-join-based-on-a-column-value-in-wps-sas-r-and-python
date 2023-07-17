# utl-partial-sql-join-based-on-a-column-value-in-wps-sas-r-and-python
Partial sql join based on a column value in wps sas r and python 
    %let pgm=utl-partial-sql-join-based-on-a-column-value-in-wps-sas-r-and-python;

    Partial sql join based on a column value in wps sas r and python

    github
    https://tinyurl.com/yc4r4a7t
    https://github.com/rogerjdeangelis/utl-partial-sql-join-based-on-a-column-value-in-wps-sas-r-and-python

    I added SQL solutions for R, python, and wps.

    Stackoverflow/SAS
    https://tinyurl.com/3mjwbfpx
    https://stackoverflow.com/questions/75971683/a-logic-to-pick-the-values-in-num-to-fill-for-denom-from-2-columns

      1 wps/sas sql
      2 R sql
      3 Python sql

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    libname sd1 "d:/sd1";
    options validvarname=upcase;

    /*----  No need to split _NAME_. It just eliminatea subsequent spliting  ----*/
    /*----  Much less messy sql processing                                   ----*/
    /*----  I have removed columns that do not change from input to output   ----*/

    data sd1.have(drop=_name_ yn var);
    retain ltr varnum num;
    input _Name_ : $ 12. NUM;
    ltr=scan(_name_,1,'_');
    yn =scan(_name_,2,'_');
    var=scan(_name_,3,'_');
    varnum=input(substr(var,4),best.);
    cards4;
    _O_NO_VAR1  2
    _O_NO_VAR2  4
    _O_NO_VAR3  3
    _O_NO_VAR4  5
    _O_NO_VAR5  6
    _O_NO_VAR6  4
    _O_NO_VAR7  5
    _O_NO_VAR8  6
    _O_NO_VAR9  4
    _O_NO_VAR10 2
    _A_NO_VAR1  3
    _A_NO_VAR2  45
    _A_NO_VAR3  65
    _A_NO_VAR4  3
    _A_NO_VAR5  45
    _A_NO_VAR6  22
    _A_NO_VAR7  12
    _A_NO_VAR8  21
    _A_NO_VAR9  32
    _A_NO_VAR10 11
    _D_YES_VAR1 29
    _D_YES_VAR2 23
    _D_YES_VAR3 23
    _D_YES_VAR4 21
    _D_YES_VAR5 29
    _D_YES_VAR6 23
    _D_YES_VAR7 23
    _D_YES_VAR8 29
    _D_YES_VAR9 21
    _D_YES_VAR10 23
    ;;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                           |                                                                            */
    /*                                           |  RULES (create DENOM)                                                      */
    /*                                           |                                                                            */
    /*  SD1.HAVE total obs=30 17MAY2023:14:52:40 |  DENOM                                                                     */
    /*                                           |                                                                            */
    /*  Obs    LTR    VARNUM    NUM              |                                                                            */
    /*                                           |                                           case l.ltr                       */
    /*    1     O        1        2              |  10  Denom hardcode to 10                  when "O" then 10                */
    /*    2     O        2        4              |  10                                        when "A" then D.num             */
    /*    3     O        3        3              |  10                                       else .                           */
    /*    4     O        4        5              |  10                                       end as denom                     */
    /*    5     O        5        6              |  10                                                                        */
    /*    6     O        6        4              |  10                                                                        */
    /*    7     O        7        5              |  10                                                                        */
    /*    8     O        8        6              |  10                                                                        */
    /*    9     O        9        4              |  10                                                                        */
    /*   10     O       10        2              |  10                                                                        */
    /*                                           |                                                                            */
    /*   11     A        1        3              |  29  Denom use corresponding varnum values in D                            */
    /*   12     A        2       45              |  23                                                                        */
    /*   13     A        3       65              |  23                                                                        */
    /*   14     A        4        3              |  21                                                                        */
    /*   15     A        5       45              |  29                                                                        */
    /*   16     A        6       22              |  23                                                                        */
    /*   17     A        7       12              |  23                                                                        */
    /*   18     A        8       21              |  29                                                                        */
    /*   19     A        9       32              |  21                                                                        */
    /*   20     A       10       11              |  23                                                                        */
    /*                                           |                                                                            */
    /*   21     D        1       29              |   .   Left join nomatch                                                    */
    /*   22     D        2       23              |   .                                                                        */
    /*   23     D        3       23              |   .                                                                        */
    /*   24     D        4       21              |   .                                                                        */
    /*   25     D        5       29              |   .                                                                        */
    /*   26     D        6       23              |   .                                                                        */
    /*   27     D        7       23              |   .                                                                        */
    /*   28     D        8       29              |   .                                                                        */
    /*   29     D        9       21              |   .                                                                        */
    /*   30     D       10       23              |   .                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.WANT total obs=30 17MAY2023:15:08:23                                                                               */
    /*                                                                                                                        */
    /* Obs    LTR    VARNUM    NUM    DENOM                                                                                   */
    /*                                                                                                                        */
    /*   1     A        1        3      29                                                                                    */
    /*   2     A        2       45      23                                                                                    */
    /*   3     A        3       65      23                                                                                    */
    /*   4     A        4        3      21                                                                                    */
    /*   5     A        5       45      29                                                                                    */
    /*   6     A        6       22      23                                                                                    */
    /*   7     A        7       12      23                                                                                    */
    /*   8     A        8       21      29                                                                                    */
    /*   9     A        9       32      21                                                                                    */
    /*  10     A       10       11      23                                                                                    */
    /*  11     D        1       29       .                                                                                    */
    /*  12     D        2       23       .                                                                                    */
    /*  13     D        3       23       .                                                                                    */
    /*  14     D        4       21       .                                                                                    */
    /*  15     D        5       29       .                                                                                    */
    /*  16     D        6       23       .                                                                                    */
    /*  17     D        7       23       .                                                                                    */
    /*  18     D        8       29       .                                                                                    */
    /*  19     D        9       21       .                                                                                    */
    /*  20     D       10       23       .                                                                                    */
    /*  21     O        1        2      10                                                                                    */
    /*  22     O        2        4      10                                                                                    */
    /*  23     O        3        3      10                                                                                    */
    /*  24     O        4        5      10                                                                                    */
    /*  25     O        5        6      10                                                                                    */
    /*  26     O        6        4      10                                                                                    */
    /*  27     O        7        5      10                                                                                    */
    /*  28     O        8        6      10                                                                                    */
    /*  29     O        9        4      10                                                                                    */
    /*  30     O       10        2      10                                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*                         __                         _
    / | __      ___ __  ___   / /__  __ _ ___   ___  __ _| |
    | | \ \ /\ / / `_ \/ __| / / __|/ _` / __| / __|/ _` | |
    | |  \ V  V /| |_) \__ \/ /\__ \ (_| \__ \ \__ \ (_| | |
    |_|   \_/\_/ | .__/|___/_/ |___/\__,_|___/ |___/\__, |_|
                 |_|                                   |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('

    libname sd1 "d:/sd1";
    options validvarname=any;

    proc sql;

      create
         table sd1.want as
      select
         l.ltr
        ,l.varnum
        ,l.num
        ,case l.ltr
          when "O" then 10
          when "A" then r.num
         else .
         end as denom
      from
         sd1.have as l left join sd1.have as r
      on
               l.varnum = r.varnum
          and  l.ltr    = "A"
          and  r.ltr    = "D"
      order
          by l.ltr,l.varnum
    ;quit;

    proc print data=sd1.want;
    run;quit;

    ');

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    /*
     ___  __ _ ___
    / __|/ _` / __|
    \__ \ (_| \__ \
    |___/\__,_|___/

    */

    proc sql;

      create
         table want as
      select
         l.ltr
        ,l.varnum
        ,l.num
        ,case l.ltr
          when "O" then 10
          when "A" then r.num
         else .
         end as denom
      from
         sd1.have as l left join sd1.have as r
      on
               l.varnum = r.varnum
          and  l.ltr    = "A"
          and  r.ltr    = "D"
      order
          by l.ltr,l.varnum
    ;quit;

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.have r=have;
    submit;
    library(sqldf);
    want <- sqldf("
      select
         l.ltr
        ,l.varnum
        ,l.num
        ,case l.ltr
          when `O` then 10
          when `A` then r.num
         else NULL
         end as denom
      from
         have as l left join have as r
      on
               l.varnum = r.varnum
          and  l.ltr    = `A`
          and  r.ltr    = `D`
      order
          by l.ltr,l.varnum
    ");
    want;
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */


    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc python;
    export data=sd1.have python=have;
    submit;
     from os import path;
     import pandas as pd;
     import numpy as np;
     import pandas as pd;
     from pandasql import sqldf;
     mysql = lambda q: sqldf(q, globals());
     from pandasql import PandaSQL;
     pdsql = PandaSQL(persist=True);
     sqlite3conn = next(pdsql.conn.gen).connection.connection;
     sqlite3conn.enable_load_extension(True);
     sqlite3conn.load_extension(`c:/temp/libsqlitefunctions.dll`);
     mysql = lambda q: sqldf(q, globals());
     want = pdsql("""
      select
         l.ltr
        ,l.varnum
        ,l.num
        ,case trim(l.ltr)
          when `O` then 10
          when `A` then r.num
         else NULL
         end as denom
      from
         have as l left join have as r
      on
               l.varnum = r.varnum
          and  trim(l.ltr)    = `A`
          and  trim(r.ltr)    = `D`
      order
          by l.ltr,l.varnum
        """);
     print(want);
    endsubmit;
    import data=sd1.want python=want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
