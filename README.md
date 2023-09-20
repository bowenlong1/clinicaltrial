%macro completion_email();
     options emailsys=smtp emailhost="mailrelay.americredit.com" emailport=25;
           filename temp email
                to=("Bowen Long <bowen.long@gmfinancial.com>"
                    "Wei Shi <Wei.Shi@gmfinancial.com>"
					"Sophie Zu <sophie.zu@gmfinancial.com>"
/*					"Emma Ma <emma.ma@gmfinancial.com>"*/
/*					"James Corbin <james.corbin@gmfinancial.com>"*/
                   "Allison Roderick <Allison.Roderick@gmfinancial.com>"
                   "Fiza Gupta <FIZA.GUPTA@gmfinancial.com>"
                   "Paul Branoff <Paul.Branoff@gmfinancial.com>")
                  from=("Bowen Long <bowen.long@gmfinancial.com>")
                  sender=("Bowen Long <bowen.long@gmfinancial.com>")
                  subject=("Customer360 Run Complete")
                  attach=("&log_dir./&day_key_after._Customer360_Run_Check.pdf")
                  type="text/html";

ODS msoffice2k file=temp rs=none style=ANALYSIS options(pagebreak="no");
     ods escapechar="^";  
     proc odstext;
           p "^Customer360 Run Complete. 
			   Attached are the code-imbedded checks (also see short version below). 
			    ";
				p " ";
     run;

PROC REPORT DATA=COMBINE_DD nowd BOX

style (report) = {background = white
font_face = "Verdana" font_size = 11pt just=left }
style (column) = {background = white CELLHEIGHT = 2.5%
font_face = "Verdana" font_size = 11pt just=left}
style (header) = {foreground = cx5e2750 font_face="Verdana"
font_size = 11pt just=left
background = white} ;
columns 'Check No.'n 'Check'n
;
run;

ods _all_ close;
%mend;
%completion_email;
