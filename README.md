# Send LogicMonitor alerts to xMatters to help you find resolvers faster.
Automatically notify the appropriate on call resources on their preferred device when alert thresholds are met.
This integration helps you initiate xMatters notifications for LogicMonitor alerts.

<kbd>
<a href="https://support.xmatters.com/hc/en-us/community/topics">
   <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</a>
</kbd>

# Pre-Requisites
* LogicMonitor (https://www.logicmonitor.com)
   * You must have a [collector installed](https://www.logicmonitor.com/support/settings/collectors/about-the-logicmonitor-collector/)
   * You must have [devices added and configured](https://www.logicmonitor.com/support/getting-started/i-just-signed-up-for-logicmonitor-now-what/4-adding-devices/)
   * You must add a Custom Property for the device defining the xMatters Group to target.
* xMatters account - If you don't have one, [get one](https://www.xmatters.com)!



# Files
* [LogicMonitor Workflow](LogicMonitor.zip)



# How it works
Integrating xMatters with LogicMonitor allows you to automatically transfer key LogicMonitor alert data to xMatters alerts and drive workflows forward.

You can configure any alert rule in LogicMonitor to use the xMatters integration. When such a rule fires, it will trigger an API call into the xMatters Inbound Integration specified by this integration. The integration script then parses out the payload and builds an event and passes that to xMatters.

xMatters will update the LogicMonitor notes field throughout the incident life cycle.
Because the notes field in LogicMonitor does not allow multiple values, this integration will append additional details with each update.

__xMatters will update the notes field when:__
- Notifications are created in xMatters.
- A comment is added to xMatters alert.
- A response (Escalate or Acknowledge) is made to the xMatters alert.

When a user responds with _Acknowledged_, an API call will be made to LogicMonitor to update the notes and mark the alert at Acknowledged.

When an alert is resolved in LogicMonitor the corresponding xMatters alert will be terminated.

When a note is added in xMatters it will be appended to the LogicMonitor Alert Notes field.



# Installation

## Create an xMatters Integration User
This integration requires a user who can authenticate REST web service calls when injecting events to xMatters.

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




<br><br>
## Import and Configure the xMatters Workflow

The next step is to import the Workflow.

To import the Workflow:

1. In the target xMatters system, on the __Developer__ tab, click __Import Plan__.
2. Click __Browse__, and then locate the downloaded Workflow: [xMatters LogicMonitor Workflow](LogicMonitor.zip)
3. Click __Import Plan__.
4. Once the Workflow has been imported, click __Plan Disabled__ to enable the plan.
5. In the __Edit__ drop-down list, select __Access Permissions__.
6. Add the __LogicMonitor_API_User__ or Make the Plan __Accessible by All__.
7. __Save Changes__.
5. In the __Edit__ drop-down list, select __Forms__.

6. For the __On-Call Resource Alert__ form, in the __Not Deployed__ drop-down list, click __Enable for Web Service__.
7. After you Enable for Web Service, the drop-down list label will change to __Web Service__.
8. In the __Web Service__ drop-down list, click __Sender Permissions__.
9. Add the __LogicMonitor_API_User__ you created above, and then click __Save Changes__.






<br><br>
### Set xMatters Constants

There are two constants that must be configured in the LogicMonitor xMatters Workflow.
1. In the target xMatters system go to the __Developer__ tab.
2. Beside the LogicMonitor Workflow click the __Edit__ drop-down list, select __Integration Builder__.
3. Click __Edit Constants__.
4. Set __LogicMonitor Access ID__. [Instructions](#get-logicmonitor-access-id)
5. Set __LogicMonitor Access Key__. [Instructions](#get-logicmonitor-api-token)

LogicMonitor Instructions:
[LogicMonitor Access ID](https://www.logicmonitor.com/support/settings/users-and-roles/api-tokens/)
[LogicMonitor API Token](https://www.logicmonitor.com/support/settings/users-and-roles/users/#apitokens)





<br><br>
## Configure the LogicMonitor Endpoint

This will set the authentication parameters required to make API requests into LogicMonitor.

1. From inside the __LogicMonitor Workflow__ go to the __Integration Builder__ tab.
2. Click __Edit Endpoints__.
3. Click on the __LogicMonitor__ endpoint.
4. Set Base URL. This should be the url of your LogicMonitor environment followed by __/santaba/rest__.
   Example: __https://company.logicmonitor.com/santaba/rest__
5. Set Authorization Type to __None__.
   - Authentication is done with in each API calls.
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
6. Click the Import button.  Browse for the included LM-xMatters-Integration.json file.
7. Adjust the URL use the xMatters Inbound Integration Endpoint URL copied from LogicMonitor Alert Inbound Integration. Make sure to remove the https:// from the beginning as this was copied from xMatters and should be set in the drop down not the path.
8. A Username and Password are not required for URL Authentication. If you want to use Basic Authentication please enter your username and password.


   __xmatters.group__ is a LogicMonitor Custom Device Property you must include on each device that will be configured to create xMatters alerts.  The property can be configured directly on the device or inherited from a parent group. This property defines the Recipient Group to target in xMatters. [Add Custom Device Parameters](#add-custom-device-property)


9. Click __Test Alert Delivery__ and wait for a response.
10. Click __Save__ if the test is successful.


   <kbd>
      <img src="/media/LogicIntegration.png">
    </kbd>




<br><br>
### Get LogicMonitor API Token

API tokens are created on a per user basis from the Manage Users Dialog within LogicMonitor.
https://www.logicmonitor.com/support/settings/users-and-roles/users/#apitokens

After adding a LogicMonitor API Token make sure to copy it as it will only be displayed once.

When creating a user, you may want to use the __API Only Access__ toggle. This will create a user that can only access LogicMonitor for the purposes of this integration.

1. Create User for LogicMonitor Integration. Make sure it has appropriate LogicMonitor roles / permissions.
2. Copy LogicMoitor API Token.
3. Set xMatters Constant. [LogicMonitor Access Key](#set-xmatters-constants)


   <kbd>
      <img src="/media/Userspng.png">
    </kbd>




<br><br>
### Get LogicMonitor Access ID

In LogicMonitor go to the __Settings > API Tokens__ Tab.
Here you will see the Access ID of the API token you just created.

https://www.logicmonitor.com/support/settings/users-and-roles/api-tokens/

1. Copy appropriate LogicMoitor Access ID.
2. Set xMatters Constant. [LogicMonitor Access ID](#set-xmatters-constants)


   <kbd>
      <img src="/media/AccessID.png">
    </kbd>




<br><br>
### Add LogicMonitor Recipient Groups

1. Go to Settings.
2. Go to Recipient Groups.
3. Click Add button.
4. Name the Group.
5. Add a new recipient.
   - The selected recipient does not matter.
6. Set Contact Method to __xMatters Integration(http)__.
   - This is the name of the xMatters integration you created in the last step: [Configure LogicMonitor](#configure-logicmonitor-integration)
7. Save Recipient.
8. Save Group.
9. Repeat for additional Groups.

    <kbd>
      <img src="/media/logicGroup.png">
    </kbd>

More info on creating [LogicMonitor Recipient Groups](https://www.logicmonitor.com/support/settings/alert-settings/recipient-groups/)






<br><br>
###  Create LogicMonitor Escalation Chain

1. Go to Settings.
2. Go to Escalation Chains.
3. Click Add button.
4. Name the Escalation Chain.
5. Optional - Configure Rate Limiting.
6. Add Recipient. The recipient should be a group that you created in last step: [Add LogicMonitor Recipient Groups](#add-logicmonitor-recipient-groups)
7. Save Escalation Chain.

    <kbd>
      <img src="/media/Escalationchain.png">
    </kbd>

More info on creating [LogicMonitor Escalation Chains](https://www.logicmonitor.com/support/settings/alert-settings/escalation-chains/)






<br><br>
###  Create LogicMonitor Alert Rule

1. Go to Settings.
2. Go to Alert Rules.
3. Click Add button.

Use this resource to help you configure your alert rule.
https://www.logicmonitor.com/support/settings/alert-settings/alert-rules/


The most important thing you must do here is to configure the __Escalation Chain__.

- Select the escalation chain you just created in the last step: [Create LogicMonitor Escalation Chain](#create-logicmonitor-escalation-chain)

4. Save Alert Rule.

    <kbd>
      <img src="/media/Alert-Rule.png">
    </kbd>

5. Add additional Alerts for each Data source / Alert Level that you want to notify with xMatters.

More info on creating [LogicMonitor Alert Riles](https://www.logicmonitor.com/support/settings/alert-settings/alert-rules/)






<br><br>
###  Add Custom Device Property
The property can be configured directly on the device or inherited from a parent group.

To add to devices directly:
1. Go to Resources.
2. Select a Device configured to target the xMatters Integration.
4. Go to Info Tab.
5. Click the Cog beside Custom Properties.
6. Add the following Custom Property:

Name: xmatters.group
Value: {{Name of xMatters Group to Notify}}

7. Repeat this step for each device you want to notify with xMatters.


  <kbd>
      <img src="/media/DeviceProperties.png">
    </kbd>

_The value that you set here must match the name of a group in xMatters._
_If this value does not match the name of a group in xMatters your notification will not go to anyone._

More info on creating [Device Properties](https://www.logicmonitor.com/support/devices/adding-managing-devices/device-properties)





<br><br>
## Testing and Troubleshooting

Go LogicMonitor Integration. Follow instructions here: [Configure LogicMonitor](#configure-logicmonitor-integration)
- Click __Test Alert Delivery__.

Trigger a new LogicMonitor Alert check that it makes its way into xMatters.

You can check the Inbound Integration Activity Log in xMatters:
https://help.xmatters.com/ondemand/xmodwelcome/integrationbuilder/create-inbound-updates.htm
