---
author: wchigit
description: code sample
ms.service: service-connector
ms.topic: include
ms.date: 12/04/2023
ms.author: wchi
---

### [.NET](#tab/dotnet)

1. Install dependency.

    ```bash
    dotnet add package Azure.Data.Tables
    ```

2. Get the connection string from the environment variable added by Service Connector.

    ```csharp
    using Azure.Data.Tables;
    using System; 

    TableServiceClient tableServiceClient = new TableServiceClient(Environment.GetEnvironmentVariable("AZURE_COSMOS_CONNECTIONSTRING"));
    ```

### [Java](#tab/java)

1. Add the following dependency in your *pom.xml* file:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-data-tables</artifactId>
        <version>12.2.1</version>
    </dependency>
    ```

1. Get the connection string from the environment variable added by Service Connector.

    ```java
    import com.azure.data.tables.TableClient;
    import com.azure.data.tables.TableClientBuilder;

    String connectionStr = System.getenv("AZURE_COSMOS_CONNECTIONSTRING");

    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionStr)
        .buildClient();
    ```

### [Python](#tab/python)

1. Install dependency.

    ```bash
    pip install azure-data-tables
    ```

1. Get the connection string from the environment variable added by Service Connector.

    ```python
    import os
    from azure.data.tables import TableServiceClient

    conn_str = os.environ["AZURE_COSMOS_CONNECTIONSTRING"]
    table_service = TableServiceClient.from_connection_string(conn_str) 
    ```

### [Go](#tab/go)
1. Install dependency.
    ```bash
    go get github.com/Azure/azure-sdk-for-go/sdk/data/aztables
    ```
2. Get the connection string from the environment variable added by Service Connector.
    ```go
    import (
        "github.com/Azure/azure-sdk-for-go/sdk/data/aztables"
    )

    func main() {
        connStr := os.Getenv("AZURE_COSMOS_CONNECTIONSTRING")
        serviceClient, err := aztables.NewServiceClientFromConnectionString(connStr, nil)
        if err != nil {
            panic(err)
        }
    }
    ```

### [NodeJS](#tab/nodejs)

1. Install dependency.

    ```bash
    npm install @azure/data-tables
    ```

1. Get the connection string from the environment variable added by Service Connector.

    ```javascript
    const { TableClient } = require("@azure/data-tables");

    const serviceClient = TableClient.fromConnectionString(process.env.AZURE_COSMOS_CONNECTIONSTRING);
    ```

### [Other](#tab/none)
For other languages, you can use the endpoint URL and other properties that Service Connector sets to the environment variables to connect to Azure Cosmos DB for Table. For environment variable details, see [Integrate Azure Cosmos DB for Table with Service Connector](../how-to-integrate-cosmos-table.md).
