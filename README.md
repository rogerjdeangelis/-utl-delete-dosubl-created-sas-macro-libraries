# -utl-delete-dosubl-created-sas-macro-libraries
Delete dosubl created sas macro libraries
    %let pgm=utl-delete-dosubl-created-sas-macro-libraries;

    Delete dosubl created sas macro libraries

    github
    http://tinyurl.com/28tkkmw2
    https://github.com/rogerjdeangelis/-utl-delete-dosubl-created-sas-macro-libraries

    The extra macro libraries created by dosubl seem to be of no use
    after dosubl has completed. They persist after dosubl.
    You canot rerun the dosubl macros without
    the macro compilations,  keeping just the calls.

    They seem harmless but cleanup assures a prestine enviroment?
    It is also possible to have the same macro in the extra libraries and in SASMACR.

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|

    Bartosz Jablonski
    yabwon@gmail.com
    */

    data _null_;

    rc = doSubL(
        '

        %macro testDoSubL();
        %put ***testDoSubL***;
        %mend testDoSubL;

        data _null_;
          rc2 = doSubL(
          ''
          %macro testDoSubL2();
          %put ***testDoSubL2***;
          %mend testDoSubL2;


          data _null_;
            rc2 = doSubL(
            ''''
            %macro testDoSubL3();
            %put ***testDoSubL3***;
            %mend testDoSubL3;

            %testDoSubL()
            %testDoSubL2()
            %testDoSubL3()

            ''''
            );
          run;

          %testDoSubL()
          %testDoSubL2()
          ''
          );
        run;

        %testDoSubL()
        '
    );

    run;quit;

    /*---- The compiled macros cannot be resused                             ----*/

    data _null_;

    rc = doSubL(
        '
        data _null_;
          rc2 = doSubL(
          ''

          data _null_;
            rc2 = doSubL(
            ''''

            %testDoSubL()
            %testDoSubL2()
            %testDoSubL3()

            ''''
            );
          run;

          %testDoSubL()
          %testDoSubL2()
          ''
          );
        run;

        %testDoSubL()
        '
    );

    run;quit;

    /*---- ERROR Statement is not valid or it is used out of proper order.   ----*/
    /*---- WARNING: Apparent invocation of macro TESTDOSUBL not resolved.    ----*/
    /*---- WARNING: Apparent invocation of macro TESTDOSUBL2 not resolved.   ----*/
    /*---- WARNING: Apparent invocation of macro TESTDOSUBL3 not resolved.   ----*/


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Produces sasmac6, and it looks like you can go deeper.                                                                */
    /*                                                                                                                        */
    /*  List all macro libs                                                                                                   */
    /*  proc sql;                                                                                                             */
    /*    select                                                                                                              */
    /*       *                                                                                                                */
    /*    from                                                                                                                */
    /*       sashelp.vscatlg                                                                                                  */
    /*    where                                                                                                               */
    /*           libname =   "WORK"                                                                                           */
    /*       and memname eqt "SASMAC"                                                                                         */
    /*  ;quit;                                                                                                                */
    /*                                                                                                                        */
    /*  Library                                                                                                               */
    /*  Name      Member Name                                                                                                 */
    /*  ------------------------                                                                                              */
    /*  WORK      SASMAC2                                                                                                     */
    /*  WORK      SASMAC4                                                                                                     */
    /*  WORK      SASMAC6                                                                                                     */
    /*  WORK      SASMACR                                                                                                     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  produced sasmac6, and it looks like you can go deeper.                                                                */
    /*                                                                                                                        */
    /*  sasmacr has no   testDoSubL  macros
    /*  sasmac2 contains testDoSubL                                                                                           */
    /*  sasmac4 contains testDoSubL2                                                                                          */
    /*  sasmac6 contains testDoSubL3                                                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */


    %macro deleteSasmacN()
       /des="Delete all numberes sasmacr# libraries. does not delete sasnacr";

    proc sql;
      select
         memname
      into
         :_catNam separated by " "
      from
         sashelp.vscatlg
      where
             libname =   "WORK"
         and memname eqt "SASMAC"
         and memname ne  "SASMACR"
    ;quit;

    proc datasets lib=work mt=cat ;
      delete &_catNam;
    run;quit;
    %mend deleteSasmacN;

    %deleteSasmacN();

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  DELETE USELESS EXTRA DOSUBL CREATE MACRO LIBRARIES                                                                    */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  Deleting WORK.SASMAC2 (memtype=CATALOG).                                                                              */
    /*  Deleting WORK.SASMAC4 (memtype=CATALOG).                                                                              */
    /*  Deleting WORK.SASMAC6 (memtype=CATALOG).                                                                              */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
