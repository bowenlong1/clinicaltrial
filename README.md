%macro completion_email();

    /* Email configuration */
    options emailsys=smtp emailhost="mailrelay.americredit.com" emailport=25;

    filename temp email 
        to=("Bowen Long bowen.long@gmfinancial.com")
        sender=("Bowen Long bowen.long@gmfinancial.com")
        subject="4660 Strategy Run Complete"
        type="text/html";

    ODS msoffice2k file=temp rs=none style=ANALYSIS options(pagebreak="no"); 
    ods escapechar="^";

    proc odstext;
        p "^4660 Strategy Run Complete. Attached are the code-imbedded checks (also see short version below).";
        p " ";
    run;

    proc sql;
        create table combined_checks as
        select Step, 'Value'n, 'Check'n from dd_01
        union all select Step, 'Value'n, 'Check'n from dd_02
        /* ... include the rest of the UNION statements ... */
        union all select Step, 'Value'n, 'Check'n from dd_13; 
    quit;

    /* Modified PROC REPORT */
    PROC REPORT DATA=combined_checks nowd;
        COLUMN Step 'Value'n 'Check'n;
    run;

    ods _all_ close; 

%mend;

%completion_email;
