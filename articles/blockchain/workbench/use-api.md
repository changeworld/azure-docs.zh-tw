---
title: 使用 Azure Blockchain Workbench REST Api
description: 如何使用 Azure Blockchain Workbench 預覽 REST API 的案例
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87003061"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>使用 Azure Blockchain Workbench Preview REST API

Azure Blockchain Workbench Preview REST API 讓開發人員和資訊工作者能夠建立豐富的區塊鏈應用程式整合。 本文將重點說明如何使用工作臺 REST API 的幾個案例。 例如，假設您想要建立自訂區塊鏈用戶端，以允許已登入的使用者查看其指派的區塊鏈應用程式並與其互動。 用戶端可以使用 Blockchain Workbench API 來查看合約實例，並對智慧型合約採取動作。

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API 端點

Blockchain Workbench Api 可透過部署的端點來存取。 若要取得部署的 API 端點 URL：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側流覽窗格中，選取 [ **資源群組**]。
1. 選擇您已部署 Blockchain Workbench 的資源組名。
1. 選取 [類型]**** 資料行標題，依類型按字母順序來排序清單。
1. 有兩項屬於 **App Service** 類型的資源。 *使用*"-api" 尾碼來選取**App Service**類型的資源。
1. 在 App Service **總覽**中，將 **url** 值（代表 API 端點 url）複製到您部署的 Blockchain Workbench。

    ![App service API 端點 URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>驗證

Blockchain Workbench REST API 的要求會以 Azure Active Directory (Azure AD) 來保護。

若要對 REST Api 提出已驗證的要求，用戶端程式代碼需要使用有效的認證進行驗證，您才能呼叫 API。 驗證會透過 Azure AD 在不同的執行者之間協調，並提供用戶端 [存取權杖](../../active-directory/develop/developer-glossary.md#access-token) 作為驗證證明。 然後，權杖會在 REST API 要求的 HTTP 授權標頭中傳送。 若要深入瞭解 Azure AD authentication，請參閱 [適用于開發人員的 Azure Active Directory](../../active-directory/develop/index.yml)。

如需如何進行驗證的範例，請參閱 [REST API 範例](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) 。

## <a name="using-postman"></a>使用 Postman

如果您想要測試或實驗工作臺 Api，您可以使用 [Postman](https://www.postman.com) 對您的部署進行 API 呼叫。 從 GitHub[下載工作臺 API 要求的範例 Postman 集合](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman)。 如需驗證和使用範例 API 要求的詳細資訊，請參閱讀我檔案。

## <a name="create-an-application"></a>建立應用程式

您可以使用兩個 API 呼叫來建立 Blockchain Workbench 應用程式。 只有身為工作臺系統管理員的使用者才能執行此方法。

使用 [應用程式 POST API](/rest/api/azure-blockchain-workbench/applications/applicationspost) 來上傳應用程式的 JSON 檔案，並取得應用程式識別碼。

### <a name="applications-post-request"></a>應用程式 POST 要求

使用 **appFile** 參數將設定檔傳送為要求主體的一部分。

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>應用程式張貼回應

在回應中會傳回所建立的應用程式識別碼。 當您呼叫下一個 API 時，您需要應用程式識別碼才能將設定檔與程式碼檔案產生關聯。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>合約程式碼 POST 要求

傳遞應用程式識別碼以上傳應用程式的密度程式碼檔案，以使用 [應用程式合約程式碼 POST API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) 。 裝載可以是單一密度檔案，或包含密度檔案的 zip 壓縮檔案。

取代下列值：

| 參數 | 值 |
|-----------|-------|
| ApplicationId | 應用程式張貼 API 的傳回值。 |
| {ledgerId} | 總帳的索引。 此值通常是1。 您也可以檢查 [總帳資料表](data-sql-management-studio.md) 中的值。 |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>合約代碼張貼回應

如果成功，回應會包含 [ContractCode 資料表](data-sql-management-studio.md)中所建立的合約程式碼識別碼。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>將角色指派給使用者

藉由傳遞應用程式識別碼、使用者識別碼和應用程式角色識別碼，以在指定的區塊鏈應用程式中建立使用者對角色的對應，來使用 [應用程式角色指派 POST API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) 。 只有身為工作臺系統管理員的使用者才能執行此方法。

### <a name="role-assignments-post-request"></a>角色指派 POST 要求

取代下列值：

| 參數 | 值 |
|-----------|-------|
| ApplicationId | 應用程式張貼 API 的傳回值。 |
| UserId | [使用者資料表](data-sql-management-studio.md)中的使用者識別碼值。 |
| {applicationRoleId} | 從 [ApplicationRole 資料表](data-sql-management-studio.md)中的應用程式識別碼相關聯的應用程式角色識別碼值。 |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>回應後的角色指派

如果成功，回應會包含 [RoleAssignment 資料表](data-sql-management-studio.md)中所建立的角色指派識別碼。

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>列出應用程式

使用 [應用程式取得 API](/rest/api/azure-blockchain-workbench/applications/applicationsget) 來取得使用者的所有 Blockchain Workbench 應用程式。 在此範例中，已登入的使用者可以存取兩個應用程式：

- [資產轉送](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [冷藏運輸](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>應用程式取得要求

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>應用程式取得回應

回應會列出使用者在 Blockchain Workbench 中可以存取的所有區塊鏈應用程式。 Blockchain Workbench 管理員可取得所有區塊鏈應用程式。 非工作臺系統管理員會取得他們至少有一個相關聯應用程式角色或相關聯智慧合約實例角色的所有區塊鏈應用程式。

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

使用 [應用程式工作流程的 GET API](/rest/api/azure-blockchain-workbench/applications/workflowsget) ，列出指定之區塊鏈應用程式的所有工作流程，使用者在 Blockchain Workbench 中可存取此應用程式。 每個區塊鏈應用程式都有一或多個工作流程，每個工作流程都會有零個執行個體或有智慧合約執行個體。 如果區塊鏈用戶端應用程式只有一個工作流程，則建議略過可讓使用者選取適用工作流程的使用者體驗流程。

### <a name="application-workflows-request"></a>應用程式工作流程要求

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>應用程式工作流程回應

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

## <a name="create-a-contract-instance"></a>建立合約實例

使用 [合約 V2 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) 來為工作流程建立新的智慧合約實例。 只有在使用者與應用程式角色相關聯時，使用者才能建立新的智慧合約實例，以初始化工作流程的智慧合約實例。

> [!NOTE]
> 在此範例中，會使用第2版的 API。 第2版合約 Api 可為相關聯的 ProvisioningStatus 欄位提供更細微的資料細微性。

### <a name="contracts-post-request"></a>合約張貼要求

取代下列值：

| 參數 | 值 |
|-----------|-------|
| 工作 | 工作流程識別碼值是來自 [工作流程資料表](data-sql-management-studio.md)的合約 ConstructorID。 |
| {contractCodeId} | [ContractCode 資料表](data-sql-management-studio.md)中的合約程式碼識別碼值。 將您想要建立之合約實例的應用程式識別碼和總帳識別碼相互關聯。 |
| ConnectionId | [連接資料表](data-sql-management-studio.md)的連接識別碼值。 |

針對要求本文，請使用下列資訊設定值：

| 參數 | 值 |
|-----------|-------|
| workflowFunctionID | [WorkflowFunction 資料表](data-sql-management-studio.md)中的識別碼。 |
| workflowActionParameters | 傳遞給函式之參數的名稱值配對。 針對每個參數，使用 [WorkflowFunctionParameter](data-sql-management-studio.md) 資料表中的 workflowFunctionParameterID 值。 |

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

### <a name="contracts-post-response"></a>合約張貼回應

如果成功，角色指派 API 會傳回 [ContractActionParameter 資料表](data-sql-management-studio.md)中的 ContractActionID。

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>列出工作流程的智慧合約執行個體

使用 [合約 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) 來顯示工作流程的所有智慧合約實例。 或者，您可以讓使用者深入了解任何已顯示的智慧合約執行個體。

### <a name="contracts-request"></a>合約要求

在此範例中，請將使用者視為想要與其中一個智慧合約執行個體互動，以採取動作。

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>合約回應

回應會列出指定工作流程的所有智慧合約實例。 Workbench 管理員可取得所有智慧合約執行個體。 非 Workbench 管理員可取得擁有該智慧合約執行個體至少一個相關聯應用程式角色或與智慧合約執行個體角色相關聯的所有智慧合約執行個體。

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

使用 [合約動作取得 API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) 來顯示指定合約狀態的可用使用者動作。 

### <a name="contract-action-request"></a>合約動作要求

在此範例中，使用者正在查看其所建立之新智慧型合約的所有可用動作。

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>合約動作回應

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

使用 [合約動作 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) 針對指定的智慧型合約實例採取動作。

### <a name="contract-action-post-request"></a>合約動作 POST 要求

在此情況下，請考慮使用者想要修改資產的描述和價格的案例。

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

### <a name="contract-action-post-response"></a>合約動作張貼回應

如果 post 成功，會傳回 HTTP 200 OK 回應，但無回應本文。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>接下來的步驟

如需 Blockchain Workbench Api 的參考資訊，請參閱 [Azure Blockchain Workbench REST API 參考](/rest/api/azure-blockchain-workbench)。
