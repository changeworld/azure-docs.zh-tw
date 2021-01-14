---
title: 以程式設計方式建立 Azure 服務匯流排實體 |Microsoft Docs
description: 本文說明如何使用動態或以程式設計方式布建服務匯流排命名空間和實體。
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 97d89db17af9cde3afadee430b3d0c2a434e12c9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210132"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>動態布建服務匯流排命名空間和實體 
Azure 服務匯流排管理程式庫可以動態佈建服務匯流排命名空間和實體。 這適合複雜的部署和傳訊案例，且可讓您以程式設計方式決定要佈建的實體。 這些程式庫目前適用於 .NET。

## <a name="supported-functionality"></a>支援的功能

* 建立、更新、刪除命名空間
* 建立、更新、刪除佇列
* 建立、更新、刪除主題
* 建立、更新、刪除訂用帳戶

## <a name="azuremessagingservicebusadministration-recommended"></a>Azure. 管理 (建議的) 
您可以使用[Azure. 管理](/dotnet/api/azure.messaging.servicebus.administration)命名空間中的[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)類別來管理命名空間、佇列、主題和訂閱。 以下是範例程式碼。 如需完整範例，請參閱 [CRUD 範例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs)。

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Azure 管理 
您可以使用 [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) 類別來管理命名 [空間、](/dotnet/api/microsoft.azure.servicebus.management) 佇列、主題和訂用帳戶。 以下是範例程式碼： 

> [!NOTE]
> 建議您從程式庫使用 `ServiceBusAdministrationClient` 類別 `Azure.Messaging.ServiceBus.Administration` ，這是最新的 SDK。 如需詳細資訊，請參閱 [第一節](#azuremessagingservicebusadministration-recommended)。 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
此程式庫是以 Azure Resource Manager 為基礎的控制平面 SDK 的一部分。 

### <a name="prerequisites"></a>先決條件

若要開始使用此程式庫，您必須使用 Azure Active Directory (Azure AD) service 進行驗證。 Azure AD 會要求您以提供 Azure 資源存取權的服務主體來進行驗證。 如需建立服務主體的詳細資訊，請參閱以下其中一篇文章：  

* [使用 Azure 入口網站來建立可存取資源 Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 建立用來存取資源的服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [使用 Azure CLI 建立用來存取資源的服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

這些教學課程會提供您 `AppId` (用戶端識別碼)、`TenantId` 和 `ClientSecret` (驗證金鑰)，全部由管理程式庫用於驗證。 您必須至少擁有要執行之資源群組的 [**Azure 服務匯流排資料擁有**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 者或 [**參與者**](../role-based-access-control/built-in-roles.md#contributor) 許可權。

### <a name="programming-pattern"></a>程式設計模式

操控任何服務匯流排資源的模式，都會遵循共通的協定：

1. 使用 **Microsoft.IdentityModel.Clients.ActiveDirectory** 程式庫從 Azure AD 取得權杖：
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. 建立 `ServiceBusManagementClient` 物件：

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. 將 `CreateOrUpdate` 參數設定為您指定的值：

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. 執行呼叫：

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>完成建立佇列的程式碼
以下是建立服務匯流排佇列的範例程式碼。 如需完整範例，請參閱 [GitHub 上的 .net 管理範例](https://github.com/Azure-Samples/service-bus-dotnet-management/)。 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>流暢的程式庫
如需使用流暢程式庫來管理服務匯流排實體的範例，請參閱 [此範例](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus)。 

## <a name="next-steps"></a>後續步驟
請參閱下列參考主題： 

- [Azure. 管理系統管理](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Azure 管理](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)