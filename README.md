# Real-time-serverless-chat-application
📲 Real-Time Chat Application using Serverless

📌 Objective

Build a real-time messaging app that leverages serverless architecture to ensure scalability, low maintenance, and cost efficiency. The application uses AWS services like Lambda, API Gateway, DynamoDB, and WebSockets.


---

🛠 Technologies Used

AWS Lambda – Executes backend logic without managing servers.

Amazon API Gateway – Manages REST/WebSocket APIs.

Amazon DynamoDB – Stores user connections and chat messages.

WebSockets – Enables real-time bi-directional communication.

IAM Roles & Policies – Manages secure access between services.



---

📁 Folder Structure

project-root/
│
├── lambda-functions/
│   ├── connect.py        # Handles new WebSocket connections
│   ├── disconnect.py     # Cleans up on disconnection
│   ├── message.py        # Sends/receives messages
│
├── template.yaml         # (If using AWS SAM for deployment)
├── README.md             # Project documentation
└── .env                  # Environment variables


---

⚙ Features

Real-time messaging using WebSockets

Auto-scaling via Lambda functions

Persistent message storage in DynamoDB

Fully serverless; no EC2 or traditional backend

Easy to deploy and cost-effective (Free Tier eligible)



---

🔧 Setup Instructions

Step 1: Create a DynamoDB Table

Table Name: WebSocketConnections

Primary Key: connectionId (String)


Create another table for messages (optional):

Table Name: ChatMessages

Primary Key: messageId (String, UUID)



---

Step 2: Create WebSocket API in API Gateway

Route Selection Expression: $request.body.action

Define these routes:

$connect → Lambda: connect

$disconnect → Lambda: disconnect

sendMessage → Lambda: message




---

Step 3: Write and Upload Lambda Functions

Each function (connect, disconnect, sendMessage) should:

Example for connect.py:

import boto3
import os

dynamodb = boto3.client('dynamodb')

def lambda_handler(event, context):
    connection_id = event['requestContext']['connectionId']
    dynamodb.put_item(
        TableName=os.environ['WEBSOCKET_TABLE'],
        Item={'connectionId': {'S': connection_id}}
    )
    return {'statusCode': 200}

> ✅ Add WEBSOCKET_TABLE as an environment variable in Lambda




---

Step 4: Set IAM Permissions

Attach a policy to each Lambda to allow access to DynamoDB and API Gateway management:

{
  "Effect": "Allow",
  "Action": [
    "dynamodb:PutItem",
    "dynamodb:DeleteItem",
    "dynamodb:Scan",
    "execute-api:ManageConnections"
  ],
  "Resource": "*"
}


---

Step 5: Deploy & Test

Deploy via the AWS Console or CLI

Use a WebSocket testing tool like wscat:


wscat -c wss://<api-id>.execute-api.<region>.amazonaws.com/dev


---

✅ Testing & Debugging

Check CloudWatch Logs for Lambda output and errors.

Use API Gateway test tools to simulate connection and message events.

Handle errors using try/except blocks and proper status codes.


