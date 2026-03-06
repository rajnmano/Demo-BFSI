# Amazon Connect Agentic AI Demo Setup - BFSI

## Orchestration AI Agents

Amazon Connect uses Orchestration AI Agents as the primary agent type for both self-service and agent assistance scenarios. Unlike traditional rule-based systems, Orchestration AI Agents use large language models to:

- Dynamically plan multi-step workflows based on customer intent
- Reason over results at each step to determine the next best action
- Invoke tools to retrieve data, create records, or trigger workflows
- Adapt behavior based on what they discover during the conversation

## Step 1: Clone this repositry

Download the Git Repositry package or clone the the repositry using the blow command

git clone https://github.com/rajnmano/Demo-BFSI.git

## Step 2: Create S3 Bucket and upload the cloned files in Step 1

1.  Open Amazon S3 Console: https://console.aws.amazon.com/s3/ in your chosen region
2.  Click Create bucket
3.  Configure:
4.  Bucket name: ai-agent-workshop-{your-account-id} (must be globally unique)
5.  Region: Same region where you'll deploy (e.g., us-east-1)
6.  Keep all other defaults
7.  Click Create bucket
8.  Note your bucket name - you'll need it for CloudFormation
9.  Upload the files downloaded from the step 1, the folder structure should be as below:

        ai-agents/
        boto3-layer/
        demo/
        flow-lex/
        knowledge-base/
        templates/

## **Step 3: Deploy CloudFormation Stack**

### Deploy via Console

1.  Open **CloudFormation Console**: https://console.aws.amazon.com/cloudformation/  from your **chosen region**
2.  Click **Create stack** → **With new resources (standard)**

### Specify Template

1.  Select **Amazon S3 URL**
2.  Enter your template URL using this pattern: https://{your-bucket}.s3.{region}.amazonaws.com/templates/start.yaml

Example: https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/templates/start.yaml

1.  Click **Next**

### Configure Parameters

The parameters below are organized to match the order they appear in the CloudFormation console.

| **Parameter** | **Value** |
| --- | --- |
| **Stack name** | Stack name must contain only letters (a–z, A–Z), numbers (0–9) and hyphens (-) and start with a letter. Max 20 characters.<br><br>Eg. demo123 |
| **Assets S3 Bucket Name** | Name of the S3 bucket containing deployment assets as uploaded in the Step 2<br><br>Eg. ai-agent-workshop-123456789012 |
| **Existing Connect Instance ARN** | Amazon Connect Instance ARN<br><br>Eg. arn:aws:connect:us-east-1:123456789012:instance/aaaaaaaa-111a-222b-333c-a1s2d3f4g5h6 |
| **Existing Connect Instance ID** | ID of an existing Amazon Connect instance<br><br>Eg. aaaaaaaa-111a-222b-333c-a1s2d3f4g5h6 |
| **Existing Connect Instance Alias** | Alias (subdomain) of an existing Amazon Connect instance.<br><br>Eg. amazon-connect-123456789012-a1s2d3f4g5h6 |
| **ExistingProfileId** | Profile ID of the customer who would be calling for the demo. HOW TO FIND: Loging to Agent workspace search for an existing customer profile and copy the Profile ID. <br><br> Eg. 1aa1f22b313d48158270f22d47aaaaaa |
| **DemoSeedDataUrl** | URL for demo seed data JSON file<br><br>Eg. s3://ai-agent-workshop-123456789012/demo/demo-seed-data.json |
| **Knowledge Base Template URL** | S3 URL for the assistant-knowledge-base.yaml nested stack template.<br><br>Eg. https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/templates/assistant-knowledge-base.yaml |
| **AI Agents Template URL** | S3 URL for the ai-agents-setup.yaml nested stack template<br><br>Eg. https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/templates/ai-agents-setup.yaml |
| **Knowledge Base Documents ZIP URL** | S3 URL for the knowledge base documents ZIP file.<br><br>Eg. https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/knowledge-base/demo-kb.zip |
| **demo API Template URL** | S3 URL for the demo-api.yaml nested stack template.<br><br>Eg. https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/templates/demo-api.yaml |
| **AgentCoreGatewayStackUrl** | S3 URL for the agentcore-gateway.yaml nested stack template.<br><br>Eg. https://ai-agent-workshop-123456789012.s3.us-east-1.amazonaws.com/templates/agentcore-gateway.yaml |
| **Boto3LayerS3Bucket** | Eg. ai-agent-workshop-123456789012 |
| **Boto3LayerS3Key** | S3 key (path) to the boto3-layer.zip file within the bucket.<br><br>Eg. boto3-layer/boto3-layer.zip |
| **DemoAgentAssistPromptUrl** | URL for demo agent-assist AI agent prompt YAML file<br><br>Eg. s3://ai-agent-workshop-123456789012/ai-agents/demo/demo-agent-assist-prompt.yaml |
| **DemoOpenApiSpecUrl** | URL for demo API OpenAPI specification YAML file<br><br>Eg. s3://ai-agent-workshop-123456789012/demo/demo-api-openapi.yaml |
| **DemoSeedDataUrl** | URL for demo seed data JSON file<br><br>Eg. s3://ai-agent-workshop-123456789012/demo/demo-seed-data.json |
| **DemoSelfServicePromptUrl** | URL for demo self-service AI agent prompt YAML file<br><br>Eg. s3://ai-agent-workshop-123456789012/ai-agents/demo/demo-self-service-prompt.yaml |

