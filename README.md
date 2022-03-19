# vercel_logging

### This is AWS project. The heart of the project has four components:
1). AWS API Gateway
2). Lambda function what take the query paramters, rounting data to dynamoDB
3). dyanmoBD for long term/streaming storage
4). Slack integration.  The vercel error logs will be posted into designated slack channels

### Here is the steps to set up the service within AWS console
1). configure API gateway via AWS console, just follow the steps. Add two actions, /getLogs /putLogs. Both actions pointing to the same lambda function
2). create a lamda function called log_ingress (this function is used in step 1)
3). While configure API Gateway, add "action", and chose lambda function "log_ingress"
4). create a dynamoBD named"log_repo", and have two keys: prime key= OrgId, and partition key 'logid'
5). configure lambda access to access dynamoBD
    go to IAM -> roles
    create a new role, (or add permission to the existing role), i.e. when lambda function is created, a role is automatically created.
    search for dynamodb. and add full access or read only.
    go back to lamdba function -> configuration -> edit execution role, and add newly configured role
    testing db acess. the pyhton code for access dynamodb:
    
<code>
    import boto3
    client = boto3.resource("dynamodb")
    table =  client.Table("log_repo")
</code>
    
6). configure WebHooks to slack channel
    go to slack
    add application, search "Incomming WebHooks"
    Brower will pop up, and generate key for the channel

<code> curl -vv -X POST https://xxxx1-api.us-west-1.amazonaws.com/putLogs -d@alert.json --header 'Content-Type: application/json'</code>


<code>
{
    "slack_channel":"general",
    "OrgId":"Org001",
    "LogId":"12345",
    "Logs":"vercel stack trace logs 5001"
}
</code>

### End points
https://xxxx1.execute-api.us-west-1.amazonaws.com/getLogs?OrgId=OrgId001
https://xxxx1.execute-api.us-west-1.amazonaws.com/putLogs






