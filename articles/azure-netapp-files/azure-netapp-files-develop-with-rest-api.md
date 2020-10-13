---
title: 使用 REST API 開發 Azure NetApp 檔案 | Microsoft Docs
description: Azure NetApp Files 服務的 REST API 會定義資源的 HTTP 作業，例如 NetApp 帳戶、容量集區、磁片區和快照集。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 4599f4e3ca291c312c99e938b237d1eb9cd9d407
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929275"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>使用 REST API 為 Azure NetApp Files 進行開發 

Azure NetApp Files 服務的 REST API，會根據 NetApp 帳戶、容量集區、磁碟區和快照集等資源定義 HTTP 作業。 本文可協助您開始使用 Azure NetApp Files REST API。

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API 規格

Azure NetApp Files 的 REST API 規格會透過 [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)發佈：

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>存取 Azure NetApp Files REST API  

1. [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (如果您尚未安裝)。
2. 在 Azure Active Directory (Azure AD) 中建立服務主體：
   1. 確認您擁有[足夠的權限](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

   2. 在 Azure CLI 中輸入下列命令： 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      命令輸出會類似下列範例：  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      保留命令輸出。  您需要 `appId`、`password` 和 `tenant` 值。 

3. 要求 OAuth 存取權杖：

    本文中使用 cURL 的範例。 您還可以使用各種 API 工具，例如 [Postman](https://www.getpostman.com/)、[Insomnia](https://insomnia.rest/) 和 [Paw](https://paw.cloud/)。  

    使用上面步驟 2 中的命令輸出取代下列範例中的變數。 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    輸出會提供類似於下列範例的存取權杖：

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    顯示的權杖有效期為 3600 秒。 有效期之後，您需要要求新權杖。 
    將權杖儲存到文字編輯器中。  您在下一個步驟將會用到這些資料。

4. 傳送測試呼叫，並包含權杖以驗證您對 REST API 的存取：

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>使用 API 的範例  

本文使用下列 URL 作為要求的基準。 此 URL 會指向 Azure NetApp Files 命名空間的根目錄。 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

您應該以您自己的值，取代下列範例中的 `SUBIDGOESHERE` 和 `RESOURCEGROUPGOESHERE` 值。 

### <a name="get-request-examples"></a>GET 要求範例

您可以使用 GET 要求來查詢訂用帳戶中 Azure NetApp Files 的物件，如下列範例所示： 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>PUT 要求範例

您可以使用 PUT 要求在 Azure NetApp Files 中建立新的物件，如下列範例所示。 PUT 要求的主體可以包含變更的 JSON 格式資料。 它必須以文字或參考的形式包含在捲曲命令中，以做為檔案。 若要將主體參考為檔案，請將 json 範例儲存至檔案，並新增 `-d @<filename>` 至捲曲命令。

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>JSON 範例

下列範例示範如何建立 NetApp 帳戶：

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

下列範例示範如何建立容量集區： 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

下列範例顯示如何建立新的磁片區。  (磁片區的預設通訊協定為 NFSV3。 )  

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

下列範例顯示如何建立磁碟區快照集： 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> 您必須指定 `fileSystemId` 才能建立快照集。  您可以透過對磁碟區的 GET 要求取得 `fileSystemId` 值。 

## <a name="next-steps"></a>後續步驟

[請參閱 Azure NetApp Files REST API 參考](/rest/api/netapp/)