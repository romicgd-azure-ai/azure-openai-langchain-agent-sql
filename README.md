# Using OpenAI and langchain to query Microsoft SQL and return structured output

This project demonstrates how to use langchain Agents and OpenAI to query Microsoft SQL and return structured output. 

TL;DR langchain Agents make it very easy to use OpenAI to query SQL. However, there are some snags that need to be addressed and some limitations that need to be considered.
The major obstacle seem LLM token limit [Azure OpenAI Service models](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models): LLMs are not designed to process large data sets and so far best seem to work incombination with vector databases and embeddings. Vector databases are great for retrieving small amounts of relevant data before passing them to LLMs. Whereas creating prompts to access SQL database and process results tends to exhaust LLM token limit very quickly. 

But in case you need to query SQL database with OpenAI, here is how to do it.

## Prerequisites

Before you begin, you'll need to have the following:

- An Azure account with access to Azure OpenAI and Azure SQL

.env needs to contain following 
```
   OPENAI_API_BASE=https://....openai.azure.com/
   OPENAI_API_KEY=...
   OPENAI_API_VERSION=2023-05-15
   SQL_USER_NAME=...
   SQL_USER_PASSWORD=...
```

## Notes

### Permissions
As per langchain doumentation the query chain may generate insert/update/delete queries. When this is not expected, use a custom prompt or create a SQL users without write permissions. https://python.langchain.com/docs/integrations/toolkits/sql_database

### Performance
[langchain.sql_database.SQLDatabase](https://python.langchain.com/docs/integrations/toolkits/sql_database) reads metadata of all tables that user has access to. This can be very slow (minutes) if there more than just a few tables metadata user can access.
One of simpler approaches to address this issue is to limit the number of metadata accessible to the account used for connect to database.
Note: it is not the same as limit permissions to select from specific tables, you also need make sure what ```VIEW DEFINITION``` is not granted to user directly or indirectly via role membership or ``` to public```.

