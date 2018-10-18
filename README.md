# Send LogicMonitor alerts to xMatters to help you find resolvers faster.
Automatically notify the appropriate on call resources on their preferred device when alert thresholds are met.
This integration helps you initiate xMatters notifications for LogicMonitor alerts.

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

# Pre-Requisites
* LogicMonitor (https://www.logicmonitor.com)
	* You must have a [collector installed](https://www.logicmonitor.com/support/settings/collectors/about-the-logicmonitor-collector/)
	* You must have [devices added and configured](https://www.logicmonitor.com/support/getting-started/i-just-signed-up-for-logicmonitor-now-what/4-adding-devices/)
* xMatters account - If you don't have one, [get one](https://www.xmatters.com)!



# Files
* [LogicMonitor Communication Plan](xMattersPlan/LogicMonitor.zip)



# How it works
Integrating xMatters with your other tools allows you to automatically transfer key LogicMonitor alert data throughout your systems and drive workflows forward. Notifications and collaboration invites embedded with LogicMonitor insights allow your resolution teams to take immediate action.

You can configure any alert rule in LogicMonitor to use the xMatters integration. When such a rule fires, it will trigger an API call into the xMatters Inbound Integration specified by this integration. The integration script then parses out the payload and builds an event and passes that to xMatters.

xMatters will update the LogicMonitor notes field throughout the incident life cycle.
Because the notes field in LogicMonitor does not allow multiple values, this integration will append additional details with each update.

__xMatters will update the notes field when:__
- Notifications are created in xMatters.
- A comment is added to xMatters alert.
- A response is made to the xMatters alert.

When a user responds with _Acknowledged_, an API call will be made to LogicMonitor to update the notes and mark the alert at Acknowledged.

When an alert is resolved in LogicMonitor the corresponding xMatters alert will be terminated.



# Installation

## Create an xMatters Integration User
This integration requires a user who can authenticate REST web service calls when injecting events.

This user needs to be able to work with events, but does not need to update administrative settings. While you can use the default Company Supervisor role to authenticate REST web service calls, the best method is to create a user specifically for this integration with the "REST Web Service User" role that includes the permissions and capabilities.

__Note__: If you are installing this integration into an xMatters trial instance, you don't need to create a new user. Instead, locate the "Integration User" sample user that was automatically configured with the REST Web Service User role when your instance was created and assign them a new password. You can then skip ahead to the next section.

__To create an integration user:__

1. Log in to the target xMatters system.
2. On the __Users__ tab, click the __Add New User__ icon.
3. Enter the appropriate information for your new user. 
    Example User Name __LogicMonitor_API_User__
4. Assign the user the __REST Web Service User__ role.
5. Click __Save__.

Make a note of the user name and password that you set; you will need them when configuring other parts of this integration.

The integration user name and password will be needed when configuring the Twilio xm_settings Function: 
__xm_user__ and __xm_pass__




<br><br>
## Import and Configure the xMatters Communication Plan

The next step is to import the communication plan.

To import the communication plan:

1. In the target xMatters system, on the __Developer__ tab, click __Import Plan__.
2. Click __Browse__, and then locate the downloaded communication plan: [xMatters Initiate Event via Phone Call Communication Plan](xMattersPlan/LogicMonitor.zip)
3. Click __Import Plan__.
4. Once the communication plan has been imported, click __Plan Disabled__ to enable the plan.
5. In the __Edit__ drop-down list, select __Access Permissions__.
6. Add the __LogicMonitor_API_User__ or Make the Plan __Accessible by All__.
7. __Save Changes__.
5. In the __Edit__ drop-down list, select __Forms__.

6. For the __On-Call Resource Alert__ form, in the __Not Deployed__ drop-down list, click __Enable for Web Service__.
7. After you Enable for Web Service, the drop-down list label will change to __Web Service__.
8. In the __Web Service__ drop-down list, click __Sender Permissions__.
9. Add the __LogicMonitor_API_User__ you created above, and then click __Save Changes__.




<br><br>
## Configure the LogicMonitor Endpoint

This will set the authentication parameters required to make API requests into LogicMonitor.

1. From inside the __LogicMonitor communicaiton plan__ go to the __Integration Builder__ tab.
2. Click __Edit Endpoints__.
3. Click on the __LogicMonitor__ endpoint.
4. Set Base URL. This should be the url of your LogicMonitor environment followed by __/santaba/rest__.
	Example: __https://<company>.logicmonitor.com/santaba/rest__
5. Configure Authentication Method.
	- You will need to create a LogicMonitor user who has the ability to make API calls into your system.
	- Learn more here: https://www.logicmonitor.com/support/rest-api-developers-guide/overview/using-logicmonitors-rest-api/
6. Ensure __Preemptive__ is checked.
7. Save Changes.




<br><br>
## Get the xMatters Inbound Integration Endpoint

1. In the __Edit__ drop down list on the  _LogicMonitor Communication Template_ Click __Integration Builder__.
2. On the Integration Builder tab, expand the list of inbound integrations.
3. Click on  __LogicMonitor Alerts__.
4. Ensure __URL Authentication__ is set.
5. In the __How to trigger this integration__ section, set the __Authentication User__ to __LogicMonitor_API_User__.

  You must supervise this user and it must have a Web Service User Role. If you cannot select the __LogicMonitor_API_User__ if it because you do not supervise that user or they do not have the REST Web Service User role.

6. Copy the URL listed under the __Trigger__ section.

	- We will need this url when configure the xMatters Integration inside LogicMonitor




<br><br>
## Configure LogicMonitor Integration

1. Login to LogicMonitor.
2. Go to Settings.
3. Go to Integrations.
4. Click __Add__ Integration button.
5. Select __Custom HTTP Delivery__.
6. Give the Integration a name. 
	Ex: _xMatters Integration_.
7. Give the Integration a Description. 
	Ex: _Send Critical Alerts to xMatters_.
8. Select __Use different URLs or data formats to notify on various alert activity__.
9. Click the + add button to create a new integration.
10. Check "New Alert", "Acknowledge", "Cleared".
11. Set HTTP Method: HTTP Post.
12. Set URL to __HTTPS://__ and put the xMatters Inbound Integration Endpoint URL copied from LogicMonitor Alert Inbound Integration. Make sure to remove the https:// from the beginning.
14. A Username and Password are not required for URL Authentication. If you want to use Basic Authentication please enter your username 
15. Set __Alert Data__ to __Raw__.
16. Set Format to JSON.
17. Copy and Past the following JSON raw body.

  ```js
	{
	"alertid":"##ALERTID##",
	"requestId":"##EXTERNALTICKETID##",
	"type":"##ALERTTYPE##",
	"status":"##ALERTSTATUS##",
	"message":"##MESSAGE##",
	"description": "Logicmonitor ##Level## Alert",
	"service_url": "##URL##",
	"service_description":"##SERVICEDESCRIPTION##",
	"checkpoint": "##CHECKPOINT##",
	"service_group":"##SERVICEGROUP##",
	"logicmonitor_url":"##AlertDetailURL##",
	"level":"##LEVEL##",
	"host":"##HOST##",
	"datasource":"##DATASOURCE##",
	"eventsource":"##EVENTSOURCE##",
	"batchjob":"##BATCHJOB##",
	"group":"##HOSTGROUP##",
	"datapoint":"##DATAPOINT##",
	"start":"##START##",
	"finish":"##FINISH##",
	"duration":"##DURATION##",
	"value":"##VALUE##",
	"threshold":"##THRESHOLD##",
	"userdata":"##USERDATA##",
	"cmdline":"##CMDLINE##",
	"exitCode":"##EXITCODE##",
	"stdout":"##STDOUT##",
	"stderr":"##STDERR##"
	}
  ```
18. Click __Test Alert Delivery__ and wait for a response.
19. Click __Save__ if the test is successful.






<br><br>
### Add LogicMonitor Recipient Groups

1. Go to Settings.
2. Go to Recipient Groups.
3. Click Add button.
4. Name the group. Group names should match the group names in xMatters. This will be used to target xMatters groups.
5. Add a new recipient.
	The selected recipient does not matter but you may want to chose one with the purpose of this integration.
6. Set Contact Method to __xMatters Integration(http)__.
	- This is the name of the xMatters integration here: [Configure LogicMonitor](#configure-logicmonitor)
7. Save Recipient.
8. Save Group.

    <kbd>
      <img src="/media/logicGroup.png">
    </kbd>

More info on creating LogicMonitor Groups:
https://www.logicmonitor.com/support/settings/alert-settings/recipient-groups/






<br><br>
###  Create LogicMonitor Escalation Chain

1. Go to Settings.
2. Go to Escalation Chains.
3. Click Add button.
4. Name the Escalation Chain. You may want to name this after your Group Name.
5. Optional - Configure Rate Limiting
6. Add Recipient. The recipient should be a group that you created [Add LogicMonitor Recipient Groups](#add-logicmonitor-recipient-groups)
7. Save Escalation Chain.

    <kbd>
      <img src="/media/Escalationchain.png">
    </kbd>

More info on creating LogicMonitor Escalation Chains:
https://www.logicmonitor.com/support/settings/alert-settings/escalation-chains/






<br><br>
###  Create LogicMonitor Alert Rule

1. Go to Settings.
2. Go to Alert Rules.
3. Click Add button.

Use this resource to help you configure your alert rule.
https://www.logicmonitor.com/support/settings/alert-settings/alert-rules/


The most important thing you must do here is to configure the __Escalation Chain__. 

- Select the escalation chain you just created in [Create LogicMonitor Escalation Chain](#create-logicmonitor-escalation-chain)

4. Save Alert Rule.

    <kbd>
      <img src="/media/Alert-Rule.png">
    </kbd>

More info on creating LogicMonitor Groups:
https://www.logicmonitor.com/support/settings/alert-settings/alert-rules/






<br><br>
## Test the Integration.

1. Call your Twilio number. 

2. Test your configuration and all of the call prompts making sure it works as expected.






<br><br>
# Troubleshooting
Alternatively, you can check the Inbound Integration Activity Log in xMatters:
https://help.xmatters.com/ondemand/xmodwelcome/integrationbuilder/create-inbound-updates.htm


