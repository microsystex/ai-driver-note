# Azure OpenAI

## 1. Overview

Azure OpenAI is a collaboration between Microsoft Azure and OpenAI, providing access to powerful language models like GPT-4 and Codex on Azure’s secure cloud platform. This service enables developers to build applications such as chatbots, automated customer service, content generation, and more.

### 1.1 Key Advantages

- **Security and Compliance**: Enterprise-grade security with Azure compliance certifications.
- **Scalability**: Horizontally scalable to meet application needs.
- **Seamless Integration**: Direct integration with Azure services, including Cognitive Services and Data Factory.

## 2. Getting Started

### 2.1 Requirements

1. **Azure Subscription**: Required to activate the service.
2. **Access Permissions**: Specific permissions may be needed for service usage.

### 2.2 Setup

1. **Create Resource**:
    - Sign in to the Azure portal, search for "Azure OpenAI," and create a new resource.
    - Configure resource name, region, and pricing plan.

2. **Select Model**:
    - Choose the appropriate model (e.g., GPT-4 or Codex) on the resource page.
    - Configure model parameters as needed (e.g., response length, quality).

3. **Get API Key**:
    - Generate your API Key from the Azure portal and save it to an environment variable on your local machine or container.

## 3. Using Azure OpenAI API

### 3.1 Chat vs Non-Chat Mode

Azure OpenAI supports both Chat and Non-Chat modes, each suited for different scenarios.

#### 3.1.1 Chat Mode

- **Use Case**: Ideal for conversational interactions that require context management, such as chatbots.
- **How It Works**: Each request includes a message list (`messages`) with past interactions and user inputs.

```python
from azure.identity import DefaultAzureCredential
from azure.ai.openai import OpenAIClient

# Set up Azure OpenAI parameters
AZURE_OPENAI_ENDPOINT = "https://<your Azure OpenAI endpoint>.openai.azure.com/"
AZURE_OPENAI_API_KEY = "<your API Key>"

# Initialize OpenAIClient
client = OpenAIClient(
    endpoint=AZURE_OPENAI_ENDPOINT, 
    credential=AZURE_OPENAI_API_KEY
)

# Set up model deployment name and request content
deployment_id = "<your deployment name>" 
messages = [
    {"role": "system", "content": "You are an assistant."},
    {"role": "user", "content": "Describe AI's future in three sentences."}
]

# Call the API to get a response
response = client.chat_completions.create(
    deployment_id=deployment_id,
    messages=messages,
    max_tokens=50
)

# Display the API response result
print(response.choices[0].message["content"].strip())
```

#### 3.1.2 Non-Chat Mode

- **Use Case**: Suitable for single-response interactions or content generation without context tracking.
- **How It Works**: Uses a `prompt` parameter to supply input without context handling.

```python
from azure.identity import DefaultAzureCredential
from azure.ai.openai import OpenAIClient

# Set up Azure OpenAI parameters
AZURE_OPENAI_ENDPOINT = "https://<your Azure OpenAI endpoint>.openai.azure.com/"
AZURE_OPENAI_API_KEY = "<your API Key>"

# Initialize OpenAIClient
client = OpenAIClient(
    endpoint=AZURE_OPENAI_ENDPOINT, 
    credential=AZURE_OPENAI_API_KEY
)

# Set up model name and request content
deployment_id = "<your deployment name>"  
prompt = "Describe AI's future in three sentences."

# Call the API to get a response
response = client.completions.create(
    deployment_id=deployment_id,
    prompt=prompt,
    max_tokens=50
)

# Display the API response result
print(response.choices[0].text.strip())
```

#### 3.1.3 Choosing Modes

**Chat Mode** is best for interactions needing context retention, while **Non-Chat Mode** is suitable for one-off responses, Q&A, or generation tasks. When the chat's messages only include a single user content, there is little difference between chat and non-chat modes.

In Chat Mode, if the number of messages becomes too large, it is important to summarize the conversation appropriately to avoid generating too many tokens. Here are some strategies to condense previous conversations:

