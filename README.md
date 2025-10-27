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

I was then met with a 403 forbidden error code, which meant that though the user was authenticated, but the server refused to authorize the specific action or access to the requested source. I realized that the user needed write privileges so I created a writer role and assigned that role the following privileges before assigning it to the user:
![writer](/images/writer_permissions.png)

I used *sudo filebeat test output* again and this time there were no issues.
![Output](/images/filebeat_test_output.png)


