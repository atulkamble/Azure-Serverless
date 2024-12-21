# Azure-Serverless

Here’s a comprehensive guide with sample code and practice examples for key **Azure Serverless services**:

---

## 1. **Azure Functions**

Azure Functions is the centerpiece of Azure Serverless solutions. Below are examples of different triggers:

### 1.1 **HTTP Trigger**
Run a function in response to an HTTP request.

#### Example: Simple HTTP API
```python
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello, {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

---

### 1.2 **Timer Trigger**
Execute code on a schedule (e.g., daily tasks).

#### Example: Scheduled Cleanup
```python
import datetime
import logging

def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().isoformat()
    logging.info(f"Cleanup task executed at {utc_timestamp}")
```

---

### 1.3 **Blob Trigger**
Triggered when a blob is created or updated in Azure Blob Storage.

#### Example: Process Uploaded Files
```python
import logging

def main(blob: func.InputStream) -> None:
    logging.info(f"Processing blob: {blob.name}, Size: {blob.length}")
```

---

## 2. **Azure Logic Apps**

Logic Apps provides low-code workflow automation. While its main use is through the Azure Portal, here’s an overview of scenarios:

### Use Cases:
1. **Send an Email on File Upload**:
   - Trigger: Blob added to Azure Blob Storage.
   - Action: Send an email via Outlook or Gmail connector.

2. **Integrate with APIs**:
   - Trigger: HTTP request.
   - Action: Call external REST API, process response, and save to database.

---

## 3. **Azure Event Grid**

Event Grid is used to handle event-driven architectures. It routes events from sources to subscribers.

### Example: Create an Event Subscription

```bash
az eventgrid topic create --name mytopic --resource-group myresourcegroup --location eastus
az eventgrid event-subscription create \
    --name mysubscription \
    --source-resource-id /subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/mytopic \
    --endpoint https://my-function-app.azurewebsites.net/api/my-function
```

---

## 4. **Azure Service Bus**

A fully managed messaging service for decoupling applications.

### Example: Queue Listener in Azure Functions

#### Function Code:
```python
import azure.functions as func
import logging

def main(msg: func.ServiceBusMessage):
    logging.info(f"Received message: {msg.get_body().decode('utf-8')}")
```

---

## 5. **Azure Storage Queues**

A simple queue service for message handling.

### Example: Queue Message Producer
```python
from azure.storage.queue import QueueClient

# Replace with your connection string
CONNECTION_STRING = "your_connection_string"
QUEUE_NAME = "my-queue"

queue_client = QueueClient.from_connection_string(CONNECTION_STRING, QUEUE_NAME)
queue_client.send_message("Hello, Azure Queue!")
```

### Example: Queue Message Consumer (Azure Function)
```python
import azure.functions as func
import logging

def main(msg: func.QueueMessage) -> None:
    logging.info(f"Queue message: {msg.get_body().decode('utf-8')}")
```

---

## 6. **Azure Durable Functions**

Durable Functions allows creating stateful workflows.

### Example: Orchestrator Function
```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    outputs = []
    tasks = ["Task1", "Task2", "Task3"]

    for task in tasks:
        outputs.append(context.call_activity("ActivityFunction", task))

    return outputs

main = df.Orchestrator.create(orchestrator_function)
```

### Example: Activity Function
```python
import logging

def main(name: str) -> str:
    logging.info(f"Processing {name}")
    return f"Processed {name}"
```

---

## Practice Projects

1. **Weather Notification System**:
   - Use Logic Apps to trigger a weather API call daily and send weather updates via email.

2. **Image Processing Pipeline**:
   - Blob Storage + Blob Trigger Function: Automatically resize uploaded images.
   - Store processed images in another Blob Container.

3. **Event-Driven Workflow**:
   - Use Event Grid to notify a Function when a new file is uploaded to Blob Storage.
   - Process the file and send notifications using Service Bus.

4. **Serverless Chat App**:
   - Use Azure Functions with SignalR to build a real-time chat application.

---

If you'd like detailed guidance or a specific implementation, let me know!