## Step 4: Monitor Deployment

1.  Watch the **Events** tab for progress
2.  Stack creation takes approximately **6-8 minutes**
3.  Status: CREATE_IN_PROGRESS → CREATE_COMPLETE

## Step 5: Verify Deployment

1.  Open **Amazon Connect Console**
2.  Find your instance and click **Log in**
3.  Navigate to **AI agent designer** → **AI Agents**
4.  Verify AI Agents are created

## Step 6: Navigate to Your Pre-Deployed Gateway

1.  Open the **AWS Console** and search for **Amazon Bedrock AgentCore**
2.  Click **Gateways** in the left navigation
3.  Locate your pre-deployed gateway:
    - Look for a gateway name starting with workshop-gw-
    - Or find the **Gateway ID** from your CloudFormation stack outputs (GatewayId)
4.  Click on the gateway name to view its configuration

## Step 7: Update Target with Full OpenAPI Specification

The pre-deployed targets have placeholder OpenAPI specifications. Update them with the full specifications from S3 to enable all API operations.

1.  From your gateway details page, scroll down to the **Targets** section
2.  Click on a target to update
3.  Click **Edit** to modify the target configuration
4.  In the **Schema** section:
    - Change **Schema Source** from **Inline** to **S3 resource**
    - Enter the **S3 URI** from your CloudFormation stack outputs
    - For Hotel, use the value from DemoOpenApiSpecS3Location
5.  Click **Update target** to apply the changes


## Step 8: Associating MCP Server with Amazon Connect

Your AgentCore Gateway is pre-deployed and configured. The final step is to associate it with your Amazon Connect instance so your AI Agents can invoke the MCP tools.

1.  Open the **Amazon Connect Console**
2.  Go to **Third-party applications** in the left navigation
3.  Click **Add application**
4.  Configure the application:
    - **Display name**: A descriptive name (e.g., workshop-gateway)
    - **Description**: Brief description
    - **Application type**: Select **MCP server**
5.  Select your **Connect instance** in the Instance association section
6.  Click **Add application**

## **Step 9:** Navigate to Security Profiles and Configure AI Agent Tools

1.  In the Amazon Connect admin console, go to **Users** > **Security profiles**
2.  Find and select **Workshop AI Agent - demo** (pre-deployed for this workshop)
3.  Under Tools, find the tools associated with your gateway.
4.  Enable **All tools** within the gateway to grant access to all MCP tool namespaces
5.  Click **Save** to apply changes

## Step 10: Configuring the Escalate Tool

The Escalate tool transfers the conversation to a human agent with context. Configure it to capture relevant information:

1.  Open **Amazon Connect Console**
2.  Find your instance and click **Log in**
3.  Navigate to **AI agent designer** → **AI Agents**
4.  Click on **Demo-Self-Service-Agent** AI agent
5.  Click on **Edit in Agent Builder**
2.  In the Agent Builder, find the **Escalate** tool (pre-configured)
3.  Select it and **Edit** to expand the tool configuration
4.  Update the **Instructions** field - this guides when the AI should escalate:

Use this tool when:
```
- The customer explicitly asks to speak with a human agent
- You cannot resolve the customer's issue after reasonable attempts
- The situation requires human judgment or authorization
- The customer expresses frustration or dissatisfaction with AI assistance
```
Before escalating, summarize the conversation context so the human agent can continue seamlessly.

