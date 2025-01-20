# elastic-soc-lab

ADDING INTEGRATIONS  
KALI LINUX VMWARE IMAGE PASSWORD AND USERNAME BOTH BEING kali 
Adding elastic defend agent as an integration
 
Configuration settings
 click save and continue 
Clicked add elastic agent to your hosts
 
Since im using kali linux vm im going to use this
 
 pasted it in here  it has finished downloading and the elastic agent has been successfully installed 
Ran this: sudo systemctl status elastic-agent.service to confirm it is running
 now running an nmap scan on the kali box itself 
Did nmap -p- localhost to do a port scan on the local machine 
 
Did sudo nmap -sS localhost  

Search process.args: nmap and got a  couple events which makes sense:
 
Create dashboard, create a visualization 
 
 horizontal axis timestamp and vertical axis count
 
 
Now clicking save and return 
 saving dashboard 
Doing some more stuff like nmap -sS me and my friend’s website livethefight doing commands like -sS, -sC, -sV, populating the visualization graph to have a lot of things to work with and diff commands like mkdir hihello, sudo -l, sudo ps
 
 
Alerts then manage rules then create new rule
 
 
Source nmap_scan
 
So in a professional environment it would be important to actually provide a name and description and about etc incase the alert actually fires off to a person working with you and the person is confused wondering what this is so I did that and kept the other defaults
 
Rule actions is also good because it decides where the alert will actually go like it can become a jira ticket or send and notify everyone on slack, Microsoft teams, etc or even a general webhook if we’re getting into apis etc. this is powerful because it allows for more automation and orchestration . I will just choose it to be sent as an email.  
 create and enable rule  now it’s all ready and it’s an alert called nmap scan and it’s now one of our rules. Let’s try to fire it off and see if we get that email
 created another visualization bar graph
Now I had realized by going here and checking the details that I was NOT getting the alerts so there was an issue with my rule. 
     as we can see the event.action is specified as end AND the event.type . However, process.args has nmap for example so I will fix my rule. 
 now it is this process_name: “nmap”
And now it works
   and I have received it as an email  when I select view rule in kibana it takes me back to here
 

This lab is more like a soc analyst skill, the EDR lab I had is more incident response 
So now I am happy to have played with elastic stack and with kibana and created alerts and detections 

Now an extension is I have decided to also try with hydra password cracking 

 
So I used rockyou.txt here I extracted it using sudo gunzip (filepath/rockyou.txt.gz)
 
This error occurs because Hydra is sending too many requests too quickly, and the SSH server is rejecting them so The default number of parallel tasks (-t) is too high for most SSH servers. Reduced it to a safer level as seen here 

Then I did sudo nano /etc/ssh/sshd_config
Changed to these parameters from default to these
MaxAuthTries 10
MaxSessions 10
LoginGraceTime 120
because
•	•  MaxAuthTries: Increases the maximum number of failed login attempts before the connection is dropped.
•	MaxSessions: Allows more concurrent SSH sessions.
•	LoginGraceTime: Extends the time before the server disconnects idle login attempts.
•  
Then did this: sudo systemctl restart ssh

 
hydra -l username -P /usr/share/wordlists/rockyou.txt -t 1 -w 3 ssh://127.0.0.1 then did this 
then used sudo systemctl status elastic-agent.service to make sure it’s running still 
 
Now doing the same thing here to avoid a mistake and seeing what is recognized here and we see that process.args is hydra: 
  
 new visualization graphs
Now creating the new rule with the mentioned info: process.name: "hydra" AND (process.args: "ssh://127.0.0.1" OR process.command_line: "hydra -l")to  Ensure the detection rule only matches events where hydra is the process name and Matches commands targeting SSH on the localhost and Adds flexibility to match additional hydra commands with partial command-line arguments like -l.
 

About rule name and desc: hydra ssh bruteforce, detects hydra activity targeting ssh on localhost 
And this time I will use a webhook instead 
 
authentication I chose none since webhook.site doesn’t require authentication for its default free-tier URLs
In the body I added this:
{
  "rule_name": "{{rule.name}}",
  "alert_id": "{{alert.id}}",
  "alert_severity": "{{alert.severity}}",
  "alert_risk_score": "{{alert.risk_score}}",
  "alert_status": "{{alert.status}}",
  "timestamp": "{{alert.timestamp}}",
  "host": {
    "name": "{{host.name}}",
    "ip": "{{host.ip}}"
  },
  "process": {
    "name": "{{process.name}}",
    "args": "{{process.args}}",
    "command_line": "{{process.command_line}}",
    "pid": "{{process.pid}}"
  }
}

 
Right now it is empty as we can see (the webhook and awaiting the first request) 
 
I now ran a hydra command 
hydra -l username -P /usr/share/wordlists/rockyou.txt -t 1 -w 3 ssh://127.0.0.1

•	hydra: Starts Hydra, a tool for brute-forcing passwords.
•	-l username: Sets the username you want to try logging in with (in this case, username).
•	-P /usr/share/wordlists/rockyou.txt: Uses the rockyou.txt file, which is a list of potential passwords, to try logging in.
•	-t 1: Runs only 1 connection (or task) at a time, so it’s slower but less likely to get blocked.
•	-w 3: Waits 3 seconds between each login attempt to avoid overwhelming the SSH server.
•	ssh://127.0.0.1: Specifies that the target is the SSH service running on your own machine (127.0.0.1 is the localhost IP).
In short: Hydra is trying to find the correct password for the specified username by testing every password in the rockyou.txt file against your local SSH server, one attempt at a time.

 and our alert here has detected the hydra ssh bruteforce so our detection rule has worked 

 
Here we have the alerts page, severity levels (I should change the hydra to not be low, but right now all 110 alerts are low due to them being the nmap and hydra being set to low) and alerts by name are shown and top alerts (100%) are by the host.name kali
 here I changed the hydra rule to medium and a risk score of 47
Lastly here is the execution log for our second rule where it shows the successful status of our manual and scheduled executions of this hydra ssh bruteforce rule 
Now we have successfully also included incident respojnse skills with the addition of detecting and creating an alert for hydra’s ssh password bruteforce and responding by sending an alert to the webhook. 





