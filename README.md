I checked the video topic and related AWS documentation/blogs. The video is basically showing how to create a Post Chat Survey Flow in Amazon Connect using Lex Bot + Lambda + Disconnect Flow. 

Here’s the step-by-step flow shown in the video in simple practical order:


---

Post Chat Survey Flow — Complete Steps

1. Create Amazon Lex Bot

Go to:

[Amazon Lex Console](https://console.aws.amazon.com/lex/?utm_source=chatgpt.com)


Then:

1. Click Create Bot


2. Select:

Create blank bot

Runtime role creation



3. Enter:

Bot Name

Language = English



4. Create bot




---

2. Create Survey Intents

Inside Lex Bot:

Create intents like:

RatingIntent1

RatingIntent2

RatingIntent3

RatingIntent4

RatingIntent5


OR one intent with slots.


---

3. Add Sample Utterances

Example utterances:

1

one

bad

poor

2

3

4

5

excellent


Basically user rating capture karna hota hai.


---

4. Configure Closing Responses

For each intent:

Add response like:

“Thanks for your feedback.”

“Thank you for rating us.”



---

5. Build and Publish Lex Bot

Then:

1. Click Build


2. Create Bot Version


3. Create Alias



Very important step.


---

6. Add Lex Bot into Amazon Connect

Go to:

[Amazon Connect Console](https://console.aws.amazon.com/connect/?utm_source=chatgpt.com)


Then:

1. Open your Connect instance


2. Go to:

Routing

Contact Flows



3. Left side:

Amazon Lex



4. Select region


5. Add created Lex bot 




---

7. Create Post Chat Survey Contact Flow

Create new contact flow.

Usually type:

Disconnect Flow OR

Contact Flow


depending on architecture.


---

8. Add “Get Customer Input” Block

This is the main block.

Configure:

Text prompt:

“Please rate your chat experience from 1 to 5.”


Enable Lex Bot

Select created Lex bot


This block sends customer message to Lex. 


---

9. Store Survey Result

Add:

Set Contact Attributes block


Map values like:

Rating	Stored Value

Excellent	5
Good	4
Average	3
Poor	2
Bad	1


Usually stored as:

SurveyResult

CSATScore


attribute.


---

10. Invoke Lambda Function (Optional but shown in most flows)

Add:

Invoke AWS Lambda block


Purpose:

Save response in DynamoDB

Send analytics

Store survey response

Push data to backend/API


Flow:

Customer Response → Lex → Connect → Lambda → DB/API




---

11. Configure Lambda Return Values

Lambda returns:

success

statusCode

survey saved


Then connect routes to:

Success branch

Error branch



---

12. Add Play Prompt / Message

Example:

“Thank you for completing the survey.”



---

13. Disconnect / End Chat

Finally:

Disconnect block


Ends survey flow.


---

14. Configure Disconnect Flow

This is VERY important in video.

Go to:

Chat settings / flow settings


Set:

Disconnect Flow = Post Chat Survey Flow


So when agent/customer disconnects: Survey automatically starts. 


---

15. Enable DisconnectOnCustomerExit

For web chat/widget:

Add:

"DisconnectOnCustomerExit": ["AGENT"]

inside StartChatContact API.

This ensures disconnect flow triggers properly. 


---

16. Test Complete Flow

Testing sequence:

1. Start chat


2. Connect agent


3. End chat


4. Survey starts


5. User gives rating


6. Lex captures response


7. Lambda saves response


8. Chat disconnects




---

Actual Architecture Used

Customer Chat
     ↓
Amazon Connect Chat
     ↓
Agent Disconnect
     ↓
Disconnect Flow Triggered
     ↓
Get Customer Input
     ↓
Amazon Lex Bot
     ↓
AWS Lambda
     ↓
DynamoDB / Analytics
     ↓
Thank You Message
     ↓
Disconnect


---

Main AWS Services Used

Amazon Web Services

Amazon Connect

Amazon Lex

AWS Lambda

Amazon DynamoDB



---

Important Thing Video Is Showing

Most important understanding:

After agent leaves chat:

Disconnect Flow
   ↓
Lex Bot asks survey
   ↓
Lambda processes response
   ↓
Save survey data

This is exactly what you were asking previously also.























Got it.
Since your existing chat flow is already working, now you only need to attach:

Disconnect Flow → Lex Bot → Survey Capture

Then later you’ll add:

Lambda → DynamoDB

So here’s the exact practical implementation flow.


---

STEP 1 — Create Lex Bot

Go to:

[Amazon Lex Console](https://console.aws.amazon.com/lex/?utm_source=chatgpt.com)

Then:

Create Bot

Create Bot

Blank bot

Name:

PostChatSurveyBot


Language:

English


IAM role:

Create new role



Create bot.


---

STEP 2 — Create Intent

Inside bot:

Create intent:

CaptureSurveyRating


---

STEP 3 — Add Sample Utterances

Add:

1
2
3
4
5
one
two
three
four
five
good
bad
excellent
average


---

STEP 4 — Create Slot (VERY IMPORTANT)

Inside intent:

Add Slot

Type:

AMAZON.Number

Slot name:

rating

Prompt:

Please rate your experience from 1 to 5


---

STEP 5 — Success Response

Closing response:

Thank you for your feedback.


---

STEP 6 — Build + Alias

Now:

Build Bot

Click:

Build

Then:

Create Alias

Example:

TestAlias

THIS IS IMPORTANT.

Without alias Connect won't detect bot.


---

STEP 7 — Add Lex in Amazon Connect

Now go to:

[Amazon Connect Console](https://console.aws.amazon.com/connect/?utm_source=chatgpt.com)


---

Inside Connect

Go:

Routing
→ Contact Flows

Then:

AWS Lex

OR sometimes:

Contact Flows
→ Lex Bots

(depending UI version)


---

Add Bot

Select Region

Select:

PostChatSurveyBot


Add Lex Bot


Save.


---

STEP 8 — Open Your EXISTING FLOW

Now this is your main point.

Open your existing flow.

Probably:

Chat flow OR

Disconnect flow



---

STEP 9 — Add “Get Customer Input” Block

Left panel:

Interact
→ Get customer input

Drag this block.


---

STEP 10 — Configure This Block

Open block.


---

Select Lex Bot

Inside:

Amazon Lex

Choose:

PostChatSurveyBot

Alias:

TestAlias

Intent:

CaptureSurveyRating


---

STEP 11 — Add Prompt Message

Inside same block:

Text to customer:

Example:

Please rate your support experience from 1 to 5.


---

STEP 12 — Store Rating in Attribute

VERY IMPORTANT.

Inside same block:

Go:

Input handling

Store input in attribute:

Example:

$.Lex.Slots.rating

Save as:

CustomerRating

So now:

CustomerRating = 5

gets stored automatically.


---

STEP 13 — Connect Success Branch

Now:

Get Customer Input
       ↓
Success

Connect it to:

Play Prompt

Example message:

Thank you for your feedback.


---

STEP 14 — Then Disconnect

Add:

Disconnect / End Chat


---

FINAL FLOW

Disconnect Flow Triggered
        ↓
Get Customer Input
   (Lex Bot)
        ↓
Capture Rating
        ↓
Store Attribute
(CustomerRating)
        ↓
Thank You Prompt
        ↓
Disconnect


---

STEP 15 — Attach as Disconnect Flow

This is MOST IMPORTANT.

Go to your chat flow.

Find:

Set disconnect flow

OR

Chat settings.

Select:

PostChatSurveyFlow

Now whenever:

Agent disconnects OR

Chat ends


Survey automatically starts.


---

AFTER THIS

Then next phase:

Lex Rating
    ↓
Invoke Lambda
    ↓
Save in DynamoDB

You are already very close actually.
Right now your main task is only:

Lex Bot working inside Get Customer Input block

Once that works, Lambda + DDB is easy.






Complete Detailed Steps — Post Chat Survey using Amazon Connect + Lex

(Based on your video flow implementation)


---

PHASE 1 — CREATE AMAZON LEX BOT

Go to:

[Amazon Lex Console](https://console.aws.amazon.com/lex/?utm_source=chatgpt.com)


---

STEP 1 — Click “Create Bot”

You’ll see:

Create bot

Click it.


---

STEP 2 — Select Creation Method

Choose:

Create a blank bot

NOT:

Sample bot

Import bot



---

STEP 3 — Configure Basic Details

Fill these:

Field	Value

Bot name	PostChatSurveyBot
IAM permissions	Create a role with basic Amazon Lex permissions
COPPA	No
Idle timeout	Default


Then click:

Next


---

STEP 4 — Choose Language

Select:

English (US)

Then:

Done

Bot gets created.


---

STEP 5 — Create Intent

Inside bot dashboard:

Click:

Create intent

Select:

Add empty intent

Intent name:

CaptureSurveyRating

Save intent.


---

STEP 6 — Add Sample Utterances

Now inside intent:

Find:

Sample utterances

Add one-by-one:

1
2
3
4
5
one
two
three
four
five
excellent
good
bad
average
very good
poor

Purpose: Lex learns customer rating patterns.


---

STEP 7 — Add Slot

VERY IMPORTANT STEP.

Scroll to:

Slots

Click:

Add slot


---

Slot Configuration

Slot Name

rating


---

Slot Type

Choose built-in slot:

AMAZON.Number


---

Prompt

Add prompt:

Please rate your support experience from 1 to 5


---

Required

Enable:

Required for fulfillment

Save slot.


---

STEP 8 — Configure Closing Response

Scroll:

Closing response

Enable it.

Message:

Thank you for your feedback.


---

STEP 9 — Build Bot

Top-right:

Click:

Build

Wait until build succeeds.


---

STEP 10 — Create Alias

Now:

Go:

Deployments
→ Aliases

Create alias.

Example:

Field	Value

Alias name	TestAlias


Save.

VERY IMPORTANT.

Without alias Amazon Connect cannot connect Lex.


---

PHASE 2 — CONNECT LEX WITH AMAZON CONNECT

Go to:

[Amazon Connect Console](https://console.aws.amazon.com/connect/?utm_source=chatgpt.com)


---

STEP 11 — Open Your Amazon Connect Instance

Open your instance.


---

STEP 12 — Add Lex Bot into Connect

Go:

Routing
→ Contact Flows

Now left-side settings area:

Find:

Amazon Lex

OR

Lex Bots

(depending AWS UI version)


---

STEP 13 — Associate Bot

Click:

Add Lex Bot

Choose:

Field	Value

Region	Your region
Bot	PostChatSurveyBot
Alias	TestAlias


Add bot.

Save.


---

PHASE 3 — CREATE POST CHAT SURVEY FLOW


---

STEP 14 — Create New Contact Flow

Go:

Routing
→ Contact Flows

Click:

Create contact flow


---

STEP 15 — Select Flow Type

Choose:

Disconnect flow

Name:

PostChatSurveyFlow

Create.


---

PHASE 4 — BUILD SURVEY FLOW


---

STEP 16 — Add “Get Customer Input” Block

Left panel:

Interact
→ Get customer input

Drag block to canvas.

Connect:

Entry
↓
Get customer input


---

STEP 17 — Configure Get Customer Input Block

Open block.


---

Customer Input Type

Select:

Amazon Lex


---

Choose Lex Bot

Select:

Field	Value

Lex Bot	PostChatSurveyBot
Alias	TestAlias



---

Intent

Choose:

CaptureSurveyRating


---

Prompt

Inside text prompt:

Please rate your support experience from 1 to 5.


---

STEP 18 — Store Rating into Contact Attribute

Inside same block:

Find:

Input handling

OR:

Session attributes

(depending AWS UI)


---

Add Attribute

Map:

Source	Destination

$.Lex.Slots.rating	CustomerRating


Meaning:

If customer says:

5

then:

CustomerRating = 5

gets stored in contact attributes.

VERY IMPORTANT for Lambda later.


---

STEP 19 — Success Branch

Now connect:

Get customer input
↓
Success

to:

Play prompt


---

STEP 20 — Configure Thank You Message

Message:

Thank you for completing the survey.


---

STEP 21 — Add Disconnect Block

Add:

Disconnect / End flow

Connect:

Play prompt
↓
Disconnect


---

COMPLETE FLOW STRUCTURE

Entry
 ↓
Get Customer Input
(Lex Bot)
 ↓
Capture Rating
 ↓
Store Attribute
(CustomerRating)
 ↓
Thank You Prompt
 ↓
Disconnect


---

PHASE 5 — ATTACH SURVEY FLOW TO EXISTING CHAT FLOW

THIS PART IS MOST IMPORTANT.


---

STEP 22 — Open Existing Chat Flow

Open your main production chat flow.


---

STEP 23 — Find “Set Disconnect Flow” Block

Inside existing flow:

Search:

Set disconnect flow

If not present:

Add it from:

Set
→ Set disconnect flow


---

STEP 24 — Configure Disconnect Flow

Select:

PostChatSurveyFlow

This means:

When chat disconnects:

Main Chat Ends
      ↓
PostChatSurveyFlow Starts


---

PHASE 6 — TEST FLOW


---

STEP 25 — Publish Everything

Publish:

Lex Bot

Alias

Contact Flow


ALL REQUIRED.


---

STEP 26 — Start Test Chat

Flow:

Customer starts chat
↓
Agent joins
↓
Agent disconnects
↓
Disconnect flow triggers
↓
Lex asks rating
↓
Customer enters number
↓
Rating stored
↓
Thank you message
↓
Chat ends


---

PHASE 7 — NEXT STEP (YOUR FUTURE IMPLEMENTATION)

After Lex works properly:

You’ll add:

Invoke Lambda

inside survey flow.


---

Future Architecture

Customer Rating
      ↓
Lex Bot
      ↓
Contact Attribute
(CustomerRating)
      ↓
Lambda
      ↓
DynamoDB


---

HOW LAMBDA WILL READ RATING

Inside Lambda:

event['Details']['ContactData']['Attributes']['CustomerRating']

This gives:

1
2
3
4
5

directly.


---

MOST IMPORTANT UNDERSTANDING

Your actual working chain is:

Chat Ends
   ↓
Disconnect Flow Starts
   ↓
Get Customer Input Block
   ↓
Lex Bot Captures Rating
   ↓
Store Contact Attribute
   ↓
(Optional Lambda + DDB)
   ↓
Thank You
   ↓
Disconnect

That is the exact architecture shown in the video implementation.



















