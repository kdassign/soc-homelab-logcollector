# SOC Homelab: Ubuntu Log Collection & Security Monitoring

This is a Security Operations Center (SOC) homelab that demonstrates enterprise style log collections from Ubuntu to Security Onion with real-time Kibana dashboards for security monitoring. 

## Technologies Used
- Filebeat 8.11.0 (Log collection)
- Security Onion 2.4 (Siem Platform)
- Elasticsearch (Log storage and search)
- Kibana 8.18.6 (Data visualization)
- Ubuntu 22.04 (Log Source)

## Architecture
![Archictecture Diagram](images/architecture.drawio.png)

## Quick Start Guide
### Prerequisites
- Security Onion deployed
- Ubuntu 
- Network connectivity between systems

### Install Filebeat
sudo apt update && sudo apt install filebeat

## Project Walkthrough

### Authentication Journey

I configured the filebeat.yml file. I first tried to generate an API key in order for filebeat to be able to write indices to Kibana, but kept being met with a 401 Unauthorized error code. Though I did try and troubleshoot this issue, I could not figure it out, so I decided to use a username and password in order to be authenticated. 

You can see how I ran into issues and dealt with them here:![Troubleshooting](configs/filebeat-troubleshooting.md)

### Solution: Username/Password Authentication

In Kibana I created a user. I gave the user permissions to manage beats, log parsing, and access to cluster monitoring features in order to practice using least-privileges.
![Kibana User Creation](images/filebeat_user.png)

I next edited the filebeat.yml file with the required credentials for authentication: 
![Filebeat.yml](examples/filebeat.yml.example)

I used *sudo filebeat test config* to test the configuration file and check for any syntax issues. There were none. 
![Config](/images/filebeat_test_config.png)

After fixing this, I used *sudo filebeat test output* to verify that filebeat can actually connect and authenticate with Security Onion and it worked, for the most part.

I was then met with a 403 forbidden error code, which meant that though the user was authenticated, the server refused to authorize the specific action or access to the requested source. I realized that the user needed write privileges so I created a writer role and assigned that role the following privileges before assigning it to the user:
![writer](/images/writer_permissions.png)

I used *sudo filebeat test output* again and this time there were no issues.
![Output](/images/filebeat_test_output.png)

### Service Startup & Verification
After successful authentication, I started Filebeat and verified the service: 

*sudo systemctl start filebeat*
- starts the filebeat service

*sudo systemctl status filebeat*
![Filebeat_status](/images/filebeat_status.png)

### Kibana Dashboard Creation
I then went to the IP address associated with Security Onion and then accessed Kibana after I logged in. This is what my Kibana dashboard looks like: 
![Kibana Dashboard](/images/Kibana_dashboard.png)
- Log Volume Time - Track log traffic patterns over time
- Authentication Activity - Monitor login attempts and sudo usage
- Real-time Log Stream - live view of all incoming logs
- System Health Metrics - Overview of logging system performance 

### Final Test: Logging
Now to verify that filebeat is able to send logs to Elastic search which in turn allows Kibana to read from Elasticsearch, I use: 
*sudo logger "INSERT_TEST_MESSAGE"*

Once I get back to Kibana I check Discover under the Analytics tab and see this:
![Log_Output](/images/logger_message.png)

And thats it. Now for key takeaways.

## Key Takeaways

### 1. Authentication Simplicity Over Complexity
- There will be a time for API keys, but sometimes simpler solutions work better in homelab environments. Still fun to challenge myself though and I should!

### 2. RBAC is Non-Negotiable!
- This is the first time that I'm actually getting hands-on experience with authentication and authorization. At least to this extent. 
- Principle of Least Privilege applies even in lab enviroments. 
- Service accounts need precise permissions, not just broad access. To read, write, or both?

### 3. Homelab vs Enterprise 
- 'ssl.verification_mode: none' is great for lab simplicity, but in an enterprise environment it'd not be acceptable.

## Skills Demonstrated 
- **SIEM Deployment & Configuration**
- **Log Collection**
- **RBAC & Security Principiles**
- **Enterprise Monitoring Patterms**

## Whats Next?
- Add Windows log collection
- Network security monitoring
- Custom detection rules
- Automated alerting