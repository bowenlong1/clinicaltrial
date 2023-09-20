%macro completion_email();

    options emailsys=smtp emailhost="mailrelay.americredit.com" emailport=25;

    filename temp email
        to=("Bowen Long <bowen.long@gmfinancial.com>"
            "Wei Shi <Wei.Shi@gmfinancial.com>"
            "Sophie Zu <sophie.zu@gmfinancial.com>"
            "Allison Roderick <Allison.Roderick@gmfinancial.com>"
            "Fiza Gupta <FIZA.GUPTA@gmfinancial.com>"
            "Paul Branoff <Paul.Branoff@gmfinancial.com>")
        from=("Bowen Long <bowen.long@gmfinancial.com>")
        sender=("Bowen Long <bowen.long@gmfinancial.com>")
        subject="Customer360 Run Complete"
        type="text/html";

    ODS msoffice2k file=temp rs=none style=ANALYSIS options(pagebreak="no");
    ods escapechar="^";

    proc odstext;
        p "^Customer360 Run Complete. 
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
        style (report) = {background = white
        font_face = "Verdana" font_size = 11pt just=left }
        style (column) = {background = white CELLHEIGHT = 2.5%
        font_face = "Verdana" font_size = 11pt just=left}
        style (header) = {foreground = cx5e2750 font_face="Verdana"
        font_size = 11pt just=left
        background = white} ;
        columns 'Check No.'n 'Check'n 'Value'n;
    run;

    ods _all_ close;
    
%mend;

%completion_email;
