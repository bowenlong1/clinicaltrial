filename mailtemp temp;

data _null_;
    set combined_checks;
    file mailtemp;
    put Step $30. +3 'Value'n $20. +3 'Check'n $10.;
run;

filename sendmail email to="your_email_address@example.com"
                     subject="Combined Checks"
                     from="sender_email_address@example.com";

data _null_;
    file sendmail;
    infile mailtemp;
    input;
    put _infile_;
run;

filename mailtemp clear;