1.  Update the **Input Schema** - this defines what information to capture:
```
{
"type": "object",
"properties":
{
"customerIntent":
{
"type": "string",
"description": "A brief phrase (10-15words) describing what the customer wants to accomplish"
},
"sentiment":
{
"type": "string",
"description": "Customer's emotional state during the conversation",
"enum":
[
"positive",
"neutral",
"frustrated"
]
},
"escalationSummary":
{
"type": "string",
"description": "Detailed summary for the human agent including what the customer asked for, what was attempted, and why escalation is needed",
"maxLength": 500
},
"escalationReason":
{
"type": "string",
"description": "Category for the escalation reason",
"enum":
[
"complex_booking",
"technical_issue",
"customer_frustration",
"policy_exception",
"out_of_scope",
"other"
]
}
},
"required":
[
"escalationReason",
"escalationSummary",
"customerIntent",
"sentiment"
]
}
```

1.  Add **Examples** Examples help the AI understand the tone you want for escalation messages. Add these five examples:

**Example 1:**
```
Good example - Technical difficulties:
```
&lt;message&gt;
```
I'm having trouble accessing the information you need right now. Let me connect you with a human agent who can help you further and make sure you get taken care of.
```
&lt;/message&gt;

**Example 2:**
```
Good example - Frustrated customer:
```
&lt;message&gt;
```
I understand your frustration with this issue, and I want to make sure you get the help you deserve. Let me connect you with a human agent who can give this their full attention.
```
&lt;/message&gt;

**Example 3:**
```
Bad example (avoid this - too abrupt, no empathy):
```
&lt;message&gt;
```
I can't help with that. Let me transfer you to someone else.
```
&lt;/message&gt;

1.  Click **Update** to save your changes

## Step 11: Configuring the Complete Tool

The Complete tool gracefully ends the conversation. Configure it to ensure proper closure:

1.  Find the **Complete** tool in the Agent Builder
2.  Select is and choose **Edit** to expand the tool configuration
3.  Update the **Instructions** field:

Use this tool when:
```
- The customer's issue has been fully resolved
- The customer confirms they have no more questions
- The customer says goodbye or indicates they want to end the call
- The interaction has reached a natural conclusion
```
Always confirm with the customer before ending the conversation.

1.  Review the **Input Schema**:
```
{
"type": "object",
"properties": {
"reason": {
"type": "string",
"description": "Reason of completion"
}
},
"required": [
"reason"
]
}
```
1.  Click **Update** if you made changes

## Step 12: Create a Lex Bot

Your AI Agent needs a Lex bot to handle the conversational interface. The Lex bot serves as the voice/text interface layer while the AI Agent handles intelligence and decision-making.

1.  In Amazon Connect, navigate to **Routing** -> **Flows** → **Conversational AI**
2.  Click **Create Conversational AI Bot**
3.  Configure the bot:
    - **Bot name**: Demo-SelfServiceBot
    - **Description**: Bot for Self-Service AI Agent
4.  Click **Create**
5.  Wait for the bot to be created (this may take a few moments)

**Lex Bot Role**

In self-service flows, the Lex bot serves as the voice/text interface layer. The AI Agent handles the intelligence and decision-making, while Lex manages speech recognition and text-to-speech.

1.  Once the bot is created, select **Add language** and select **English (US)**
2.  Within the bot, enable **Amazon Connect AI agent in Connect intent** by toggling, selecting your assistant ID, and confirming.
3.  Select **Build language** to build the bot.

## Step 13: Download and Import the Self-Service Flow

We provide a pre-built Self-Service flow that includes all necessary configuration:

1.  Download the Self-Service flow: flow/Demo-In.json
2.  In Amazon Connect, navigate to **Routing** → **Flows**
3.  Click **Create flow**
4.  Click the dropdown arrow next to **Save** and select **Import**
5.  Select the downloaded Demo-In.json file
6.  Review the flow structure:
    - **Set logging behavior** - Enables flow logging for troubleshooting
    - **Create Connect Assistant session** - Initializes the AI assistant
    - **Set voice** - Configures text-to-speech voice
    - **Get customer input** - Routes to Lex bot for AI Agent conversation
    - **Check contact attributes** - Detects escalation or completion
    - **Set contact attributes** - Stores escalation context for human agent
    - **Transfer to queue** - Routes escalated contacts to human agents
