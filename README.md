# Step by Step Process that you need to follow for this INTEGRATION

step 1. Create a script in  /var/ossec/etc/lists/(new folder)/fetch_abuseipdb_blacklist.sh        (Note: Do changes in script like api key)

Step2: Give exicutable permission permission to the script 

       chmod 777 fetch_abuseipdb_blacklist.sh

       Before run this script need to install " sudo yum install jq "  # For CentOS/RHEL-based systems


Step 3: After that go to /var/ossec/etc/lists/ and check for newly created "abuseipdb_blacklist" list.

Step 4: After creating the list, we need to tell Wazuh to load this list. Wazuh will generate the .cdb file on the fly for us.

       nano /var/ossec/etc/ossec.conf


       Find the <ruleset> section and add our new list.


       <list>etc/lists/abuseipdb_blacklist</list>


Step 5: systemctl restart wazuh-manager