1. **Summarize Key Points**: Extract the main ideas and key points from the previous messages and create a concise summary.
2. **Remove Redundant Information**: Eliminate any repetitive or unnecessary information that does not contribute to the context.
3. **Use Bullet Points**: Convert detailed explanations into bullet points to make the information more compact.
4. **Combine Messages**: Merge related messages into a single message to reduce the overall number of tokens.
5. **Focus on Relevant Context**: Retain only the parts of the conversation that are directly relevant to the current interaction.

By applying these strategies, you can maintain the essential context while minimizing the token usage in Chat Mode.

Here is a Python example that condenses a conversation history by removing duplicate messages, adds a new user message, and calls the OpenAI Chat API with the updated conversation. The condensing strategy also includes selecting only the first sentence from each message to keep the conversation concise. 

```python

from azure.identity import DefaultAzureCredential
from azure.ai.openai import OpenAIClient

# Set up Azure OpenAI parameters
AZURE_OPENAI_ENDPOINT = "https://<your Azure OpenAI endpoint>.openai.azure.com/"
AZURE_OPENAI_API_KEY = "<your API Key>"

# Initialize OpenAIClient
client = OpenAIClient(
    endpoint=AZURE_OPENAI_ENDPOINT, 
    credential=AZURE_OPENAI_API_KEY
)

# Condense the conversation
def condense_conversation(conversation_history):
    seen_contents = set()   # A set to store unique message contents
    cleaned_history = []    # Cleaned conversation history

    # Remove duplicate messages
    for message in conversation_history:
        if message['content'] not in seen_contents:
            cleaned_history.append(message)
            seen_contents.add(message['content'])

    # Condensed by selecting the first sentence of each message
    summary = "\n".join(
        msg['content'].split('.')[0] 
        for msg in cleaned_history 
        if msg['content']
    )

    # Create a condensed conversation by combining role and content
    condensed_conversation = "\n".join(
        f"{msg['role']}: {msg['content']}" 
        for msg in cleaned_history
    )    
    
    return cleaned_history

# Original conversation history
conversation_history = [
    {
        "role": "user", 
        "content": "Hello, I need some help with my account."
    },
    {
        "role": "assistant", 
        "content": "Sure, I can help you with your account. What seems to be the issue?"
    },
    {
        "role": "user", 
        "content": "I forgot my password and cannot log in."
    },
    {
        "role": "assistant", 
        "content": "No problem, I can assist you with resetting your password."
    },
    {
        "role": "user", 
        "content": "I forgot my password and cannot log in."
    }
]

# Get cleaned conversation
cleaned_history = condense_conversation(conversation_history)

# Add new conversation message
new_message = {
    "role": "user", 
    "content": "Can you tell me how long the reset process will take?"
}
cleaned_history.append(new_message)

# Call Azure OpenAI API with updated conversation
response = client.chat_completions.create(
    deployment_id="<your deployment name>",
    messages=cleaned_history,
    max_tokens=50
)

# Get API response content
response_content = response.choices[0].message["content"].strip()

# Display the response
print(response_content)

```

## 4. Azure OpenAI vs OpenAI’s API

Azure OpenAI differs from OpenAI’s ChatGPT API in these key aspects:

- **Access**: Azure OpenAI requires an Azure account and specific access permissions, while OpenAI’s API is accessible directly through OpenAI’s website.
- **Integration**: Azure OpenAI integrates seamlessly with other Azure services, such as Key Vault and Monitor.
- **Enterprise Management**: Azure OpenAI offers enterprise-grade features, including resource monitoring, network security, and compliance.
- **Endpoint URL**: Azure OpenAI uses a specific API URL format, like `https://<your-azure-endpoint>/openai/deployments/<deployment-id>/...`, and requires an `api-version` specification, whereas OpenAI’s ChatGPT API uses a simpler endpoint structure like `https://api.openai.com/v1/...`.
- **Deployment Management**: In Azure OpenAI, each model requires setting up a "deployment" with a unique `deployment-id` identifier, while OpenAI's API directly specifies the model name without needing a separate deployment configuration.