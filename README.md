# utl_transposing_when_mutiple_values_and_variable_names_are_in_the_data
Transposing when mutiple values and variable names are in the data. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Transposing when mutiple values and variable names are in the data
    
    see improvement to double transpose on end

    github
    https://tinyurl.com/yaqxs6fe
    https://github.com/rogerjdeangelis/utl_transposing_when_mutiple_values_and_variable_names_are_in_the_data

    SAS Forum
    https://tinyurl.com/y9aduwmf
    https://communities.sas.com/t5/SAS-Enterprise-Guide/Proc-transpose-by-two-columns/m-p/465607

      Two Solutons

            1. Double Transpose  ( output is character)     (Same result WPS/SAS)
            2. Gather macro Alea Iacta (output is numeric)  (Don't have gather in WPS yet)

    INPUT
    =====

      WORK.HAVE total obs=2                          |  RULES
                                                     |  Note mutiple different variable names
        GROUP    NAME1    VALUE1    NAME2    VALUE2  |  in subsequent observations
                                                     |
          A      VAR1       100     VAR2       200   |  For each group we want VAR1-VAR4 in one
          A      VAR3       300     VAR4       400   |  observation

      EXAMPLE OUTPUT

        GROUP   VAR1    VAR3    VAR2    VAR4

          A      100     300     200     400

    PROCESS
    =======

     1. Double Transpose

       proc transpose data=have out=havxpo
          (keep=group _name_ col1 where=(upcase(_name_ )=: "VALUE"));
         by _all_;
         var _all_;
       run;quit;

       proc transpose data=havXpo out=wantXpoXpo(drop=_name_) prefix=var;
         by group;
         var col1;
       run;quit;


     2. Gather macro Alea Iacta (output is numeric)

       * this keeps val as numeric;
       %utl_gather(have,var,val,group,havNrm,valformat=8.);

       proc transpose data=havNrm(where=(val ne .))
            out=wantNrmXpo(drop=_name_) prefix=var;
         by group;
         var val;
       run;quit;


    OUTPUT
    ======

     1. Double Transpose

       WORK.WANTXPOXPO total obs=1

                           Character
                  ============================
        GROUP    VAR1    VAR2    VAR3    VAR4

          A      100     200     300     400

     2. Gather macro Alea Iacta (output is numeric)

       WORK.WANTNRMXPO total obs=1

                           Numeric
                  ============================
       GROUP    VAR1    VAR2    VAR3    VAR4

         A       100     200     300     400

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;


    options validvarname=upcase;

    data have ;
      input group $ name1 $ value1 name2 $ value2 ;
    cards4;
    A VAR1 100 VAR2 200
    A VAR3 300 VAR4 400
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

    see process for SAS solutions;

    * WPS double transpose;
    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
       proc transpose data=wrk.have out=havxpo
          (keep=group _name_ col1 where=(upcase(_name_ )=: "VALUE"));
         by _all_;
         var _all_;
       run;quit;
       proc transpose data=havXpo out=wantXpoXpo(drop=_name_) prefix=var;
         by group;
         var col1;
       run;quit;
    run;quit;
    proc print;
    run;quit;
    ');


    Improvement to double transpose keeps numeric type

    proc transpose data=have(keep=group val:) out=havxpo
      (keep=group _name_ col1 where=(upcase(_name_ )=: "VALUE"));
     by _all_;
     var val:;
    run;quit;

    proc transpose data=havXpo out=wantXpoXpo(drop=_name_) prefix=var;
     by group;
     var col1;
    run;quit;
