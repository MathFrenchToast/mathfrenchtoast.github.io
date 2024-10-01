# Declaring and using LLMs in Azure AI Studio

#ia #llm #terraform #iaops #azure

translated using deepl from [my article in french on medium](https://medium.com/@mathieu.veron_70170/d%C3%A9clarer-et-utiliser-des-llm-dans-azure-ai-studio-57a8e9b23666).

Azure AI Studio is presented as ‘A unified platform for responsibly developing and deploying generative AI applications.’  
It's an AI platform that federates several services and resources already found on Azure and aims to make it easier to organise and use these resources.  

In this article, we'll take a look at how:
  -  Why use Azure AI Studio
  - How to deploy the resources required for a RAG application using Terraform
  -  How to use the resources deployed in python code written for OpenAI and in the Continue coding agent.

## Why use Azure AI Studio

Azure AI Studio, launched in May 2023, is presented by Azure as its unifying platform for AI. Without replacing existing resources, it aims to bring them together in a coherent way and make them easier to access.  
 - Thanks to the organisation into hubs (containing resources) and Projects, which will grant and define access, it is easy to ensure that deployment, usage and access policies are respected. For example, models and resources can be deployed in the chosen region (see model deployment below).
 - The availability of a large catalogue of templates. These include, of course, those from openAI, but also those from Lama and Mistral, as well as many models available on HuggingFace. This gives you unified access to several providers.
 - A low-code/no-code interface for testing, finetuning and chatting, which can facilitate an initial proof-of-concept or mock-up approach.
 - Easy integration with data sources. Either directly using other Azure storage resources, or via an AI Search service (vectorisation of information). This makes it possible to build data-driven solutions (e.g. RAG solutions) without duplicating or moving them around.
 - Tools to facilitate testing, monitoring and governance. This makes it easier to track costs, for example.

## How to deploy AI projects with Azure IA Studio

The idea here is to present a terraform deployment for a typical LLM RAG project.  

A procedure for doing this via the portal can be found on the Azure document centre.  

But it's more likely that you're looking for an industrial, reproducible and automatable approach, so I'm proposing a terraform deployment here. You can find some information on the terraform deployment on the portal, but it's not complete.  

I've built a project which contains the entire definition and which will enable you to deploy:
   - a hub
   - a project
   - an AI service
   - a search index
   - two LLMs (one for inferring prompts and one for embedding your data)
   - access rights

I'm not going to go into detail here, but there are several pitfalls that I will explain.

To view the entire project, go to [the public github repository](https://github.com/MathFrenchToast/AzureAIStudio-demo).

## Documentation and the provider are often ‘late’.

As the Azure provider changes frequently and the Azure AI Studio service is fairly recent and also changes fairly quickly, you'll find a lot of documentation, articles and tutorials that are a bit dated.

### 1. Here I'm using version 4+ of the provider
```hcl
required_providers {
  azurerm = {
  source = ‘hashicorp/azurerm
  version = ‘~>4.0
}
```

### 2. the ARM provider is not complete

It is often necessary to use the azapi provider, which encapsulates the azure Json api, because it lacks native definitions for several resources.

This is the case for:

  -  the hub
  -  the project
  -  connections to the AI service and the search index

Note that several github issues are already listed (#25857, #25858, #25859) which should allow the use of new resource definitions soon.  
```
resource ‘azapi_resource’ ‘hub’ {
  type = ‘Microsoft.MachineLearningServices/workspaces@2024-07-01-preview’
  name = ‘${random_pet.rg_name.id}-aih’
  location = azurerm_resource_group.rg.location
  parent_id = azurerm_resource_group.rg.id
}
```

### 3. several permissions are required to access services.

These include

  -  access to the inference service,
  -  another to add data to the search index,
  -  and one to use this index.

In the code I have assigned these rights to the logged-in user running the terraform deployment, but in a real case you will assign these rights to the correct user or main service.  
```
resource ‘azurerm_role_assignment’ ‘identity_access_to_ai_services’ {
  principal_id = data.azurerm_client_config.current.object_id
  scope = azapi_resource.AIServices.id
  role_definition_name = ‘Cognitive Services OpenAI User’
}
```

### 4. the resources need to be created but also connected to the hub or the project

Here, the AI service is connected to the hub, the AI Search service is connected to the project.  

Each connection is defined just after the resource (in tf/aiservice.tf and tf/aisearch.tf).  

### 5. Models are linked to the AI Service

In order to facilitate the transition of python code written on the basis of the OpenAI api and to be migrated to these deployments, I have used the model name as the resource name.  

The deployment model chosen is the standard model in order to respect data localisation constraints as much as possible.  

### 6. Deploying and using all these resources can have a significant cost

Fortunately, Azure offers a number of free-tiers or free usage. As far as possible, I have used the values that allow testing and use at minimum cost.  

As an exception to this rule, I have used the standard deployment model, in order to maintain regional data routing.  
```
resource ‘azurerm_cognitive_deployment’ ‘model’ {
  name = ‘gpt-4o-mini’ # reusing same name as model to ease the migration from openai to azure openai
  cognitive_account_id = azapi_resource.AIServices.id
  version_upgrade_option = ‘OnceNewDefaultVersionAvailable’
  model {
  format = ‘OpenAI’
  name = ‘gpt-4o-mini’
  }
...
}
```

using sku.name=‘GlobalStandard’ can still reduce costs ([by around 10%](https://azure.microsoft.com/fr-fr/pricing/details/cognitive-services/openai-service/)) but at the cost of routing anywhere in the world, which may be incompatible with your regulatory or customer commitments.  

More information [on deployment models is available here](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/deployment-types).  

### to deploy

 -  retrieve the project
 -   go to the ./tf directory
 -  connect to your azure tenant via az login

then run the sequence:
```
terraform init -upgrade
terraform plan -out main.tfplan
terraform apply ‘main.tfplan
```

## Once everything is deployed, all that remains is to use it

In your code, here's a quick look at migrating python code using OpenAI to AzureOpenAI:

On the following code using the Standard OpenAI api
```
import os
from openai import OpenAI

client = OpenAI(
  api_key=os.environ.get(‘OPENAI_API_KEY’),
)

chat_completion = client.chat.completions.create(
  messages=[
    {
      ‘role": “user”,
      ‘content": “Say this is a test”,
    }
  ],
  model=‘gpt-4o-mini’,
)

print(chat_completion)
```

all you have to do is modify the client definition.  
Or by using an api key and the endpoint (which you can find on the home page of your Azure AI service deployed by tf in the portal)  

```
from openai import AzureOpenAI

client = AzureOpenAI(
  api_version=api_version,
  azure_endpoint=endpoint,
  api_key=os.getenv(‘AZURE_OPENAI_API_KEY’) # Go to your Azure AI Service homepage in the portal to get the key
)
```

or by using a connection token
```
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from openai import AzureOpenAI
 
token_provider = get_bearer_token_provider(
  DefaultAzureCredential(),
  ‘https://cognitiveservices.azure.com/.default’
)
api_version = ‘2024-07-01-preview’
endpoint = ‘https://my-resource.openai.azure.com’

client = AzureOpenAI(
  api_version=api_version,
  azure_endpoint=endpoint,
  azure_ad_token_provider=token_provider,
)
```

## Second use - Continue.dev

if you use Continue, the IA extension for VSCode or JetBrain, you can simply enter different templates in your config file.  

A chat model, a code autocompletion model, an embedding model (for codebase analysis), etc.  

You can find all the continue-azure configuration here.  

In conclusion, if you're in an Azure environment, I'd advise you to look at Azure AI Studio and include the resources you'll be using in your IaC, in order to reduce the fragmentation of tools and secure usage.

I remain at your disposal for any questions or projects you may have.  
In: https://www.linkedin.com/in/mathieu-veron-55b9bb55/
