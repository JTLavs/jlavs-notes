# AWS Step Functions
- Model your workflows as state machines (one per workflow)
    - Order fulfillment, data processing
    - Web applications, any workflow
- Written in JSON
- Visualization of the workflow and the execution of the workflow, as well as history
- Short workflow with SDK call, API Gateway, EventBridge (CloudWatch event)

### Step Functions - Task states
- Do some work in your state machine
- Invoke one AWS service
    - Lambda function
    - Run AWS batch job
    - Run ECS task and wait for it
    - Insert item into DynamoDB
    - Publish message to SNS, SQS
    - Launch another step function workflow
- Run on one activity
    - EC2, Amazon ECS, on-premises
    - Activities poll the step functions for work
    - Activities send results back to step functions

### Step functions - States
- Choice state: Test for a condition to send to a branch
- Fail or succeed state - Stop execution with failure/success
- Pass state - Simply pass its input to its putput or inject some fixed data without performing work
- Wait state - Provide a delay for a certain amount of time or until a specified time/date
- Parallel state - Begin parallel branches of execution

### Step functions - Error handling 
- Any state can encounter runtime errors for various reasons:
    - State machine definition issues
    - Task failures
    - Transistent issues
- Use Retry and Catch in the State Machines to handle the errors instead of inside the application code
- Predefined error codes:
    - States.ALL - matches any error name
    - States.Timeout - Task ran longer than TimeoutSeconds or no. heartbeat recieved
    - States.TaskFailed - Execution failure
    - States.Permissons - insufficient priviledges to execute
- State may report its own errors

### Step function - Retry
- Evaluated from top to bottom
- ErrorEquals: match a specific kind of error
- IntervalSeconds: initial delay before retrying
- BackoffRate: multiple delay after each retry
- MaxAttempts: default to 3, set to 0 for no retries
- When max. attempts are reached, Catch kicks in

### Step Functions - Catch
- Evaluated from top to bottom
- ErrorEquals: match specific kind of error
- Next: State to send to
- ResultPath: Path that determines what input is sent to the state specified in the Next field

### Step Functions - Standard vs Express
- Standard
    - 1 yr duration
    - Execution start rate: 2000/sec
    - State transition rate: 4000/sec
    - Pricing per state transition - Counted each time a step in your execution is completed (more expensive)
    - Executions can be listed and described with step function APIs and visually debugged through the console. Can also be debugged in CloudWatch Logs by enabling logging on your state machine
    - Exactly-once workflow execution
- Express
    - 5 min. duration
    - Execution start rate: > 100000/sec
    - State transition: Nearly unlimited
    - Priced by the no. of execution you run, their duration and memory consumption
    - Execution can be inspected in CloudWatch Logs by enabling logging on your state machine
    - At-least-once workflow execution