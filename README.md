# utl-common-products-sold-by-two-stores-split-string-pivot-long-sql-wps-r-and-python
Select products sold by both store1 and store2
    %let pgm=utl-common-products-sold-by-two-stores-split-string=pivot-long-sql-wps-r-and-python;

    Select products sold by both store1 and store2

    SOLUTIONS

        1 wps sql
        2 wps r sql
        3 wps python sql
        4 wps sql dynamic
    github                                                                                                                  
    https://tinyurl.com/spn3vscf                                                                                            
    https://github.com/rogerjdeangelis/utl-common-products-sold-by-two-stores-split-string-pivot-long-sql-wps-r-and-python  
                                                                                                                            
    stackoverflow                                                                                                           
    https://tinyurl.com/2evp47vx                                                                                            
    https://stackoverflow.com/questions/20324372/splitting-a-single-column-name-into-two-forename-surname-in-sql            
                                                                                                                
    Slightly related repos
    https://github.com/rogerjdeangelis/utl-pivot-multiple-columns-to-long-format-untranspose
    https://github.com/rogerjdeangelis/utl-transpose-fat-to-skinny-pivot-longer-in-sas-wps-r-pythonv

    https://github.com/rogerjdeangelis/utl_words_in_common_in_two_sentences
    https://github.com/rogerjdeangelis/utl-linking-values-based-on-common-names
    https://github.com/rogerjdeangelis/utl-find-common-strings-inside-groups-of-records
    https://github.com/rogerjdeangelis/utl-linking-values-based-on-common-names
    https://github.com/rogerjdeangelis/utl-select-a-list-of-variables-with-common-prefix-and-suffix
    https://github.com/rogerjdeangelis/utl_most_popular_common_words_in_three_shakespeare_plays
    https://github.com/rogerjdeangelis/utl-length-of-longest-common-word-in-two-sentences-text-AI

    SOAPBOX ON

    There is an issue with sqllite, it needs a scan words function.
    The sqllite solution is unworkable for long sentences.

    For long sentences you can use sql arrays for the wps solution, see last solution.

    Pivoting outside sql is a more robust solution for R and Python.

    The solution demonstrates how long data structure can lead to simple understadable solutions.
    Yhr slower performance of SQL is overstated, except fo r big data, single table over 1tb.
    This solution can easily be parallelized.

    SOAPBOX OFF

    /**************************************************************************************************************************/
    /*  _                   _                                                                                                 */
    /* (_)_ __  _ __  _   _| |_ ___                                                                                           */
    /* | | `_ \| `_ \| | | | __/ __|                                                                                          */
    /* | | | | | |_) | |_| | |_\__ \                                                                                          */
    /* |_|_| |_| .__/ \__,_|\__|___/                                                                                          */
    /*         |_|                                                                                                            */
    /*                                                                                                                        */
    /*   SD1.HAV1ST total obs=6                         SD1.HAV2ND total obs=6                                                */
    /*                                                                                                                        */
    /*   PRODUCTS                    COMPANY           LOCATION      PRODUCTS            COMPANY           LOCATION           */
    /*                                                                                                                        */
    /*   books maps paper            bookstore.inc      55567        puzzles maps        bookstore.inc      22908             */
    /*   spoons knives sport         silverware.inc     85750        forks knives        silverware.inc     05301             */
    /*   lugar rifles zip            arms.inc           95750        23special pistol    arms.inc           33129             */
    /*   pc tablets iphone           eletronic.inc      53310        pc tablets          electronic.inc     22028             */
    /*   truck car van               vehicles.inc       44123        minivan car         vehicles.inc       77190             */
    /*   toothpase lipbalm soap.inc  health             88123        toothpase lipbalm   health.inc         66983             */
    /*      _               _                                                                                                 */
    /*  ___| |_ ___ _ __   / |                                                                                                */
    /* / __| __/ _ \ `_ \  | |                                                                                                */
    /* \__ \ ||  __/ |_) | | |                                                                                                */
    /* |___/\__\___| .__/  |_|                                                                                                */
    /*             |_|                                                                                                        */
    /*                                                                                                                        */
    /*  STEP ONE PIVOT LONG (EXAMPLE)                                                                                         */
    /*                                                                                                                        */
    /*  NRM1ST total obs=18                             NRM2ND total obs=12                                                   */
    /*                                                                                                                        */
    /*  PRD          COMPANY           LOCATION          PRD          COMPANY           LOCATION                              */
    /*                                                                                                                        */
    /*  books        bookstore.inc      55567            maps         bookstore.inc      22908                                */
    /*  maps         bookstore.inc      55567            puzzles      bookstore.inc      22908                                */
    /*  paper        bookstore.inc      55567            ...                                                                  */
    /*  ...                                                                                                                   */
    /*              _               _                                                                                         */
    /*   ___  _   _| |_ _ __  _   _| |_                                                                                       */
    /*  / _ \| | | | __| `_ \| | | | __|                                                                                      */
    /* | (_) | |_| | |_| |_) | |_| | |_                                                                                       */
    /*  \___/ \__,_|\__| .__/ \__,_|\__|                                                                                      */
    /*                 |_|                                                                                                    */
    /*                                                                                                                        */
    /*     COMPANY        L_LOCATION    R_LOCATION    R_PRD         L_PRD                                                     */
    /*                                                                                                                        */
    /*  bookstore.inc       55567         22908       maps          maps   * only maps are sold at both locations             */
    /*                                                                                                                        */
    /*  health.inc          88123         66983       lipbalm       lipbalm                                                   */
    /*  health.inc          88123         66983       toothpaste    toothpaste                                                */
    /*  silverware.inc      85750         05301       knives        knives                                                    */
    /*  vehicles.inc        44123         77190       car           car                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";

    data sd1.hav1st;
     informat products $24. company $18. location $5.;
     input products & company location;
    cards4;
    toothpaste lipbalm soap  health.inc 88123
    spoons knives sport  silverware.inc 85750
    lugar rifles shotgun  arms.inc 95750
    pc tablets iphone  eletronic.inc 53310
    truck car van  vehicles.inc 44123
    books maps paper  bookstore.inc 55567
    ;;;;
    run;quit;

    data sd1.hav2nd;
     informat products $24.  company $18. location $5.;
     input products & company location;
    cards4;
    toothpaste lipbalm  health.inc 88123
    forks knives  silverware.inc 05301
    special pistol  arms.inc 33129
    pc tablets  electronic.in 22028
    minivan car  vehicles.inc 77190
    puzzles maps  bookstore.inc 22908
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   SD1.HAV1ST total obs=6                         SD1.HAV2ND total obs=6                                                */
    /*                                                                                                                        */
    /*   PRODUCTS                    COMPANY           LOCATION      PRODUCTS            COMPANY           LOCATION           */
    /*                                                                                                                        */
    /*   spoons knives sport         silverware.inc     85750        forks knives        silverware.inc     05301             */
    /*   lugar rifles zip            arms.inc           95750        23special pistol    arms.inc           33129             */
    /*   pc tablets iphone           eletronic.inc      53310        pc tablets          electronic.inc     22028             */
    /*   truck car van               vehicles.inc       44123        minivan car         vehicles.inc       77190             */
    /*   books maps paper            bookstore.inc      55567        puzzles maps        bookstore.inc      22908             */
    /*   toothpase lipbalm soap.inc  health             88123        toothpase lipbalm   health.inc         66983             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                  _
    / | __      ___ __  ___   ___  __ _| |
    | | \ \ /\ / / `_ \/ __| / __|/ _` | |
    | |  \ V  V /| |_) \__ \ \__ \ (_| | |
    |_|   \_/\_/ | .__/|___/ |___/\__, |_|
                 |_|                 |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    options validvarname=any;
    libname sd1 "d:/sd1";
    proc sql;
      create
        table sd1.want as
      select
        l.company
       ,l.location as l_location
       ,r.location as r_location
       ,r.prd as r_prd
       ,l.prd as l_prd
     from
      (select scan(products,1," ") as prd, company, location from sd1.hav1st union all
       select scan(products,2," ") as prd, company, location from sd1.hav1st union all
       select scan(products,3," ") as prd, company, location from sd1.hav1st) as l
     inner join
      (select scan(products,1," ") as prd, company, location from sd1.hav2nd union all
       select scan(products,2," ") as prd, company, location from sd1.hav2nd) as r
     on
           trim(l.company) = trim(r.company)
       and trim(l.prd) = trim(r.prd)
     order
        by l.company, l.prd
    ;quit;

    proc print;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.WANT total obs=5                                                                                                  */
    /*                                                                                                                        */
    /* Obs     L_COMPANY        L_LOCATION    R_LOCATION    R_PRD         L_PRD                                               */
    /*                                                                                                                        */
    /*  1     bookstore.inc       55567         22908       maps          maps                                                */
    /*  2     health.inc          88123         66983       lipbalm       lipbalm                                             */
    /*  3     health.inc          88123         66983       toothpaste    toothpaste                                          */
    /*  4     silverware.inc      85750         05301       knives        knives                                              */
    /*  5     vehicles.inc        44123         77190       car           car                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                                          _
    |___ \  __      ___ __  ___   _ __   ___  __ _| |
      __) | \ \ /\ / / `_ \/ __| | `__| / __|/ _` | |
     / __/   \ V  V /| |_) \__ \ | |    \__ \ (_| | |
    |_____|   \_/\_/ | .__/|___/ |_|    |___/\__, |_|
                     |_|                        |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x(resolve('
    libname sd1 "d:/sd1";
    %let scan=substring(products, instr(products, ` `) + 1;
    %put &=scan;
    proc r;
    export data=sd1.hav1st r=hav1st;
    export data=sd1.hav2nd r=hav2nd;
    submit;
    library(sqldf);
    want<-sqldf("
     select
        l.company  as l_company
       ,r.company  as r_company
       ,l.location as l_location
       ,r.location as r_location
       ,r.prd as r_prd
       ,l.prd as l_prd
     from
       (select substring(products, 1, instr(products, ` `) - 1) as prd, company, location from hav1st union all
        select substring(&scan), 1, instr(&scan), ` `) - 1)     as prd, company, location from hav1st union all
        select substring(&scan),    instr(&scan), ` `) + 1)     as prd, company, location from hav1st) as l
     join
       (select substring(products, 1, instr(products, ` `) - 1) as prd, company, location from hav2nd union all
        select &scan)  as prd, company, location from hav2nd)   as r
     on
           trim(l.company) = trim(r.company)
       and trim(l.prd)     = trim(r.prd)
     order
        by l.company, l.prd
    ");
    want;
    str(want);
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    proc print data=sd1.want;
    run;quit;
    '));

    libname sd1 "d:/sd1";
    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  The WPS R System                                                                                                      */
    /*                                                                                                                        */
    /*         r_company      r_company l_location r_location      r_prd      l_prd                                           */
    /*  1  bookstore.inc  bookstore.inc      55567      22908       maps       maps                                           */
    /*  2     health.inc     health.inc      88123      88123    lipbalm    lipbalm                                           */
    /*  3     health.inc     health.inc      88123      88123 toothpaste toothpaste                                           */
    /*  4 silverware.inc silverware.inc      85750      05301     knives     knives                                           */
    /*  5   vehicles.inc   vehicles.inc      44123      77190        car        car                                           */
    /*                                                                                                                        */
    /*  The WPS System                                                                                                        */
    /*                                                                                                                        */
    /*  Obs      R_COMPANY         R_COMPANY1      L_LOCATION    R_LOCATION    R_PRD         L_PRD                            */
    /*                                                                                                                        */
    /*   1     bookstore.inc     bookstore.inc       55567         22908       maps          maps                             */
    /*   2     health.inc        health.inc          88123         88123       lipbalm       lipbalm                          */
    /*   3     health.inc        health.inc          88123         88123       toothpaste    toothpaste                       */
    /*   4     silverware.inc    silverware.inc      85750         05301       knives        knives                           */
    /*   5     vehicles.inc      vehicles.inc        44123         77190       car           car                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                                    _   _                             _
    |___ /  __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) |  \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/    \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
                     |_|         |_|    |___/                                |_|
    */

    %utlfkil(d:/xpt/want.xpt);

    proc datasets lib=work nolist mt=view mt=data nodetails;delete want; run;quit;
    proc datasets lib=sd1 nolist mt=view mt=data nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.hav1st python=hav1st;
    export data=sd1.hav2nd python=hav2nd;
    submit;
    import pyreadstat;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    want = pdsql('''
     select
        l.company  as l_company
       ,r.company  as r_company
       ,l.location as l_location
       ,r.location as r_location
       ,r.prd      as r_prd
       ,l.prd      as l_prd
     from
       (select substring(products, 1, instr(products, ` `) - 1) as prd, company, location from hav1st union all
        select substring(&scan), 1, instr(&scan), ` `) - 1)     as prd, company, location from hav1st union all
        select substring(&scan),    instr(&scan), ` `) + 1)     as prd, company, location from hav1st) as l
     join
       (select substring(products, 1, instr(products, ` `) - 1) as prd, company, location from hav2nd union all
        select &scan)  as prd, company, location from hav2nd)   as r
     on
           trim(l.company) = trim(r.company)
       and trim(l.prd)     = trim(r.prd)
     order
        by l.company, l.prd
    ''');
    print(want);
    pyreadstat.write_xport(want, 'd:/xpt/want.xpt',table_name='want',file_format_version=5
    ,column_labels=['R_COMPANY' ,'R_COMPANY' ,'L_LOCATION' ,'R_LOCATION' ,'R_PRD' ,'L_PRD']);
    endsubmit;
    run;quit;
    ");

    libname xpt xport "d:/xpt/want.xpt";

    proc contents data=xpt._all_;
    run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;

    proc print;
    run;quit;

    libname xpt clear;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  The WPS Python System                                                                                                 */
    /*                                                                                                                        */
    /*              l_company           r_company  ...               r_prd       l_prd                                        */
    /*  0  bookstore.inc       bookstore.inc       ...    maps                    maps                                        */
    /*  1  health.inc          health.inc          ...       lipbalm           lipbalm                                        */
    /*  2  health.inc          health.inc          ...          toothpaste  toothpaste                                        */
    /*  3  silverware.inc      silverware.inc      ...  knives                  knives                                        */
    /*  4  vehicles.inc        vehicles.inc        ...    car                      car                                        */
    /*                                                                                                                        */
    /*  The WPS System                                                                                                        */
    /*                                                                                                                        */
    /* Obs      R_COMPANY       L_LOCATION    R_LOCATION    R_PRD         L_PRD                                               */
    /*                                                                                                                        */
    /*  1     bookstore.inc       55567         22908       maps          maps                                                */
    /*  2     health.inc          88123         88123       lipbalm       lipbalm                                             */
    /*  3     health.inc          88123         88123       toothpaste    toothpaste                                          */
    /*  4     silverware.inc      85750         05301       knives        knives                                              */
    /*  5     vehicles.inc        44123         77190       car           car                                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                                   _       _                             _
    | || |  __      ___ __  ___   ___  __ _| |   __| |_   _ _ __   __ _ _ __ ___ (_) ___
    | || |_ \ \ /\ / / `_ \/ __| / __|/ _` | |  / _` | | | | `_ \ / _` | `_ ` _ \| |/ __|
    |__   _| \ V  V /| |_) \__ \ \__ \ (_| | | | (_| | |_| | | | | (_| | | | | | | | (__
       |_|    \_/\_/ | .__/|___/ |___/\__, |_|  \__,_|\__, |_| |_|\__,_|_| |_| |_|_|\___|
                     |_|                 |_|          |___/
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    proc sql;
      select max(countw(products)) into :_wd1st from sd1.hav1st;  /* 3 */
      select max(countw(products)) into :_wd2nd from sd1.hav2nd;  /* 2 */
    quit;

    %array(_w1,values=1-&_wd1st);
    %array(_w2,values=1-&_wd2nd);

    %utl_submit_wps64x(resolve('
    options validvarname=any;
    libname sd1 "d:/sd1";
    proc sql;
      create
        table sd1.want as
      select
        l.company
       ,l.location as l_location
       ,r.location as r_location
       ,r.prd as r_prd
       ,l.prd as l_prd
     from
      (
       %do_over(_w1,phrase=%str(
       select scan(products,?," ") as prd, company, location from sd1.hav1st),between=union all)
     ) as l
     inner join
      (
       %do_over(_w2,phrase=%str(
       select scan(products,?," ") as prd, company, location from sd1.hav2nd),between=union all)
      ) as r
     on
           trim(l.company) = trim(r.company)
       and trim(l.prd) = trim(r.prd)
     order
        by l.company, l.prd
    ;quit;

    proc print;
    run;quit;
    '));

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  SD1.WANT total obs=5 10MAY2022:16:48:42                                                                               */
    /*                                                                                                                        */
    /*  Obs       COMPANY        l_location    r_location    r_prd         l_prd                                              */
    /*                                                                                                                        */
    /*   1     bookstore.inc       55567         22908       maps          maps                                               */
    /*   2     health.inc          88123         88123       lipbalm       lipbalm                                            */
    /*   3     health.inc          88123         88123       toothpaste    toothpaste                                         */
    /*   4     silverware.inc      85750         05301       knives        knives                                             */
    /*   5     vehicles.inc        44123         77190       car           car                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
