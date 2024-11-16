# utl-summarize-by-mutiple-nested-groups-using-sql-in-sas-r-and-python-multi-language
Summarize by mutiple nested groups using sql in sas r and python 
    %let pgm=utl-summarize-by-mutiple-nested-groups-using-sql-in-sas-r-and-python-multi-language;

    %stop_submission;

    Summarize by mutiple nested groups using sql in sas r and python

         SOLUTIONS

            1 sas sql
            2 r dql
            3 python sql
            4 r dplyr syntax/language
              Not run

              count(have, Date, Compound
                   ,wt = Compound_mg) |>
               mutate(perc = n / sum(n)
                   ,.by = Date)
              left_join(have
                   ,count(Df, Date
                   , Compound, wt = Compound_mg) |>
              mutate(perc = n / sum(n), .by = Date))


    github
    https://tinyurl.com/4b3cy54f
    https://github.com/rogerjdeangelis/utl-summarize-by-mutiple-nested-groups-using-sql-in-sas-r-and-python-multi-language

    github
    https://tinyurl.com/3xpxfawk
    https://stackoverflow.com/questions/79189763/can-i-calculate-the-percentage-of-a-value-and-summarize-this-information-over-ev

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /***************************************************************************************************************************/
    /*                               |                                         |                                               */
    /*          INPUT                |              PROCESS                    |              OUTPUT                           */
    /*                               |                                         |                                               */
    /* DATA SORTED FOR DOCUMENTATION | DATA SORTED FOR DOCUMENTATION           | RATE DATE TREE ROCK WGTMG WGTCNT WGTPCT       */
    /* SOLUTION WORKS  UNSORTED DATA | SOLUTION WORKS WITH UNSORTED DATA       |                                               */
    /*                               | RATE TREE NOT SHOWN BUT                 |   1  13.1  a   C21     5   18    0.45 **      */
    /*                               |                                         |   2  13.1  b   C21     4   18    0.45         */
    /* RATE DATE TREE  ROCK WGTMG    |  DATE  ROCK WGTMG  WGTCNT    PERCENT    |   3  13.1  c   C21     9   18 *  0.45         */
    /*                               |                            -            |   4  20.2  a   C21     6   21    0.53         */
    /*   1  13.1  a    C21     5     |  13.1  C21   5 sum         |            |   5  20.2  b   C21     5   21    0.53         */
    /*   2  13.1  b    C21     4     |  13.1  C21   4 by date rock|            |   6  20.2  c   C21    10   21    0.53         */
    /*   3  13.1  c    C21     9     |  13.1  C21   9 5+4+9=18    | 18/40=.45  |   7  13.1  a   C23     6   22    0.55 ***     */
    /*                               |                      ==    |            |   8  13.1  b   C23     6   22    0.55         */
    /*   7  13.1  a    C23     6     |  13.1  C23   6 sum         |            |   9  13.1  c   C23    10   22    0.55         */
    /*   8  13.1  b    C23     6     |  13.1  C23   6 by date rock|            |  10  20.2  a   C23     5   18    0.46         */
    /*   9  13.1  c    C23    10     |  13.1  C23  10 6+6+10=22   | 18/40=.55  |  11  20.2  b   C23     4   18    0.46         */
    /*                               |                       ==   - 18+22=40   |  12  20.2  c   C23     9   18    0.46         */
    /*                               |                                         |                                               */
    /*  10  20.2  a    C23     5     |  20.2  C23   5                          |                                               */
    /*  11  20.2  b    C23     4     |  20.2  C23   4                          | DATE  ROCK WGTMG  WGTCNT       PERCENT        */
    /*  12  20.2  c    C23     9     |  20.2  C23   9                          |                              -                */
    /*   4  20.2  a    C21     6     |  20.2  C21   6                          | 13.1  C21   5 sum            |                */
    /*   5  20.2  b    C21     5     |  20.2  C21   5                          | 13.1  C21   4 by date rock   |                */
    /*   6  20.2  c    C21    10     |  20.2  C21  10                          | 13.1  C21   9 5+4+9=18 *     | 18/40=.45 **   */
    /*                               |                                         |                     ==       |                */
    /*                               | ----------------------------------------| 13.1  C23   6 sum            |                */
    /*                               |                                         | 13.1  C23   6 by date rock   |                */
    /*                               |  SAS SQL RXPLANATION                    | 13.1  C23  10 6+6+10=22      | 22/40=0.55 *** */
    /*                               |  -------------------                    |                      ==      - 18+22=40       */
    /*                               |                                         |                                               */
    /*                               |  I know this seems a little             |                                               */
    /*                               |  complicated but                        |                                               */
    /*                               |                                         |                                               */
    /*                               |  The first nested select                |                                               */
    /*                               |    selects counts of wtgmg              |                                               */
    /*                               |    by date and roc;                     |                                               */
    /*                               |                                         |                                               */
    /*                               |                             ======      |                                               */
    /*                               |  RATE DATE TREE ROCK WGTMG  WGTCNT      |                                               */
    /*                               |                             ======      |                                               */
    /*                               |     1 13.1    a  C21     5   18         |                                               */
    /*                               |     2 13.1    b  C21     4   18         |                                               */
    /*                               |     3 13.1    c  C21     9   18 5+4+9   |                                               */
    /*                               |                                         |                                               */
    /*                               |  The second nested select               |                                               */
    /*                               |    selects counts of wtgmg by date      |                                               */
    /*                               |                                         |                                               */
    /*                               |       DATE  WGTSUM                      |                                               */
    /*                               |                                         |                                               */
    /*                               |       13.1    40                        |                                               */
    /*                               |                                         |                                               */
    /*                               |  The outer select divides               |                                               */
    /*                               |                                         |                                               */
    /*                               |       WGTCNT/WGTSUM 18/40-.45           |                                               */
    /*                               |                                         |                                               */
    /*                               |-----------------------------------------|                                               */
    /*                               |                                         |                                               */
    /*                               |  SAS SQL CODE                           |                                               */
    /*                               |  ------------                           |                                               */
    /*                               |                                         |                                               */
    /*                               |  select                                 |                                               */
    /*                               |   l.*                                   |                                               */
    /*                               |  ,l.wgtcnt/r.wgtsum as wgtpct           |                                               */
    /*                               |  from                                   |                                               */
    /*                               |    (select                              |                                               */
    /*                               |       *                                 |                                               */
    /*                               |      ,sum(wgtmg) as  wgtcnt             |                                               */
    /*                               |    from                                 |                                               */
    /*                               |       sd1.have                          |                                               */
    /*                               |    group                                |                                               */
    /*                               |       by date, rock) as l               |                                               */
    /*                               |    left                                 |                                               */
    /*                               |      join                               |                                               */
    /*                               |    (select                              |                                               */
    /*                               |        date                             |                                               */
    /*                               |       ,sum(wgtmg) as wgtsum             |                                               */
    /*                               |     from                                |                                               */
    /*                               |        sd1.have                         |                                               */
    /*                               |     group                               |                                               */
    /*                               |        by date) as r                    |                                               */
    /*                               |  on                                     |                                               */
    /*                               |     l.date = r.date                     |                                               */
    /*                               |  order                                  |                                               */
    /*                               |     by rate                             |                                               */
    /*                               |                                         |                                               */
    /*                               | ----------------------------------------|                                               */
    /*                               |                                         |                                               */
    /*                               |  R AND PYTHON SQL                       |                                               */
    /*                               |  NO AUTOMATIC REMERGING                 |                                               */
    /*                               |  ======================                 |                                               */
    /*                               |                                         |                                               */
    /*                               |  with                                   |                                               */
    /*                               |    wgtcnt as                            |                                               */
    /*                               |  (select                                |                                               */
    /*                               |     *                                   |                                               */
    /*                               |    ,sum(wgtmg) as  wgtcnt               |                                               */
    /*                               |  from                                   |                                               */
    /*                               |     have                                |                                               */
    /*                               |  group                                  |                                               */
    /*                               |     by date, rock),                     |                                               */
    /*                               |   wgtsum as                             |                                               */
    /*                               |   (select                               |                                               */
    /*                               |       date                              |                                               */
    /*                               |      ,sum(wgtmg) as wgtsum              |                                               */
    /*                               |    from                                 |                                               */
    /*                               |       have                              |                                               */
    /*                               |    group                                |                                               */
    /*                               |       by date)                          |                                               */
    /*                               |  select                                 |                                               */
    /*                               |   ll.*                                  |                                               */
    /*                               |  ,l.wgtcnt/r.wgtsum as wgtpct           |                                               */
    /*                               |  from                                   |                                               */
    /*                               |    have as ll                           |                                               */
    /*                               |    left                                 |                                               */
    /*                               |      join                               |                                               */
    /*                               |    wgtcnt as l                          |                                               */
    /*                               |      on ll.date = l.date                |                                               */
    /*                               |       and ll.rock = l|.rock             |                                               */
    /*                               |   left                                  |                                               */
    /*                               |     join                                |                                               */
    /*                               |   wgtsum as r                           |                                               */
    /*                               |     on ll.date = r.date                 |                                               */
    /*                               | order                                   |                                               */
    /*                               |    by rate                              |                                               */
    /*                               |                                         |                                               */
    /***************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      input rate date$ tree$ rock$ wgtmg;
    cards4;
    1. 13.1 a C21 5
    2. 13.1 b C21 4
    3. 13.1 c C21 9
    4. 20.2 a C21 6
    5. 20.2 b C21 5
    6. 20.2 c C21 10
    7. 13.1 a C23 6
    8. 13.1 b C23 6
    9. 13.1 c C23 10
    10. 20.2 a C23 5
    11. 20.2 b C23 4
    12. 20.2 c C23 9
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  SD1.HAVE total obs=12                                                                                                 */
    /*                                                                                                                        */
    /*   RATE    DATE    TREE    ROCK    WGTMG                                                                                */
    /*                                                                                                                        */
    /*     1     13.1     a      C21        5                                                                                 */
    /*     2     13.1     b      C21        4                                                                                 */
    /*     3     13.1     c      C21        9                                                                                 */
    /*     4     20.2     a      C21        6                                                                                 */
    /*     5     20.2     b      C21        5                                                                                 */
    /*     6     20.2     c      C21       10                                                                                 */
    /*     7     13.1     a      C23        6                                                                                 */
    /*     8     13.1     b      C23        6                                                                                 */
    /*     9     13.1     c      C23       10                                                                                 */
    /*    10     20.2     a      C23        5                                                                                 */
    /*    11     20.2     b      C23        4                                                                                 */
    /*    12     20.2     c      C23        9                                                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                             _
    / |  ___  __ _ ___   ___  __ _| |
    | | / __|/ _` / __| / __|/ _` | |
    | | \__ \ (_| \__ \ \__ \ (_| | |
    |_| |___/\__,_|___/ |___/\__, |_|
                                |_|
    */

    proc sql;
      create
        table want as
      select
       l.*
      ,l.wgtcnt/r.wgtsum as wgtpct
      from
        (select
           *
          ,sum(wgtmg) as  wgtcnt
        from
           sd1.have
        group
           by date, rock) as l
        left
          join
        (select
            date
           ,sum(wgtmg) as wgtsum
         from
            sd1.have
         group
            by date) as r
      on
         l.date = r.date
      order
         by rate
    ;quit;

    /*---The query requires remerging summary statistics back with the original data. --*/

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.WANT total obs=12                                                                                                */
    /*                                                                                                                        */
    /*   RATE    DATE    TREE    ROCK    WGTMG    WGTCNT     WGTPCT                                                           */
    /*                                                                                                                        */
    /*     1     13.1     a      C21        5       18      0.45000                                                           */
    /*     2     13.1     b      C21        4       18      0.45000                                                           */
    /*     3     13.1     c      C21        9       18      0.45000                                                           */
    /*     4     20.2     a      C21        6       21      0.53846                                                           */
    /*     5     20.2     b      C21        5       21      0.53846                                                           */
    /*     6     20.2     c      C21       10       21      0.53846                                                           */
    /*     7     13.1     a      C23        6       22      0.55000                                                           */
    /*     8     13.1     b      C23        6       22      0.55000                                                           */
    /*     9     13.1     c      C23       10       22      0.55000                                                           */
    /*    10     20.2     a      C23        5       18      0.46154                                                           */
    /*    11     20.2     b      C23        4       18      0.46154                                                           */
    /*    12     20.2     c      C23        9       18      0.46154                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    /*--- R is little more complex than sas ---*/
    /*--- because sqllite does NOT remerge  ---*/

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf('
      with
        wgtcnt as
      (select
         *
        ,sum(wgtmg) as  wgtcnt
      from
         have
      group
         by date, rock),
       wgtsum as
       (select
           date
          ,sum(wgtmg) as wgtsum
        from
           have
        group
           by date)
      select
       ll.*
      ,l.wgtcnt/r.wgtsum as wgtpct
      from
        have as ll
        left
          join
        wgtcnt as l
          on ll.date = l.date and ll.rock = l.rock
        left
          join
        wgtsum as r
          on ll.date = r.date
      order
         by rate
      ')
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
    /*                                          |                                                                             */
    /*  > want                                  | SD1.WANT total obs=12                                                       */
    /*                                          |                                                                             */
    /*     RATE DATE TREE ROCK WGTMG    wgtpct  | ROWNAMES    RATE    DATE    TREE    ROCK    WGTMG     WGTPCT                */
    /*                                          |                                                                             */
    /*  1     1 13.1    a  C21     5 0.4500000  |     1         1     13.1     a      C21        5     0.45000                */
    /*  2     2 13.1    b  C21     4 0.4500000  |     2         2     13.1     b      C21        4     0.45000                */
    /*  3     3 13.1    c  C21     9 0.4500000  |     3         3     13.1     c      C21        9     0.45000                */
    /*  4     4 20.2    a  C21     6 0.5384615  |     4         4     20.2     a      C21        6     0.53846                */
    /*  5     5 20.2    b  C21     5 0.5384615  |     5         5     20.2     b      C21        5     0.53846                */
    /*  6     6 20.2    c  C21    10 0.5384615  |     6         6     20.2     c      C21       10     0.53846                */
    /*  7     7 13.1    a  C23     6 0.5500000  |     7         7     13.1     a      C23        6     0.55000                */
    /*  8     8 13.1    b  C23     6 0.5500000  |     8         8     13.1     b      C23        6     0.55000                */
    /*  9     9 13.1    c  C23    10 0.5500000  |     9         9     13.1     c      C23       10     0.55000                */
    /*  10   10 20.2    a  C23     5 0.4615385  |    10        10     20.2     a      C23        5     0.46154                */
    /*  11   11 20.2    b  C23     4 0.4615385  |    11        11     20.2     b      C23        4     0.46154                */
    /*  12   12 20.2    c  C23     9 0.4615385  |    12        12     20.2     c      C23        9     0.46154                */
    /*                                          |                                                                             */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''                                 \
      with                                         \
        wgtcnt as                                  \
      (select                                      \
         *                                         \
        ,sum(wgtmg) as  wgtcnt                     \
      from                                         \
         have                                      \
      group                                        \
         by date, rock),                           \
       wgtsum as                                   \
       (select                                     \
           date                                    \
          ,sum(wgtmg) as wgtsum                    \
        from                                       \
           have                                    \
        group                                      \
           by date)                                \
      select                                       \
       ll.*                                        \
      ,l.wgtcnt/r.wgtsum as wgtpct                 \
      from                                         \
        have as ll                                 \
        left                                       \
          join                                     \
        wgtcnt as l                                \
          on ll.date = l.date and ll.rock = l.rock \
        left                                       \
          join                                     \
        wgtsum as r                                \
          on ll.date = r.date                      \
      order                                        \
         by rate                                   \
       ''');
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                                            |                                                                           */
    /*     RATE  DATE TREE ROCK  WGTMG    wgtpct  |  RATE    DATE    TREE    ROCK    WGTMG     WGTPCT                         */
    /*                                            |                                                                           */
    /* 0    1.0  13.1    a  C21    5.0  0.450000  |    1     13.1     a      C21        5     0.45000                         */
    /* 1    2.0  13.1    b  C21    4.0  0.450000  |    2     13.1     b      C21        4     0.45000                         */
    /* 2    3.0  13.1    c  C21    9.0  0.450000  |    3     13.1     c      C21        9     0.45000                         */
    /* 3    4.0  20.2    a  C21    6.0  0.538462  |    4     20.2     a      C21        6     0.53846                         */
    /* 4    5.0  20.2    b  C21    5.0  0.538462  |    5     20.2     b      C21        5     0.53846                         */
    /* 5    6.0  20.2    c  C21   10.0  0.538462  |    6     20.2     c      C21       10     0.53846                         */
    /* 6    7.0  13.1    a  C23    6.0  0.550000  |    7     13.1     a      C23        6     0.55000                         */
    /* 7    8.0  13.1    b  C23    6.0  0.550000  |    8     13.1     b      C23        6     0.55000                         */
    /* 8    9.0  13.1    c  C23   10.0  0.550000  |    9     13.1     c      C23       10     0.55000                         */
    /* 9   10.0  20.2    a  C23    5.0  0.461538  |   10     20.2     a      C23        5     0.46154                         */
    /* 10  11.0  20.2    b  C23    4.0  0.461538  |   11     20.2     b      C23        4     0.46154                         */
    /* 11  12.0  20.2    c  C23    9.0  0.461538  |   12     20.2     c      C23        9     0.46154                         */
    /*                                            |                                                                           */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
