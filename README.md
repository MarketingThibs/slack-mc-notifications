# Send Slack Notifications From Marketing Cloud

You can use Slack [**Incoming Webhooks**](https://api.slack.com/incoming-webhooks) into a script automation, a landing page or email to send a notification to one of your Slack channel. 

For example, setting reminders addressed to MC users or send a notification with a data extension records counts...

**Steps:**

1. Setup Slack's Incoming Webhooks
2. Setup an AMPscript block
3. Setup a script automation (if not using it from email or landing page)



## Setup Slack's Incoming Webhooks

You can refere to Slack's [Incoming Webhooks documentation](https://api.slack.com/incoming-webhooks). You will need to:

1. [Create a Slack app](https://api.slack.com/apps/new) 
2. Enable Incoming Webhooks
   if you already have an app, you can [edit your apps page](https://api.slack.com/apps) directly
3. Create an Incoming Webhook

![slack-example-notif-1](slack-example-notif-1.png)

> :bulb: you can use one of the nice [SLDS icons](https://lightningdesignsystem.com/) to make to app friendly with [Einstein](https://cdnjs.cloudflare.com/ajax/libs/design-system/2.9.3/images/einstein-headers/einstein-figure.svg) for instance





## Setup an AMPscript block

The AMPscript code block can be used in : an email, a landing page, then a script activity. In this example, we will use it into a script activity, but this would work with the other versions. We will use the [`TreatAsContent`](https://developer.salesforce.com/docs/atlas.en-us.noversion.mc-programmatic-content.meta/mc-programmatic-content/treatascontent.htm) and  [`HTTPPost2`](https://developer.salesforce.com/docs/atlas.en-us.noversion.mc-programmatic-content.meta/mc-programmatic-content/httppost2.htm) functions.

### SIMPLE EXAMPLE

```vbscript
%%[
set @url = "https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX"
/* "mrkdwn" parameter enables the slack markdown option */
set @contentLoad1 = '{"attachments": [{"title": "Msg title :smiley code:","mrkdwn": true,"text": "'
set @slackMsg = '*Bold Text*
- Some text here: %%=DataExtensionRowCount("MyDe")=%%

*OTHER BOLD TEXT*
- Some other text here: %%=DataExtensionRowCount("MyDe2")=%%'
/* "Color" of the attachment line*/
set @contentLoad2 = '","color": "#7CD197"}]}'
/* prepare the payload for the HTTPPOST */
set @contentLoad = TreatAsContent(concat(@contentLoad1,@slackMsg,@contentLoad2))
var @statusCode
var @response
  /* Posts the message*/
set @post = HTTPPost2(@url,"application/json", @contentLoad, false,@statusCode, @response)
]%%

```

You can insert mpre personalisation thanks to [AMPscript functions](https://developer.salesforce.com/docs/atlas.en-us.mc-programmatic-content.meta/mc-programmatic-content/functions.htm) or add dyn images or formatted contents. 



### EXAMPLE WITH ATTACHED IMAGE

```java
%%[
set @url = "https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX"
set @contentLoad1 = '{"attachments": [{"title": "CONTACT DELETION REMINDER :warning:","mrkdwn": true,"text": "'
set @slackMsg = 'Reminder - Today we have *%%=DataExtensionRowCount("DeleteDE")=%%* extra contacts to delete !
1. Go to Contact Builder in All Contacts
2. Click on `delete` from data extension
3. Select the data extension from `Data Extensions > path > path`
4. Click On delete contacts'
set @contentLoad2 = '","color": "#d91e18", "image_url": "https://www.fillmurray.com/460/300"}]}'
set @contentLoad = TreatAsContent(concat(@contentLoad1,@slackMsg,@contentLoad2))
var @statusCode
var @response
set @post = HTTPPost2(@url,"application/json", @contentLoad, false,@statusCode, @response)
]%%

```

> :bulb: Choose what you prefer and style your message thanks to Slack [formating documentation](https://api.slack.com/docs/message-attachments) and use the *[Message Builder](https://api.slack.com/docs/messages/builder?msg=%7B%22text%22%3A%22Robert%20DeSoto%20added%20a%20new%20task%22%2C%22attachments%22%3A%5B%7B%22fallback%22%3A%22Plan%20a%20vacation%22%2C%22author_name%22%3A%22Owner%3A%20rdesoto%22%2C%22title%22%3A%22Plan%20a%20vacation%22%2C%22text%22%3A%22I%27ve%20been%20working%20too%20hard%2C%20it%27s%20time%20for%20a%20break.%22%2C%22actions%22%3A%5B%7B%22name%22%3A%22action%22%2C%22type%22%3A%22button%22%2C%22text%22%3A%22Complete%20this%20task%22%2C%22style%22%3A%22%22%2C%22value%22%3A%22complete%22%7D%2C%7B%22name%22%3A%22tags_list%22%2C%22type%22%3A%22select%22%2C%22text%22%3A%22Add%20a%20tag...%22%2C%22data_source%22%3A%22static%22%2C%22options%22%3A%5B%7B%22text%22%3A%22Launch%20Blocking%22%2C%22value%22%3A%22launch-blocking%22%7D%2C%7B%22text%22%3A%22Enhancement%22%2C%22value%22%3A%22enhancement%22%7D%2C%7B%22text%22%3A%22Bug%22%2C%22value%22%3A%22bug%22%7D%5D%7D%5D%7D%5D%7D)* to prepare your JSON





## Setup a script automation

1. First save the AMPscript above into a content builder HTML block

2. Create a *Script Activity* into the *Automation Studio

3. Call the AMPscript block into your SSJS script

   ```html
   <script runat="server">
   Platform.Load("Core", "1.1.1");
   try {
       var ampscriptCode = Platform.Function.ContentBlockByID("ContentBlockByID");
       var textBlock = TreatAsContent(ampscriptCode);
   } catch (ex) {
       Write("error message: " + ex);
   }
   </script>
   ```

4. Create an Automation containing this script activity
