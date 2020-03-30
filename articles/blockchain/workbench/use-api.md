---
title: 使用 Azure 區塊鏈工作臺 REST API
description: 如何使用 Azure 區塊鏈工作臺預覽 REST API 的方案
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672740"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>使用 Azure 區塊鏈工作臺預覽 REST API

Azure 區塊鏈工作臺預覽 REST API 為開發人員和資訊工作者提供了一種構建與區塊鏈應用程式的豐富集成的方法。 本文重點介紹了如何使用工作臺 REST API 的幾個方案。 例如，假設您要創建一個自訂區塊鏈用戶端，允許登錄使用者查看其分配的區塊鏈應用程式並與之交互。 用戶端可以使用區塊鏈工作臺 API 查看合同實例並針對智慧合約執行操作。

## <a name="blockchain-workbench-api-endpoint"></a>區塊鏈工作臺 API 端點

區塊鏈工作臺 API 可通過部署的終結點進行訪問。 要獲取部署的 API 終結點 URL，請處理：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在左側功能窗格中，選擇 **"資源組**"。
1. 選擇已部署的區塊鏈工作臺的資源組名稱。
1. 選取 [類型]**** 資料行標題，依類型按字母順序來排序清單。
1. 有兩項屬於 **App Service** 類型的資源。 選擇*具有*"api"尾碼**的應用服務**類型的資源。
1. 在應用服務**概述**中，複製**URL**值，該值表示已部署的區塊鏈工作臺的 API 終結點 URL。

    ![應用服務 API 終結點 URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>驗證

對區塊鏈工作臺 REST API 的請求受 Azure 活動目錄 （Azure AD） 的保護。

要向 REST API 發出經過身份驗證的請求，用戶端代碼需要使用有效憑據進行身份驗證，然後才能調用 API。 Azure AD 協調了各個參與者之間的身份驗證，並為用戶端提供[訪問權杖](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token)作為身份驗證的證明。 然後，在 REST API 請求的 HTTP 授權標頭中發送權杖。 要瞭解有關 Azure AD 身份驗證的更多內容，請參閱[開發人員的 Azure 活動目錄](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。

有關如何進行身份驗證的示例，請參閱[REST API 示例](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples)。

## <a name="using-postman"></a>使用 Postman

如果要測試或試驗工作臺 API，可以使用[Postman](https://www.postman.com)對部署進行 API 呼叫。 從 GitHub[下載工作臺 API 請求的 Postman 示例集合](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman)。 有關身份驗證和使用示例 API 請求的詳細資訊，請參閱 README 檔。

## <a name="create-an-application"></a>建立應用程式

您可以使用兩個 API 呼叫來創建區塊鏈工作臺應用程式。 此方法只能由工作臺管理員的使用者執行。

使用[應用程式 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost)上載應用程式的 JSON 檔並獲得應用程式 ID。

### <a name="applications-post-request"></a>應用 POST 請求

使用**appFile**參數將設定檔作為請求正文的一部分發送。

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>應用程式 POST 回應

在回應中返回創建的應用程式 ID。 調用下一個 API 時，需要應用程式 ID 將設定檔與代碼檔相關聯。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>合同代碼 POST 請求

通過傳遞應用程式 ID 來上載應用程式的可靠性代碼檔，使用[應用程式協定代碼 POST API。](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) 有效負載可以是單個實體檔或包含實體檔的壓縮檔。

取代下列值：

| 參數 | 值 |
|-----------|-------|
| [應用程式 Id] | 從應用程式 POST API 傳回值。 |
| [分類帳 Id] | 分類帳的索引。 該值通常為 1。 您還可以檢查[分類帳表](data-sql-management-studio.md)的值。 |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>合同代碼 POST 回應

如果成功，回應包括來自[合同代碼表中](data-sql-management-studio.md)創建的合同代碼 ID。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>將角色指派給使用者

通過傳遞應用程式 ID、使用者 ID 和應用程式角色 ID 來使用[應用程式角色分配 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost)在指定的區塊鏈應用程式中創建使用者到角色映射。 此方法只能由工作臺管理員的使用者執行。

### <a name="role-assignments-post-request"></a>角色指派 POST 請求

取代下列值：

| 參數 | 值 |
|-----------|-------|
| [應用程式 Id] | 從應用程式 POST API 傳回值。 |
| [使用者 Id] | [使用者表中](data-sql-management-studio.md)的使用者 ID 值。 |
| [應用程式RoleId] | 應用程式角色 ID 值與[應用程式角色表中](data-sql-management-studio.md)的應用程式 ID 相關聯。 |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>角色指派 POST 回應

如果成功，回應包括來自[角色指派表](data-sql-management-studio.md)中創建的角色指派 ID。

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>列出應用程式

使用[應用程式 GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget)為使用者檢索所有區塊鏈工作臺應用程式。 在此示例中，登錄使用者有權訪問兩個應用程式：

- [資產轉送](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [冷藏運輸](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>應用程式獲取請求

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>應用程式獲取回應

回應會列出使用者在 Blockchain Workbench 中可以存取的所有區塊鏈應用程式。 Blockchain Workbench 管理員可取得所有區塊鏈應用程式。 非工作臺管理員獲取他們至少具有一個關聯應用程式角色或關聯的智慧合約實例角色的所有區塊鏈應用程式。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>列出應用程式的工作流程

使用[應用程式工作流 GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget)列出使用者在區塊鏈工作臺中有權訪問的指定區塊鏈應用程式的所有工作流。 每個區塊鏈應用程式都有一或多個工作流程，每個工作流程都會有零個執行個體或有智慧合約執行個體。 如果區塊鏈用戶端應用程式只有一個工作流程，則建議略過可讓使用者選取適用工作流程的使用者體驗流程。

### <a name="application-workflows-request"></a>應用程式工作流請求

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>應用程式工作流回應

Blockchain Workbench 管理員可取得所有區塊鏈工作流程。 非 Workbench 管理員可取得擁有該工作流程至少一個相關聯應用程式角色或與智慧合約執行個體角色相關聯的所有工作流程。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>創建合同實例

使用[合同 V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost)為工作流創建新的智慧合同實例。 僅當使用者與應用程式角色關聯時，使用者才能創建新的智慧協定實例，應用程式角色可以為工作流啟動智慧協定實例。

> [!NOTE]
> 在此示例中，使用 API 的版本 2。 版本 2 協定 API 為關聯的預配狀態欄位提供了更細細微性。

### <a name="contracts-post-request"></a>合同 POST 請求

取代下列值：

| 參數 | 值 |
|-----------|-------|
| [工作流 Id] | 工作流 ID 值是[工作流表中](data-sql-management-studio.md)的合約的建構函式 ID。 |
| [合同代碼Id] | [合同代碼表中](data-sql-management-studio.md)的合同代碼 ID 值。 關聯要創建的合同實例的應用程式 ID 和分類帳 ID。 |
| [連接 Id] | [連接表](data-sql-management-studio.md)中的連接 ID 值。 |

對於請求正文，使用以下資訊設置值：

| 參數 | 值 |
|-----------|-------|
| 工作流功能ID | [來自工作流函數表](data-sql-management-studio.md)的 ID。 |
| 工作流指令引數 | 傳遞給建構函式的參數的值對的名稱。 對於每個參數，請使用[工作流函數參數](data-sql-management-studio.md)表中的工作流函數參數 ID 值。 |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>合同 POST 回應

如果成功，角色指派 API 將從[合同指令引數表中返回合同操作](data-sql-management-studio.md)ID。

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>列出工作流程的智慧合約執行個體

使用[合同 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget)顯示工作流的所有智慧合同實例。 或者，您可以讓使用者深入了解任何已顯示的智慧合約執行個體。

### <a name="contracts-request"></a>合同請求

在此範例中，請將使用者視為想要與其中一個智慧合約執行個體互動，以採取動作。

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>合同回應

回應列出指定工作流的所有智慧協定實例。 Workbench 管理員可取得所有智慧合約執行個體。 非 Workbench 管理員可取得擁有該智慧合約執行個體至少一個相關聯應用程式角色或與智慧合約執行個體角色相關聯的所有智慧合約執行個體。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>列出合約的可用動作

使用[合同操作 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget)顯示給定協定狀態的可用使用者操作。 

### <a name="contract-action-request"></a>合同操作請求

在此示例中，使用者正在查看他們創建的新智慧合約的所有可用操作。

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>合同操作回應

回應會根據所指定智慧合約執行個體的目前狀態列出使用者可採取的所有動作。

* 修改：允許使用者資產的描述和價格。
* 終止：允許使用者結束資產的合約。

如果使用者具有相關聯的應用程式角色，或與所指定智慧合約目前狀態的智慧合約執行個體角色的相關聯，則使用者會得到所有適用的動作。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>為合約執行動作

使用[合同操作 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost)對指定的智慧合同實例執行操作。

### <a name="contract-action-post-request"></a>合同操作 POST 請求

在這種情況下，請考慮使用者希望修改資產的描述和價格的情況。

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

使用者只能夠根據所指定智慧合約執行個體目前狀態，以及使用者相關聯的應用程式角色或智慧合約執行個體角色，來執行動作。

### <a name="contract-action-post-response"></a>合同操作 POST 回應

如果 post 成功，會傳回 HTTP 200 OK 回應，但無回應本文。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>後續步驟

有關區塊鏈工作臺 API 的參考資訊，請參閱[Azure 區塊鏈工作臺 REST API 參考](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)。
