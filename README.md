%macro completion_email();

options emailsys=smtp emailhost="mailrelay.americredit.com" emailport=25;

filename temp email
    to=("Bowen Long <bowen.long@gmfinancial.com>"
    sender=("Bowen Long <bowen.long@gmfinancial.com>")
    subject="4660 Strategy Run Complete"
    type="text/html";

ODS msoffice2k file=temp rs=none style=ANALYSIS options(pagebreak="no");
ods escapechar="^";

proc odstext;
    p "^4660 Strategy Run Complete. 
      Attached are the code-imbedded checks (also see short version below). 
      ";
    p " ";
run;

proc sql;
    create table combined_checks as
        select Step, 'Value'n, 'Check'n from dd_01
        union all select Step, 'Value'n, 'Check'n from dd_02
        union all select Step, 'Value'n, 'Check'n from dd_03
        union all select Step, 'Value'n, 'Check'n from dd_04
        union all select Step, 'Value'n, 'Check'n from dd_05
        union all select Step, 'Value'n, 'Check'n from dd_06
        union all select Step, 'Value'n, 'Check'n from dd_07
        union all select Step, 'Value'n, 'Check'n from dd_08
        union all select Step, 'Value'n, 'Check'n from dd_09
        union all select Step, 'Value'n, 'Check'n from dd_10
        union all select Step, 'Value'n, 'Check'n from dd_11
        union all select Step, 'Value'n, 'Check'n from dd_12
        union all select Step, 'Value'n, 'Check'n from dd_13;
quit;

PROC REPORT DATA=combined_checks nowd BOX
/*    style (report) = {background = white*/
/*    font_face = "Verdana" font_size = 11pt just=left }*/
/*    style (column) = {background = white CELLHEIGHT = 2.5%*/
/*    font_face = "Verdana" font_size = 11pt just=left}*/
/*    style (header) = {foreground = cx5e2750 font_face="Verdana"*/
/*    font_size = 11pt just=left*/
/*    background = white} ;*/
    columns 'Step'n 'Value'n 'Check'n;
run;

ods _all_ close;
%mend;

%completion_email;

79                                                                                                   PROC REPORT
79       ! DATA=combined_checks nowd BOX            columns 'Step'n 'Value'n 'Check'n; run;  ods _all_ close;
                                                    _______
                                                    22
                                                    202

ERROR 22-322: Syntax error, expecting one of the following: ;, BOX, BYPAGENO, CENTER, COLWIDTH, COMMAND, COMPLETECOLS, 
              COMPLETEROWS, CONTENTS, DATA, EXCLNPWGT, EXCLNPWGTS, FORMCHAR, FS, HEADLINE, HEADSKIP, HELP, LIST, LS, MISSING, 
              NAMED, NOALIAS, NOCENTER, NOCOMPLETECOLS, NOCOMPLETEROWS, NOEXEC, NOEXECUTE, NOFS, NOHEADER, NOTHREADS, NOWD, 
              NOWINDOWS, OUT, OUTREPT, PANELS, PCTLDEF, PROFILE, PROMPT, PS, PSPACE, QMARKERS, QMETHOD, QNTLDEF, REPORT, SHOWALL, 
              SPACING, SPANROWS, SPLIT, STYLE, THREADS, VARDEF, WD, WINDOWS, WRAP.  

ERROR 202-322: The option or parameter is not recognized and will be ignored.
