### Introduction 

I’ve created this project to demonstrate how the Elastic Stack and Kibana can be used to detect and respond to real-world security events, focusing on tools like Nmap and Hydra. This lab covers the full process—from setting up the Elastic Agent on a Kali Linux VMware image to creating custom detection rules, new Kibana visualizations, and automated alerts sent to email and webhooks. Along the way, I explored debugging detection issues, refining rules for better accuracy, and extending capabilities to include brute-force attack detection alongside Nmap scans. This project showcases how Elastic Stack is a powerful tool for monitoring, detection, and automation in cybersecurity workflows, combining SOC analyst and incident response skills.

---


#### Adding Integrations
![image](https://github.com/user-attachments/assets/a7ebdbf0-ad3d-43e5-8b62-9d24ff952ea3)

**Kali Linux VMware Image**
- Password and Username: Both are `kali`.
![image](https://github.com/user-attachments/assets/7f53ef9a-f6de-4cc4-85ce-bcb7d57aab5c)

**Adding Elastic Defend Agent as an Integration**:
1. Open Configuration Settings.
2. Click **Save and Continue**.
3. Click **Add Elastic Agent to Your Hosts**.
4. Since I’m using a Kali Linux VM, I selected the relevant configuration, pasted it into the terminal of Kali VM, and waited for the download and installation to finish.
![image](https://github.com/user-attachments/assets/9b14aaf6-0770-4d0a-8816-b5d594ed1f4c)
Config settings:
![image](https://github.com/user-attachments/assets/5e61b9f0-4312-4cb9-9193-3df6085105f0)
Since im using kali linux vm im going to use this
![image](https://github.com/user-attachments/assets/5679be80-3aad-4a5a-940a-c06c8882d406)
![image](https://github.com/user-attachments/assets/fe271b52-7c96-4e2d-8c4b-44c428be87e4)
It has finished downloading and the elastic agent has been successfully installed 
![image](https://github.com/user-attachments/assets/335cce50-b202-4b4f-a5d7-a864cb3f6628)


**To confirm the installation was successful:**
```bash
sudo systemctl status elastic-agent.service
```
![image](https://github.com/user-attachments/assets/27fbb2bc-c143-4c7b-9eaf-581fd926e25a)

---
 Now running an nmap scan on the kali box itself 
#### Nmap Scanning and Event Detection

**Step 1: Perform an Nmap scan on the local machine.**
```bash
nmap -p- localhost
sudo nmap -sS localhost
```
![image](https://github.com/user-attachments/assets/c9ec4763-3045-4f41-8c47-d2b0ee4f4409)
![image](https://github.com/user-attachments/assets/120a13b9-4256-47a3-a8d3-7d3fb8b7aaaa)


**Step 2: Search for process.args: nmap in Kibana to validate events.**
- A couple of events appeared, which made sense given the scan.
![image](https://github.com/user-attachments/assets/9dd1fabc-eadd-4560-a1fe-2aaa7cf1a09f)

**Step 3: Create a Dashboard:**
1. Create a visualization with the Horizontal Axis as the timestamp and the Vertical Axis as the count.
2. Save the visualization and return to the dashboard.
![image](https://github.com/user-attachments/assets/336593f2-afd4-4298-a7ce-be611ad7a139)
![image](https://github.com/user-attachments/assets/6a396fe9-7d7f-4944-86fe-f4fe537e64a1)
![image](https://github.com/user-attachments/assets/6eee9138-e7f4-4a5c-a924-7fcb64642505)
![image](https://github.com/user-attachments/assets/f34fe663-daa4-41a9-9806-67666949f5ee)
Giving it a title and a description (title: karim siem visualization, desc: count over time)
![image](https://github.com/user-attachments/assets/005dfe0f-f247-4ccd-99d9-9e31adf761bc)


**Step 4: Populate the visualization graph by running additional commands:**
```bash
nmap -sS [target website]
nmap -sC [target website]
nmap -sV [target website]
mkdir hihello
sudo -l
sudo ps
```
Here I'm doing some more stuff like nmap -sS against me and my friend’s website livethefight doing commands like -sS, -sC, -sV, populating the visualization graph to have a lot of things to work with and diff. commands like mkdir hihello, sudo -l, sudo ps, etc
![image](https://github.com/user-attachments/assets/6339e5db-6d43-4f7f-b131-b6d46dae300d)
![image](https://github.com/user-attachments/assets/3363841a-b9f0-4b99-b644-778fe06ee375)


---

#### Alerts and Rules for Nmap Scans

1. Navigate to **Alerts → Manage Rules → Create New Rule**.
2. Create a source for `nmap_scan`.
3. Provide a meaningful name, description for clarity and professional communication.
![image](https://github.com/user-attachments/assets/3837e6df-08d0-407a-923c-8153c60929f5)
![image](https://github.com/user-attachments/assets/1ebc7ffe-9ed8-4542-9bf2-7dfb87c05507)
Source:event_action: nmap_scan
![image](https://github.com/user-attachments/assets/cc01f4db-c07f-441a-87cf-c0b170966568)

So in a professional environment it would be important to actually provide a name and description and about etc incase the alert actually fires off to a person working with you and the person is confused wondering what this is so I did that here and kept the other defaults
![image](https://github.com/user-attachments/assets/ae0e3fe0-68d5-443f-a208-c6a91697818a)

![image](https://github.com/user-attachments/assets/e5e34697-ec04-4979-8383-d95ed767b0d9)


**Rule Actions:**
- Decide where the alert should be sent:
  - Options include Jira tickets, Slack, Microsoft Teams, general webhooks, etc.
  - For this example, choose Email Notifications.

4. Enable the rule and test it by running an Nmap scan.

Rule actions are also good because it decides where the alert will actually go like it can become a jira ticket or send and notify everyone on slack, Microsoft teams, etc or even a general webhook if we’re getting into apis etc. this is powerful because it allows for more automation and orchestration . I will just choose it to be sent as an email.  
![image](https://github.com/user-attachments/assets/ef822bfb-83c6-493a-b493-8227b484e30a)
![image](https://github.com/user-attachments/assets/17f3d6b7-3eff-4e04-bb82-805f3cb0af91)
 create and enable rule  
![image](https://github.com/user-attachments/assets/a1cfa7b9-9639-47be-838f-912caa79e9ac)
now it’s all ready and it’s an alert called nmap scan and it’s now one of our rules. Let’s try to fire it off and see if we get that email
![image](https://github.com/user-attachments/assets/4ed43d3e-a7fa-4b09-b438-eb0983b13395)
created another visualization bar graph

Now at this point, I had realized by going here and checking the details that I was **not** getting the alerts so there was an issue with my rule. 

**Debugging and Fixing Nmap Rule**:
- Initially, I wasn’t receiving alerts. After inspecting the rule, I realized:
  - The `event.action` of the logs was set to `end`, whereas my rule had it as `nmap`.
  - However, `process.args` contained `nmap`.
 Here are the pictures from the logs
![image](https://github.com/user-attachments/assets/a36f9562-1406-43b9-9f8a-5f0ea0e31d5d)
![image](https://github.com/user-attachments/assets/655c89ff-def7-4ea9-b377-37cd1c54dedd)
![image](https://github.com/user-attachments/assets/d7307c6e-1e36-4648-ba88-b0ae8a25e1d0)
As we can see the event.action is specified as end AND the event.type is end too. However, process.args has nmap for example so I will fix my rule. 


**To fix this, I updated the rule to:**
```plaintext
process.name: "nmap"
```
- This resolved the issue, and I received email alerts successfully.
![image](https://github.com/user-attachments/assets/5c7bbb46-e963-4c48-8ba0-19585ed9483d)
Now my rule will detect process_name: “nmap”
And now it works:
![image](https://github.com/user-attachments/assets/9981e4c0-8828-456a-983c-1660d7420b72)

![image](https://github.com/user-attachments/assets/ac81f7b4-6baa-48e3-ab8f-e3432da69fa4)
and I have received it as an email 
![image](https://github.com/user-attachments/assets/2c68b41f-d8f4-4616-9a41-133271206b73)
when I select view rule in kibana it takes me back to here
![image](https://github.com/user-attachments/assets/531a7a7d-d214-4553-a3c7-35892e576843)
Up until now, This lab is more like a soc analyst skill, the EDR lab I made before in my other repo is more incident response, So now I am happy to have played around with elastic stack and with kibana and created alerts and detections and developed these skills a bit more.


---

#### Extending Detection to Hydra Password Cracking
Now an extension is I have decided to also try adding hydra password cracking 
**Step 1: Extract the `rockyou.txt` wordlist:**
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```
![image](https://github.com/user-attachments/assets/05c3516f-579a-4165-b0dc-4238db5902a2)
So I used rockyou.txt here I extracted it using sudo gunzip (filepath/rockyou.txt.gz)
![image](https://github.com/user-attachments/assets/c67ccb3a-182c-4b1f-abff-bc085cb0b7b6)
This error occurs because Hydra is sending too many requests too quickly, and the SSH server is rejecting them so The default number of parallel tasks (-t) is too high for most SSH servers. Reduced it to a safer level as seen here 

**Step 2: Configure SSH to handle Hydra scans more effectively:**
1. Edit `/etc/ssh/sshd_config` and modify these parameters:
```plaintext
MaxAuthTries 10
MaxSessions 10
LoginGraceTime 120
```
**Explanations:**
- `MaxAuthTries`: Increases the maximum number of failed login attempts before the connection is dropped.
- `MaxSessions`: Allows more concurrent SSH sessions.
- `LoginGraceTime`: Extends the time before the server disconnects idle login attempts.

2. Restart SSH to apply changes:
```bash
sudo systemctl restart ssh
```
![image](https://github.com/user-attachments/assets/ca425a94-260c-47b0-94e1-1e3cff21b3dc)


**Step 3: Run a Hydra command:**
```bash
hydra -l username -P /usr/share/wordlists/rockyou.txt -t 1 -w 3 ssh://127.0.0.1
```
- `-l username`: Specifies the username.
- `-P rockyou.txt`: Specifies the password list.
- `-t 1`: Limits to one connection at a time to avoid being blocked.
- `-w 3`: Waits 3 seconds between attempts to reduce server load.
- `ssh://127.0.0.1`: Targets the SSH service on localhost.

then used sudo systemctl status elastic-agent.service to make sure it’s running still
![image](https://github.com/user-attachments/assets/c17e3ad8-f262-4368-b32e-cf51c673f87b)
![image](https://github.com/user-attachments/assets/ac37e714-3b1f-4fbb-8bda-6c63174d7212)
Now doing the same thing here to avoid a mistake and seeing what is recognized here and we see that process.args is hydra: 
![image](https://github.com/user-attachments/assets/483f27aa-62f2-4a41-b1b9-3daa0d980741)
![image](https://github.com/user-attachments/assets/09e22418-17f9-41ed-8253-c071fee57c4a)


---

#### Creating Hydra Detection Rule

**Visualization:**
- Create a graph that highlights Hydra activity in `process.args`.

**Rule Details:**
**Query:**
```plaintext
process.name: "hydra" AND (process.args: "ssh://127.0.0.1" OR process.command_line: "hydra -l")
```
- Ensures the detection rule matches events where Hydra is the process name.
- Matches commands targeting SSH on localhost and adds flexibility to detect additional Hydra commands.
![image](https://github.com/user-attachments/assets/b2ba6a41-8a2f-40bd-b850-c6740af7568a)

**Rule Name and Description:**
- **Name:** `hydra ssh bruteforce`
- **Description:** Detects Hydra activity targeting SSH on localhost.
![image](https://github.com/user-attachments/assets/5780b26b-268a-4509-a657-fe1a244293fc)

And this time I will use a webhook instead 
**Webhook Configuration:**
- **Authentication:** None (Webhook.site doesn’t require authentication for free-tier URLs).
- **Body:**
```json
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
```

![image](https://github.com/user-attachments/assets/9f30c801-c70c-4743-9c31-eb4f69e463b6)

---

#### Testing the Hydra Rule

- Running the Hydra command triggered alerts in the system. The detection rule successfully identified:
  - Process name: `hydra`.
  - Arguments targeting SSH on localhost.

---![image](https://github.com/user-attachments/assets/484e798a-a1af-401e-b978-fc6c265f8575)
![image](https://github.com/user-attachments/assets/bf62d9cb-5d05-4294-8fb9-d9b03c230eec)
and our alert here has detected the hydra ssh bruteforce so our detection rule has worked 
![image](https://github.com/user-attachments/assets/b6f14a21-2490-4053-9037-b65f17b9510e)
Here we have the alerts page, severity levels (I should change the hydra to not be low, but right now all 110 alerts are low due to them being the nmap and hydra being set to low) and alerts by name are shown and top alerts (100%) are by the host.name kali

#### Alerts and Severity Levels

- Initially, both Nmap and Hydra rules were set to low severity. After testing, the Hydra rule was updated to:
  - **Severity:** Medium
  - **Risk Score:** 47
![image](https://github.com/user-attachments/assets/c1d4fae0-a20b-4af0-b580-7f56066b7d72)
However, realistically, I would change this severity to high since it means there is an active attack.
---

#### Execution Logs

- The execution log shows successful statuses for both manual and scheduled executions of the Hydra SSH brute-force rule. This verifies the detection and alerting functionality.
![image](https://github.com/user-attachments/assets/c372fea3-c518-49f3-b859-78998d22f623)

---

#### Conclusion

With these setups, the Elastic Stack was effectively used to:
- Detect and respond to Nmap scans and Hydra brute-force attacks.
- Automate incident response by sending alerts to email and webhooks.

Now we have successfully also included incident response skills, not just SOC analyst skills, with the addition of detecting and creating an alert for hydra’s ssh password bruteforce and responding by sending an alert to the webhook. 

