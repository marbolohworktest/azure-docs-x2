---
author: yungezz
ms.service: service-connector
ms.topic: include
ms.date: 11/24/2023
ms.author: yungezz
---


### [.NET](#tab/dotnet)

Get the Azure Blob Storage connection string from the environment variable added by Service Connector.

Install dependencies
```bash
dotnet add package Azure.Storage.Blob
```

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System; 

// get Blob connection string
var connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGEBLOB_CONNECTIONSTRING");

// Create a BlobServiceClient object 
var blobServiceClient = new BlobServiceClient(connectionString);
```

### [Java](#tab/java)

1. Add the following dependencies in your *pom.xml* file:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-storage-blob</artifactId>
    </dependency>
    ```
1. Get the connection string from the environment variable to connect to Azure Blob Storage:

    ```java
    String connectionStr = System.getenv("AZURE_STORAGEBLOB_CONNECTIONSTRING");
    BlobServiceClient blobServiceClient = new BlobServiceClientBuilder()
        .connectionString(connectionStr)
        .buildClient();
    ```

### [SpringBoot](#tab/springBoot)
Refer to [Upload a file to an Azure Blob Storage](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json) and set up your Spring application. The configuration properties are added to Spring Apps by Service Connector. Two sets of configuration properties are provided according to the version of Spring Cloud Azure (below 4.0 and above 4.0). For more information about library changes of Spring Cloud Azure, refer to [Spring Cloud Azure Migration Guide](https://microsoft.github.io/spring-cloud-azure/current/reference/html/appendix.html#_from_azure_spring_boot_starter_storage_to_spring_cloud_azure_starter_storage_blob).


### [Python](#tab/python)
1. Install dependencies
   ```bash
   pip install azure-storage-blob
   ```
1. Get the Azure Blob Storage connection string from the environment variable added by Service Connector.
   ```python
   from azure.storage.blob import BlobServiceClient
   import os
   
   connection_str = os.getenv('AZURE_STORAGEBLOB_CONNECTIONSTRING')

   blob_service_client = BlobServiceClient.from_connection_string(connection_str)
   ```

### [Django](#tab/django)
1. Install dependencies.
   ```bash
   pip install django-storages[azure]
   ```

1. Configure and set up the Azure Blob Storage backend in your Django settings file accordingly to your django version. For more information, see [django-storages[azure]](https://django-storages.readthedocs.io/en/latest/backends/azure.html#configuration-settings).

1. In setting file, add following lines.
   ```python
   # in your setting file, eg. settings.py
   AZURE_CONNECTION_STRING = os.getenv('AZURE_STORAGEBLOB_CONNECTIONSTRING')
   
   ```

### [Go](#tab/go)

1. Install dependencies.
   ```bash
   go get "github.com/Azure/azure-sdk-for-go/sdk/storage/azblob"
   ```
2. Get the Azure Blob Storage connection string from the environment variable added by Service Connector.
   ```go
   import (
     "context"

     "github.com/Azure/azure-sdk-for-go/sdk/storage/azblob"
   )
   
   
   func main() {

     connection_str = os.LookupEnv("AZURE_STORAGEBLOB_CONNECTIONSTRING")     
     client, err := azblob.NewClientFromConnectionString(connection_str, nil);
   }
   ```

### [NodeJS](#tab/nodejs)
1. Install dependencies
   ```bash
   npm install @azure/storage-blob
   ```
2. Get the Azure Blob Storage connection string from the environment variable added by Service Connector.
   ```javascript
   const { BlobServiceClient } = require("@azure/storage-blob");
   
   const connection_str = process.env.AZURE_STORAGEBLOB_CONNECTIONSTRING;
   const blobServiceClient = BlobServiceClient.fromConnectionString(connection_str);
   ```

### [Other](#tab/none)
For other languages, you can use the Azure Blob Storage account url and other properties that Service Connector sets to the environment variables to connect to Azure Blob Storage. For environment variable details, see [Integrate Azure Blob Storage with Service Connector](../how-to-integrate-storage-blob.md).
