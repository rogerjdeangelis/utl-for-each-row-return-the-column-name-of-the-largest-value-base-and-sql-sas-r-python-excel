# utl-for-each-row-return-the-column-name-of-the-largest-value-base-and-sql-sas-r-python-excel
For each row return the column name of the largest value base and sql sas r python excel
    %let pgm=utl-for-each-row-return-the-column-name-of-the-largest-value-base-and-sql-sas-r-python-excel;

    %stop_submission;

    For each row return the column name of the largest value base and sql sas r python excel;

    github
    https://tinyurl.com/4xf7az5d
    https://github.com/rogerjdeangelis/utl-for-each-row-return-the-column-name-of-the-largest-value-base-and-sql-sas-r-python-excel

    stackoverflow R
    https://tinyurl.com/38bmdwfy
    https://stackoverflow.com/questions/17735859/for-each-row-return-the-column-name-of-the-largest-value

         SOLUTIONS

            1 sas base
            2 sas sql w/o arrays
            3 sas sql with arrays
            4 r tidyverse language
              %>%, gather, group_by, slice, which.max
            5 r sql
            6 python sql

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    All the solutions below will identify one maximum per row.
    However they may not chose the same column name when mutiple columns have the same maxinum value.


    /**************************************************************************************************************************/
    /*                        |                                                        |                                      */
    /*       INPUT            |    PROCESS                                             |             OUTPUT                   */
    /*       =====            |    =======                                             |             ======                   */
    /*                        |                                                        |                                      */
    /*                        |                                                        |                                      */
    /*  X1 X2 X3 X4 X5        | 1 SAS DATASTEP                                         |  X1 X2 X3 X4 X5 MAX_XS VAR MAX_IDX   */
    /*                        | ==============                                         |                                      */
    /*  14 17 18 13 12        |                                                        |  14 17 18 13 12   18    X3   3       */
    /*  17 11 17 17 18        |  data want;                                            |  17 11 17 17 18   18    X5   5       */
    /*  15 12 12 14 15        |    set sd1.have;                                       |  15 12 12 14 15   15    X1   1       */
    /*  19 14 12 12 12        |    array xs[*] x:;                                     |  19 14 12 12 12   19    X1   1       */
    /*  13 16 14 18 16        |    max_xs  = max(of xs[*]);                            |  13 16 14 18 16   18    X4   4       */
    /*  14 12 11 20 12        |    max_idx =                                           |  14 12 11 20 12   20    X4   4       */
    /*  16 10 18 20 14        |       whichn(max_xs, of xs[*]);                        |  16 10 18 20 14   20    X4   4       */
    /*  12 14 11 17 11        |    var=vname(xs[max_idx]);                             |  12 14 11 17 11   17    X4   4       */
    /*                        |  run;quit;                                             |                                      */
    /*                        |                                                        |                                      */
    /*                        |--------------------------------------------------------|--------------------------------------*/
    /*                        |                                                        |                                      */
    /*  options               | 2 SAS SQL WITHOUT ARRAYS                               |                                      */
    /*   validvarname=upcase; | ========================                               |        X2 X3 X4 X5 NAM MAXVAL        */
    /*  libname sd1 "d:/sd1"; |                                                        |                                      */
    /*  data sd1.have;        |  proc sql;                                             |  1  14 17 18 13 12 x3   18           */
    /*    input row x1-x5 @@; |      create                                            |  2  17 11 17 17 18 x5   18           */
    /*  cards4;               |        table want as                                   |  3  15 12 12 14 15 x4   15           */
    /*  1 14 17 18 13 12      |      select                                            |  4  19 14 12 12 12 x4   19           */
    /*  2 17 11 17 17 18      |          l.row                                         |  5  13 16 14 18 16 x4   18           */
    /*  3 15 12 12 14 15      |         ,l.x1                                          |  6  14 12 11 20 12 x4   20           */
    /*  4 19 14 12 12 12      |         ,l.x2                                          |  7  16 10 18 20 14 x1   20           */
    /*  5 13 16 14 18 16      |         ,l.x3                                          |  8  12 14 11 17 11 x3   17           */
    /*  6 14 12 11 20 12      |         ,l.x4                                          |                                      */
    /*  7 16 10 18 20 14      |         ,l.x5                                          |                                      */
    /*  8 12 14 11 17 11      |         ,r.nam                                         |                                      */
    /*  ;;;;                  |         ,r.maxVal                                      |                                      */
    /*  run;quit;             |      from                                              |                                      */
    /*                        |        sd1.have as l                                   |                                      */
    /*                        |     left join                                          |                                      */
    /*                        |        (select                                         |                                      */
    /*                        |           row                                          |                                      */
    /*                        |          ,nam                                          |                                      */
    /*                        |          ,max(val) as maxVal                           |                                      */
    /*                        |         from                                           |                                      */
    /*                        |         (                                              |                                      */
    /*                        |         select row, 'x1' as nam, x1 as val             |                                      */
    /*                        |           from sd1.have union all                      |                                      */
    /*                        |         select row, 'x2' as nam, x2 as val             |                                      */
    /*                        |           from sd1.have union all                      |                                      */
    /*                        |         select row, 'x3' as nam, x3 as val             |                                      */
    /*                        |           from sd1.have union all                      |                                      */
    /*                        |         select row, 'x4' as nam, x4 as val             |                                      */
    /*                        |           from sd1.have union all                      |                                      */
    /*                        |         select row, 'x5' as nam, x5 as val             |                                      */
    /*                        |           from sd1.have)                               |                                      */
    /*                        |         group                                          |                                      */
    /*                        |           by row                                       |                                      */
    /*                        |         ) as r                                         |                                      */
    /*                        |      on                                                |                                      */
    /*                        |        l.row = r.row                                   |                                      */
    /*                        |  ;quit;                                                |                                      */
    /*                        |                                                        |
    /*                        |--------------------------------------------------------|--------------------------------------*/
    /*                        |                                                        |                                      */
    /*                        |                                                        |                                      */
    /*                        | 3 SAS SQL WITH SQL ARRAYS                              |                                      */
    /*                        | =========================                              |   ROW X1 X2 X3 X4 X5 NAM MAXVAL      */
    /*                        |                                                        |                                      */
    /*                        | If you highlight the code below and type debug         |    1  14 17 18 13 12 x3    18        */
    /*                        | on the classic editor command line, mprint             |    2  17 11 17 17 18 x5    18        */
    /*                        | will run and the resolved code will be in the log.     |    3  15 12 12 14 15 x4    15        */
    /*                        | Just cut and paste the code in sql.                    |    4  19 14 12 12 12 x4    19        */
    /*                        |                                                        |    5  13 16 14 18 16 x4    18        */
    /*                        | %array(_idx,values=1-5);                               |    6  14 12 11 20 12 x4    20        */
    /*                        | proc sql;                                              |    7  16 10 18 20 14 x1    20        */
    /*                        |    create                                              |    8  12 14 11 17 11 x3    17        */
    /*                        |       table want as                                    |                                      */
    /*                        |     select                                             |                                      */
    /*                        |         l.row                                          |                                      */
    /*                        |        ,%utl_varlist(sd1.have                          |                                      */
    /*                        |           ,keep=x:                                     |                                      */
    /*                        |           ,od=%str(,))                                 |                                      */
    /*                        |        ,r.nam                                          |                                      */
    /*                        |        ,r.maxVal                                       |                                      */
    /*                        |     from                                               |                                      */
    /*                        |       sd1.have as l                                    |                                      */
    /*                        |    left join                                           |                                      */
    /*                        |       (select                                          |                                      */
    /*                        |          row                                           |                                      */
    /*                        |         ,nam                                           |                                      */
    /*                        |         ,max(val) as maxVal                            |                                      */
    /*                        |        from                                            |                                      */
    /*                        |          (                                             |                                      */
    /*                        |          %do_over(_idx,phrase=%str(                    |                                      */
    /*                        |          select                                        |                                      */
    /*                        |            row                                         |                                      */
    /*                        |           ,"x?" as nam                                 |                                      */
    /*                        |           ,x? as val from sd1.have)                    |                                      */
    /*                        |           ,between=union all)                          |                                      */
    /*                        |          )                                             |                                      */
    /*                        |        group                                           |                                      */
    /*                        |          by row                                        |                                      */
    /*                        |        ) as r                                          |                                      */
    /*                        |     on                                                 |                                      */
    /*                        |       l.row = r.row                                    |                                      */
    /*                        | ;quit;                                                 |                                      */
    /*                        |                                                        |                                      */
    /*                        |--------------------------------------------------------|--------------------------------------*/
    /*                        |                                                        |                                      */
    /*                        |                                                        | id    var     val                    */
    /*                        | 4 R TIDYVERSE LANGUAGE                                 |                                      */
    /*                        | ======================                                 | 1     X3       18                    */
    /*                        |                                                        | 2     X5       18                    */
    /*                        | %utl_rbeginx;                                          | 3     X1       15                    */
    /*                        | parmcards4;                                            | 4     X1       19                    */
    /*                        | library(tidyverse)                                     | 5     X4       18                    */
    /*                        | library(haven)                                         | 6     X4       20                    */
    /*                        | source("c:/oto/fn_tosas9x.R")                          | 7     X4       20                    */
    /*                        | have<-read_sas("d:/sd1/have.sas7bdat")                 | 8     X4       17                    */
    /*                        | print(have)                                            |                                      */
    /*                        | want<-have %>%                                         |                                      */
    /*                        |   gather(var, val, X1:X5) %>%                          |                                      */
    /*                        |   group_by(ROW) %>%                                    |                                      */
    /*                        |   slice(which.max(val))                                |                                      */
    /*                        | want                                                   |                                      */
    /*                        | fn_tosas9x(                                            |                                      */
    /*                        |       inp    = want                                    |                                      */
    /*                        |      ,outlib ="d:/sd1/"                                |                                      */
    /*                        |      ,outdsn ="want"                                   |                                      */
    /*                        |      )                                                 |                                      */
    /*                        | ;;;;                                                   |                                      */
    /*                        | %utl_rendx;                                            |                                      */
    /*                        |                                                        |                                      */
    /*                        | proc print data=sd1.have;                              |                                      */
    /*                        | run;quit;                                              |                                      */
    /*                        |                                                        |                                      */
    /*                        |                                                        |                                      */
    /*                        |--------------------------------------------------------|--------------------------------------*/
    /*                        |                                                        |                                      */
    /*                        |                                                        | ROW X1 X2 X3 X4 X5 NA MAXVAL         */
    /*                        | 5 R SQL                                                |                                      */
    /*                        | =======                                                |  1  14 17 18 13 12 x3   18           */
    /*                        |                                                        |  2  17 11 17 17 18 x5   18           */
    /*                        | %utl_rbeginx;                                          |  3  15 12 12 14 15 x4   15           */
    /*                        | parmcards4;                                            |  4  19 14 12 12 12 x4   19           */
    /*                        | library(sqldf)                                         |  5  13 16 14 18 16 x4   18           */
    /*                        | library(haven)                                         |  6  14 12 11 20 12 x4   20           */
    /*                        | source("c:/oto/fn_tosas9x.R")                          |  7  16 10 18 20 14 x1   20           */
    /*                        | have<-read_sas("d:/sd1/have.sas7bdat")                 |  8  12 14 11 17 11 x3   17           */
    /*                        | print(have)                                            |                                      */
    /*                        | want<-sqldf("                                          |                                      */
    /*                        |   select                                               |                                      */
    /*                        |       l.row                                            |                                      */
    /*                        |      ,l.x1                                             |                                      */
    /*                        |      ,l.x2                                             |                                      */
    /*                        |      ,l.x3                                             |                                      */
    /*                        |      ,l.x4                                             |                                      */
    /*                        |      ,l.x5                                             |                                      */
    /*                        |      ,r.nam                                            |                                      */
    /*                        |      ,r.maxVal                                         |                                      */
    /*                        |   from                                                 |                                      */
    /*                        |     have as l                                          |                                      */
    /*                        |   left join                                            |                                      */
    /*                        |     (select                                            |                                      */
    /*                        |        row                                             |                                      */
    /*                        |       ,nam                                             |                                      */
    /*                        |       ,max(val) as maxVal                              |                                      */
    /*                        |      from                                              |                                      */
    /*                        |      (                                                 |                                      */
    /*                        |      select row, 'x1' as nam, x1 as val                |                                      */
    /*                        |        from have union all                             |                                      */
    /*                        |      select row, 'x2' as nam, x2 as val                |                                      */
    /*                        |        from have union all                             |                                      */
    /*                        |      select row, 'x3' as nam, x3 as val                |                                      */
    /*                        |        from have union all                             |                                      */
    /*                        |      select row, 'x4' as nam, x4 as val                |                                      */
    /*                        |        from have union all                             |                                      */
    /*                        |      select row, 'x5' as nam, x5 as val                |                                      */
    /*                        |        from have)                                      |                                      */
    /*                        |      group                                             |                                      */
    /*                        |        by row                                          |                                      */
    /*                        |      ) as r                                            |                                      */
    /*                        |   on                                                   |                                      */
    /*                        |     l.row = r.row                                      |                                      */
    /*                        |   ")                                                   |                                      */
    /*                        | want                                                   |                                      */
    /*                        | fn_tosas9x(                                            |                                      */
    /*                        |       inp    = want                                    |                                      */
    /*                        |      ,outlib ="d:/sd1/"                                |                                      */
    /*                        |      ,outdsn ="want"                                   |                                      */
    /*                        |      )                                                 |                                      */
    /*                        | ;;;;                                                   |                                      */
    /*                        | %utl_rendx;                                            |                                      */
    /*                        |                                                        |                                      */
    /*                        |                                                        |                                      */
    /*                        |--------------------------------------------------------|--------------------------------------*/
    /*                        |                                                        |                                      */
    /*                        |                                                        |                                      */
    /*                        | 6 PYTHON SQL                                           |  ROW  X1  X2  X3  X4  X5 NAM  MAXVAL */
    /*                        | ============                                           |                                      */
    /*                        |                                                        | 0  1  14  17  18  13  12  x3    18   */
    /*                        | proc datasets lib=sd1 nolist nodetails;                | 1  2  17  11  17  17  18  x5    18   */
    /*                        |  delete pywant;                                        | 2  3  15  12  12  14  15  x1    15   */
    /*                        | run;quit;                                              | 3  4  19  14  12  12  12  x1    19   */
    /*                        |                                                        | 4  5  13  16  14  18  16  x4    18   */
    /*                        | %utl_pybeginx;                                         | 5  6  14  12  11  20  12  x4    20   */
    /*                        | parmcards4;                                            | 6  7  16  10  18  20  14  x4    20   */
    /*                        | exec(open('c:/oto/fn_python.py').read())               | 7  8  12  14  11  17  11  x4    17   */
    /*                        | have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat')   |                                      */
    /*                        | want=pdsql('''                                         |                                      */
    /*                        |      select                                  \         |                                      */
    /*                        |          l.row                               \         |                                      */
    /*                        |         ,l.x1                                \         |                                      */
    /*                        |         ,l.x2                                \         |                                      */
    /*                        |         ,l.x3                                \         |                                      */
    /*                        |         ,l.x4                                \         |                                      */
    /*                        |         ,l.x5                                \         |                                      */
    /*                        |         ,r.nam                               \         |                                      */
    /*                        |         ,r.maxVal                            \         |                                      */
    /*                        |      from                                    \         |                                      */
    /*                        |        have as l                             \         |                                      */
    /*                        |      left join                               \         |                                      */
    /*                        |        (select                               \         |                                      */
    /*                        |           row                                \         |                                      */
    /*                        |          ,nam                                \         |                                      */
    /*                        |          ,max(val) as maxVal                 \         |                                      */
    /*                        |         from                                 \         |                                      */
    /*                        |         (                                    \         |                                      */
    /*                        |         select row, 'x1' as nam, x1 as val   \         |                                      */
    /*                        |           from have union all                \         |                                      */
    /*                        |         select row, 'x2' as nam, x2 as val   \         |                                      */
    /*                        |           from have union all                \         |                                      */
    /*                        |         select row, 'x3' as nam, x3 as val   \         |                                      */
    /*                        |           from have union all                \         |                                      */
    /*                        |         select row, 'x4' as nam, x4 as val   \         |                                      */
    /*                        |           from have union all                \         |                                      */
    /*                        |         select row, 'x5' as nam, x5 as val   \         |                                      */
    /*                        |           from have)                         \         |                                      */
    /*                        |         group                                \         |                                      */
    /*                        |           by row                             \         |                                      */
    /*                        |         ) as r                               \         |                                      */
    /*                        |      on                                      \         |                                      */
    /*                        |        l.row = r.row                         \         |                                      */
    /*                        |    ''');                                               |                                      */
    /*                        | print(want);                                           |                                      */
    /*                        | fn_tosas9x(want,outlib='d:/sd1/' \                     |                                      */
    /*                        |   ,outdsn='pywant',timeest=3);                         |                                      */
    /*                        | ;;;;                                                   |                                      */
    /*                        | %utl_pyendx;                                           |                                      */
    /*                        |                                                        |                                      */
    /*                        | proc print data=sd1.pywant;                            |                                      */
    /*                        | run;quit;                                              |                                      */
    /*                        |                                                        |                                      */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    options
     validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      input row x1-x5 @@;
    cards4;
    1 14 17 18 13 12
    2 17 11 17 17 18
    3 15 12 12 14 15
    4 19 14 12 12 12
    5 13 16 14 18 16
    6 14 12 11 20 12
    7 16 10 18 20 14
    8 12 14  1 17 11
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  ROW    X1    X2    X3    X4    X5                                                                                     */
    /*                                                                                                                        */
    /*   1     14    17    18    13    12                                                                                     */
    /*   2     17    11    17    17    18                                                                                     */
    /*   3     15    12    12    14    15                                                                                     */
    /*   4     19    14    12    12    12                                                                                     */
    /*   5     13    16    14    18    16                                                                                     */
    /*   6     14    12    11    20    12                                                                                     */
    /*   7     16    10    18    20    14                                                                                     */
    /*   8     12    14    11    17    11                                                                                     */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                   _
    / |  ___  __ _ ___  | |__   __ _ ___  ___
    | | / __|/ _` / __| | `_ \ / _` / __|/ _ \
    | | \__ \ (_| \__ \ | |_) | (_| \__ \  __/
    |_| |___/\__,_|___/ |_.__/ \__,_|___/\___|

    */

    data want;
      set sd1.have;
      array xs[*] x:;
      max_xs  = max(of xs[*]);
      max_idx =
         whichn(max_xs, of xs[*]);
      var=vname(xs[max_idx]);
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  ROW    X1    X2    X3    X4    X5    MAX_XS    MAX_IDX    VAR                                                         */
    /*                                                                                                                        */
    /*   1     14    17    18    13    12      18         3       X3                                                          */
    /*   2     17    11    17    17    18      18         5       X5                                                          */
    /*   3     15    12    12    14    15      15         1       X1                                                          */
    /*   4     19    14    12    12    12      19         1       X1                                                          */
    /*   5     13    16    14    18    16      18         4       X4                                                          */
    /*   6     14    12    11    20    12      20         4       X4                                                          */
    /*   7     16    10    18    20    14      20         4       X4                                                          */
    /*   8     12    14    11    17    11      17         4       X4                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                              _             __
    |___ \   ___  __ _ ___   ___  __ _| | __      __/ /__    __ _ _ __ _ __ __ _ _   _ ___
      __) | / __|/ _` / __| / __|/ _` | | \ \ /\ / / / _ \  / _` | `__| `__/ _` | | | / __|
     / __/  \__ \ (_| \__ \ \__ \ (_| | |  \ V  V / / (_) || (_| | |  | | | (_| | |_| \__ \
    |_____| |___/\__,_|___/ |___/\__, |_|   \_/\_/_/ \___/  \__,_|_|  |_|  \__,_|\__, |___/
                                    |_|                                          |___/
    */

    proc sql;
        create
          table want as
        select
            l.row
           ,l.x1
           ,l.x2
           ,l.x3
           ,l.x4
           ,l.x5
           ,r.nam
           ,r.maxVal
        from
          sd1.have as l
       left join
          (select
             row
            ,nam
            ,max(val) as maxVal
           from
           (
           select row, 'x1' as nam, x1 as val
             from sd1.have union all
           select row, 'x2' as nam, x2 as val
             from sd1.have union all
           select row, 'x3' as nam, x3 as val
             from sd1.have union all
           select row, 'x4' as nam, x4 as val
             from sd1.have union all
           select row, 'x5' as nam, x5 as val
             from sd1.have)
           group
             by row
           ) as r
        on
          l.row = r.row
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* ROW    X1    X2    X3    X4    X5    MAX_XS    MAX_IDX    VAR                                                          */
    /*                                                                                                                        */
    /*  1     14    17    18    13    12      18         3       X3                                                           */
    /*  2     17    11    17    17    18      18         5       X5                                                           */
    /*  3     15    12    12    14    15      15         1       X1                                                           */
    /*  4     19    14    12    12    12      19         1       X1                                                           */
    /*  5     13    16    14    18    16      18         4       X4                                                           */
    /*  6     14    12    11    20    12      20         4       X4                                                           */
    /*  7     16    10    18    20    14      20         4       X4                                                           */
    /*  8     12    14    11    17    11      17         4       X4                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                             _            _ _   _
    |___ /   ___  __ _ ___   ___  __ _| | __      _(_) |_| |__     __ _ _ __ _ __ __ _ _   _ ___
      |_ \  / __|/ _` / __| / __|/ _` | | \ \ /\ / / | __| `_ \   / _` | `__| `__/ _` | | | / __|
     ___) | \__ \ (_| \__ \ \__ \ (_| | |  \ V  V /| | |_| | | | | (_| | |  | | | (_| | |_| \__ \
    |____/  |___/\__,_|___/ |___/\__, |_|   \_/\_/ |_|\__|_| |_|  \__,_|_|  |_|  \__,_|\__, |___/
                                    |_|                                                |___/

    */
    %array(_idx,values=1-5);

    proc sql;
       create
          table want as
        select
            l.row
           ,%utl_varlist(sd1.have
              ,keep=x:
              ,od=%str(,))
           ,r.nam
           ,r.maxVal
        from
          sd1.have as l
       left join
          (select
             row
            ,nam
            ,max(val) as maxVal
           from
             (
             %do_over(_idx,phrase=%str(
             select
               row
              ,"x?" as nam
              ,x? as val from sd1.have)
              ,between=union all)
             )
           group
             by row
           ) as r
        on
          l.row = r.row
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* ROW    X1    X2    X3    X4    X5    NAM    MAXVA                                                                      */
    /*                                                                                                                        */
    /*  1     14    17    18    13    12    x3       18                                                                       */
    /*  2     17    11    17    17    18    x5       18                                                                       */
    /*  3     15    12    12    14    15    x4       15                                                                       */
    /*  4     19    14    12    12    12    x4       19                                                                       */
    /*  5     13    16    14    18    16    x4       18                                                                       */
    /*  6     14    12    11    20    12    x4       20                                                                       */
    /*  7     16    10    18    20    14    x1       20                                                                       */
    /*  8     12    14    11    17    11    x3       17                                                                       */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _            _   _     _                                _
    | || |    _ __  | |_(_) __| |_   ___   _____ _ __ ___  ___ | | __ _ _ __   __ _ _   _  __ _  __ _  ___
    | || |_  | `__| | __| |/ _` | | | \ \ / / _ \ `__/ __|/ _ \| |/ _` | `_ \ / _` | | | |/ _` |/ _` |/ _ \
    |__   _| | |    | |_| | (_| | |_| |\ V /  __/ |  \__ \  __/| | (_| | | | | (_| | |_| | (_| | (_| |  __/
       |_|   |_|     \__|_|\__,_|\__, | \_/ \___|_|  |___/\___||_|\__,_|_| |_|\__, |\__,_|\__,_|\__, |\___|
                                 |___/                                        |___/             |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(tidyverse)
    library(haven)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-have %>%
      gather(var, val, X1:X5) %>%
      group_by(ROW) %>%
      slice(which.max(val))
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                      |                                                                                                 */
    /*  R                   |  SAS                                                                                            */
    /*                      |                                                                                                 */
    /*     ROW VAR     VAL  |  ROWNAMES    ROW    X1    X2    X3    X4    X5    NAM    MAXVA                                  */
    /*   <dbl> <chr> <dbl>  |                                                                                                 */
    /*       1 X3       18  |      1        1     14    17    18    13    12    x3       18                                   */
    /*       2 X5       18  |      2        2     17    11    17    17    18    x5       18                                   */
    /*       3 X1       15  |      3        3     15    12    12    14    15    x1       15                                   */
    /*       4 X1       19  |      4        4     19    14    12    12    12    x1       19                                   */
    /*       5 X4       18  |      5        5     13    16    14    18    16    x4       18                                   */
    /*       6 X4       20  |      6        6     14    12    11    20    12    x4       20                                   */
    /*       7 X4       20  |      7        7     16    10    18    20    14    x4       20                                   */
    /*       8 X4       17  |      8        8     12    14    11    17    11    x4       17                                   */
    /*                      |                                                                                                 */
    /**************************************************************************************************************************/

    /*___                     _
    | ___|   _ __   ___  __ _| |
    |___ \  | `__| / __|/ _` | |
     ___) | | |    \__ \ (_| | |
    |____/  |_|    |___/\__, |_|
                           |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(sqldf)
    library(haven)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf("
      select
          l.row
         ,l.x1
         ,l.x2
         ,l.x3
         ,l.x4
         ,l.x5
         ,r.nam
         ,r.maxVal
      from
        have as l
      left join
        (select
           row
          ,nam
          ,max(val) as maxVal
         from
         (
         select row, 'x1' as nam, x1 as val
           from have union all
         select row, 'x2' as nam, x2 as val
           from have union all
         select row, 'x3' as nam, x3 as val
           from have union all
         select row, 'x4' as nam, x4 as val
           from have union all
         select row, 'x5' as nam, x5 as val
           from have)
         group
           by row
         ) as r
      on
        l.row = r.row
      ")
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.have;
    run;quit;

    /**************************************************************************************************************************/
    /*                                |                                                                                       */
    /* R                              |  SAS                                                                                  */
    /*                                |                                                                                       */
    /* ROW X1 X2 X3 X4 X5 NAM MAXVAL  |  ROWNAMES    ROW    X1    X2    X3    X4    X5    NAM    MAXVAL                       */
    /*                                |                                                                                       */
    /*   1 14 17 18 13 12  x3     18  |      1        1     14    17    18    13    12    x3       18                         */
    /*   2 17 11 17 17 18  x5     18  |      2        2     17    11    17    17    18    x5       18                         */
    /*   3 15 12 12 14 15  x1     15  |      3        3     15    12    12    14    15    x1       15                         */
    /*   4 19 14 12 12 12  x1     19  |      4        4     19    14    12    12    12    x1       19                         */
    /*   5 13 16 14 18 16  x4     18  |      5        5     13    16    14    18    16    x4       18                         */
    /*   6 14 12 11 20 12  x4     20  |      6        6     14    12    11    20    12    x4       20                         */
    /*   7 16 10 18 20 14  x4     20  |      7        7     16    10    18    20    14    x4       20                         */
    /*   8 12 14 11 17 11  x4     17  |      8        8     12    14    11    17    11    x4       17                         */
    /*                                |                                                                                       */
    /**************************************************************************************************************************/

    /*__                _   _                             _
     / /_   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    | `_ \ | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
    | (_) || |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
     \___/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
    |_|           |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read())
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''
         select                                  \
             l.row                               \
            ,l.x1                                \
            ,l.x2                                \
            ,l.x3                                \
            ,l.x4                                \
            ,l.x5                                \
            ,r.nam                               \
            ,r.maxVal                            \
         from                                    \
           have as l                             \
         left join                               \
           (select                               \
              row                                \
             ,nam                                \
             ,max(val) as maxVal                 \
            from                                 \
            (                                    \
            select row, 'x1' as nam, x1 as val   \
              from have union all                \
            select row, 'x2' as nam, x2 as val   \
              from have union all                \
            select row, 'x3' as nam, x3 as val   \
              from have union all                \
            select row, 'x4' as nam, x4 as val   \
              from have union all                \
            select row, 'x5' as nam, x5 as val   \
              from have)                         \
            group                                \
              by row                             \
            ) as r                               \
         on                                      \
           l.row = r.row                         \
       ''');
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/' \
      ,outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                   |                                                                    */
    /* PYTHON                                            |                                                                    */
    /*                                                   |                                                                    */
    /*    ROW    X1    X2    X3    X4    X5 NAM  MAXVAL  | ROW    X1    X2    X3    X4    X5    NAM    MAXVAL                 */
    /*                                                   |                                                                    */
    /* 0  1.0  14.0  17.0  18.0  13.0  12.0  x3    18.0  |  1     14    17    18    13    12    x3       18                   */
    /* 1  2.0  17.0  11.0  17.0  17.0  18.0  x5    18.0  |  2     17    11    17    17    18    x5       18                   */
    /* 2  3.0  15.0  12.0  12.0  14.0  15.0  x1    15.0  |  3     15    12    12    14    15    x1       15                   */
    /* 3  4.0  19.0  14.0  12.0  12.0  12.0  x1    19.0  |  4     19    14    12    12    12    x1       19                   */
    /* 4  5.0  13.0  16.0  14.0  18.0  16.0  x4    18.0  |  5     13    16    14    18    16    x4       18                   */
    /* 5  6.0  14.0  12.0  11.0  20.0  12.0  x4    20.0  |  6     14    12    11    20    12    x4       20                   */
    /* 6  7.0  16.0  10.0  18.0  20.0  14.0  x4    20.0  |  7     16    10    18    20    14    x4       20                   */
    /* 7  8.0  12.0  14.0   1.0  17.0  11.0  x4    17.0  |  8     12    14     1    17    11    x4       17                   */
    /*                                                   |                                                                    */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
