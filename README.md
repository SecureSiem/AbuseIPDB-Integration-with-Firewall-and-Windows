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

# Now here follow for Fortigate firewall with wazuh detection.

here we target the "dstip" and "srcip"

1. Just add these rules in place,
          nano /var/ossec/etc/rules/local_rules.xml

          <!-- Abnormal Destination IP Detection by AbuseIPDB list -->
              <group name="fortigate, syslog, local, sshd">
               <rule id="101103" level="12">
                 <options>alert_by_email</options>
                 <if_group>fortigate</if_group>
                 <list field="dstip" lookup="address_match_key">etc/lists/abuseipdb_blacklist</list>
                 <description>FortiGate: Destination IP is in the AbuseIPDB blacklist. $(msg)</description>
                 <group>fortigate,</group>
               </rule>
              </group>


          <!-- Abnormal Source IP Detection by AbuseIPDB list -->
              <group name="fortigate, syslog, local, sshd, authentication_failed, invalid_login">
               <rule id="101104" level="12">
                 <options>alert_by_email</options>
                 <if_group>fortigate</if_group>
                 <list field="srcip" lookup="address_match_key">etc/lists/abuseipdb_blacklist</list>
                 <description>FortiGate: Destination IP is in the AbuseIPDB blacklist. $(msg)</description>
                 <group>fortigate,local,</group>
               </rule>
              </group>


# Follow here for detecion with Windows and Wazuh

              <!-- Abnormal Destination IP Detection by AbuseIPDB list -->
              <group name="windows, sysmon, sysmon_event3">
              <rule id="101103" level="12">
                <if_group>sysmon_event3</if_group>
                <list field="win.eventdata.destinationIp" lookup="address_match_key">etc/lists/abuseipdb_blacklist</list>
                <description>Sysmon - Event 3: Network connection to a suspicious IP from the AbuseIPDB blacklist by $(win.eventdata.image)</$
                <group>sysmon_event3,</group>
              </rule>
              </group>


              sudo systemctl restart wazuh-manager
# NOTE: For windows detection you have to enabled Sysmon events.

# To do auto update suspicious ip list from abuseIPDB we need to set crontab.

in terminal :

              crontab -e


              0 11 * * * /path/to/your/script.sh

Example: 

              0 11 * * * /var/ossec/etc/lists/AbuseIPDB/fetch_abuseipdb_blacklist.sh
Save and exit the editor.
If you're using nano, press Ctrl + X, then press Y to confirm, and Enter to save the changes.
If you're using vim, press Esc, then type :wq and hit Enter to save and exit.

# Note. in this we set auto run our fetch_abuseipdb_blacklist.sh script at 11:00 AM on Daily basis.


# For Office365 failed Login attempts -

        
       <group name="office365, office365, AzureActiveDirectoryStsLogon">
         <rule id="101105" level="12">
           <options>alert_by_email</options>
           <if_group>office365</if_group>
           <list field="office365.ClientIP" lookup="address_match_key">etc/lists/abuseipdb_blacklist</list>
           <description>Office365: Client IP matches AbuseIPDB blacklist. $(office365.UserId) operation $(office365.Operation)</des>
           <group>office365,AzureActiveDirectoryStsLogon,</group>
         </rule>
       </group>
