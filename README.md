/* Define email connection */
filename mymail email 
  to=("recipient1@example.com", "recipient2@example.com")
  subject="Subject of your email"
  from="youremail@example.com"
  attach=("/path/to/your/file.csv" content-type="text/csv");

/* Compose the email body */
data _null_;
  file mymail;
  put "Dear Recipient,";
  put "";
  put "Here's the content of the email.";
  put "Please find attached the required CSV file.";
  put "";
  put "Best regards,";
  put "Your Name";
run;

/* Clear the email filename */
filename mymail clear;

