## Common Issues:
1. **401 Unauthorized**: Web request lacked proper authentication credentials
        **My Solutions**
            - Tried API key, encountered persistent issues
            - Created user in Kibana, used username and password authentication, worked.
2. **403 Forbidden**: Missing index privileges 
        **My Solutions**
             - Had to add write privileges to Kibana user in order to write to indices.
             - Write privileges: ('create_doc', 'create index', 'write')
3. **No logs in Kibana**: Wait 2 minutes, check filebeat status
        **My Solutions**
            - Verify with: 'sudo filebeat test output'
            - Check service status: 'sudo systemctl status filebeat'
            - Geneerate test logs: 'sudo logger "INSERT_TEST_MESSAGE"
4. **Certificate errors**: Use ssl.verification_mode: none in labs
            - Used for purpose of lab setup
            - In enterprise environments, make sure to get the proper certificates and have them validated!!

## Diagnostic Commands:
sudo filebeat test output
sudo filebeat test config
sudo systemctl status filebeat
sudo tail -f /var/log/filebeat/filebeat

