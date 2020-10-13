---
title: 使用訊息與 Azure Blockchain Workbench 整合
description: 使用訊息將 Azure Blockchain Workbench 預覽與其他系統整合的總覽。
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74324499"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 訊息整合

Azure Blockchain Workbench 除了提供 REST API，也會提供以傳訊為基礎的整合。 Workbench 會透過 Azure 事件方格發佈以總帳為中心的事件，讓下游取用者能夠根據這些事件來擷取資料或採取動作。 對於需要可靠傳訊功能的用戶端，Azure Blockchain Workbench 也會將訊息傳遞至 Azure 服務匯流排端點。

## <a name="input-apis"></a>輸入 API

如果您想要從外部系統起始交易，以便建立使用者、建立合約及更新合約，您可使用傳訊輸入 API 在總帳上執行交易。 如需示範輸入 API 的範例，請參閱[傳訊整合範例](https://aka.ms/blockchain-workbench-integration-sample)。

以下是目前可用的輸入 API。

### <a name="create-user"></a>建立使用者

建立新使用者。

要求需要下列欄位：

| **名稱**             | **描述**                                      |
|----------------------|------------------------------------------------------|
| requestId            | 用戶端提供的 GUID                                |
| firstName            | 使用者的名字                              |
| lastName             | 使用者的姓氏                               |
| emailAddress         | 使用者的電子郵件地址                           |
| externalId           | 使用者的 Azure AD 物件識別碼                      |
| connectionId         | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本                            |
| messageName          | **CreateUserRequest**                               |

範例：

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**              | **描述**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | 用戶端提供的 GUID |
| userId                | 所建立使用者的識別碼 |
| userChainIdentifier   | 區塊鏈網路上所建立使用者的位址。 在乙太坊中，位址是使用者的 **連鎖** 位址。 |
| connectionId          | 區塊鏈連線的唯一識別碼|
| messageSchemaVersion  | 傳訊結構描述版本 |
| messageName           | **CreateUserUpdate** |
| status                | 使用者建立要求的狀態。  如果成功，值為 [成功]****。 失敗時，值為 [失敗]****。     |
| additionalInformation | 其他根據狀態提供的資訊 |

來自 Blockchain Workbench 的成功**建立使用者**回應範例：

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>建立合約

建立新的合約。

要求需要下列欄位：

| **名稱**             | **描述**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | 用戶端提供的 GUID |
| userChainIdentifier  | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，此位址是使用者的**鏈結**位址。 |
| applicationName      | 應用程式的名稱 |
| version              | 應用程式的版本。 如果您已啟用多個版本的應用程式，則此為必要項目。 否則，版本是選擇性項目。 如需有關應用程式版本的詳細資訊，請參閱 [Azure Blockchain Workbench 應用程式版本](version-app.md)。 |
| workflowName         | 工作流程的名稱 |
| 參數           | 可供建立合約的參數輸入 |
| connectionId         | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本 |
| messageName          | **CreateContractRequest** |

範例：

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**                 | **描述**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | 用戶端提供的 GUID                                                             |
| contractId               | Azure Blockchain Workbench 內合約的唯一識別碼 |
| contractLedgerIdentifier | 總帳上合約的位址                                            |
| connectionId             | 區塊鏈連線的唯一識別碼                               |
| messageSchemaVersion     | 傳訊結構描述版本                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | 合約建立要求的狀態。  可能的值：[已提交]****、[已認可]****、[失敗]****。  |
| additionalInformation    | 其他根據狀態提供的資訊                              |

來自 Blockchain Workbench 的已提交**建立合約**回應範例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

來自 Blockchain Workbench 的已認可**建立合約**回應範例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>建立合約動作

建立新的合約動作。

要求需要下列欄位：

| **名稱**                 | **描述**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | 用戶端提供的 GUID |
| userChainIdentifier      | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，此位址是使用者的**鏈結**位址。 |
| contractLedgerIdentifier | 總帳上合約的位址 |
| version                  | 應用程式的版本。 如果您已啟用多個版本的應用程式，則此為必要項目。 否則，版本是選擇性項目。 如需有關應用程式版本的詳細資訊，請參閱 [Azure Blockchain Workbench 應用程式版本](version-app.md)。 |
| workflowFunctionName     | 工作流程函式的名稱 |
| 參數               | 可供建立合約的參數輸入 |
| connectionId             | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion     | 傳訊結構描述版本 |
| messageName              | **CreateContractActionRequest** |

範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**              | **描述**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | 用戶端提供的 GUID|
| contractId            | Azure Blockchain Workbench 內合約的唯一識別碼 |
| connectionId          | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion  | 傳訊結構描述版本 |
| messageName           | **CreateContractActionUpdate** |
| status                | 合約動作要求的狀態。 可能的值：[已提交]****、[已認可]****、[失敗]****。                         |
| additionalInformation | 其他根據狀態提供的資訊 |

來自 Blockchain Workbench 的已提交**建立合約動作**回應範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

來自 Blockchain Workbench 的已認可**建立合約動作**回應範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>輸入 API 錯誤碼和訊息

**錯誤碼 4000：不正確的要求錯誤**
- 無效的 connectionId
- CreateUserRequest deserialization 失敗
- CreateContractRequest deserialization 失敗
- CreateContractActionRequest deserialization 失敗
- 應用程式 {依應用程式名稱識別} 不存在
- 應用程式 {依應用程式名稱識別} 沒有工作流程
- UserChainIdentifier 不存在
- 合約 {依總帳識別碼識別} 不存在
- 合約 {依總帳識別碼識別} 沒有函式 {工作流程函式名稱}
- UserChainIdentifier 不存在

**錯誤碼 4090：衝突錯誤**
- 使用者已經存在
- 合約已經存在
- 合約動作已經存在

**錯誤碼 5000：內部伺服器錯誤**
- 例外狀況訊息

## <a name="event-notifications"></a>事件通知

事件通知可用來通知使用者和下游系統，讓其知道 Blockchain Workbench 中所發生的事件以及 Blockchain Workbench 所連線到的區塊鏈網路。 事件通知可直接在程式碼中取用，或與 Logic Apps 和 Flow 等工具搭配使用，以觸發將資料傳送到下游系統的流程。

如需各種可收到訊息的詳細資料，請參閱[通知訊息參考](#notification-message-reference)。

### <a name="consuming-event-grid-events-with-azure-functions"></a>使用 Azure Functions 取用事件方格事件

如果使用者想要使用事件方格來收到有關 Blockchain Workbench 中所發生事件的通知，您可以使用 Azure Functions 從事件方格取用事件。

1. 在 Azure 入口網站中建立 **Azure 函數應用程式** 。
2. 建立新的函式。
3. 找到事件方格範本。 隨即會顯示用於讀取訊息的基本範本程式碼。 視需要修改程式碼。
4. 儲存函式。 
5. 從 Blockchain Workbench 的資源群組中選取事件方格。

### <a name="consuming-event-grid-events-with-logic-apps"></a>使用 Logic Apps 取用事件方格事件

1. 在 Azure 入口網站中建立新的 **Azure 邏輯應用程式**。
2. 在入口網站中開啟 Azure 邏輯應用程式時，系統會提示您選取觸發程序。 選取 [Azure 事件方格 - 發生資源事件時]****。
3. 當工作流程設計工具顯示時，系統會提示您登入。
4. 選取訂用帳戶。 資源作為 **Microsoft.EventGrid.Topics**。 從 Azure Blockchain Workbench 資源群組的資源名稱中選取 [資源名稱]****。
5. 從 Blockchain Workbench 的資源群組中選取事件方格。

## <a name="using-service-bus-topics-for-notifications"></a>使用服務匯流排主題來進行通知

服務匯流排主題可用來通知使用者在 Blockchain Workbench 中所發生的事件。 

1. 在 Workbench 的資源群組內瀏覽至服務匯流排。
2. 選取 [主題]****。
3. 選取 **egress-topic**。
4. 對此主題建立新的訂用帳戶。 取得其金鑰。
5. 建立程式，從這個訂用帳戶訂閱事件。

### <a name="consuming-service-bus-messages-with-logic-apps"></a>使用 Logic Apps 取用服務匯流排訊息

1. 在 Azure 入口網站中建立新的 **Azure 邏輯應用程式**。
2. 在入口網站中開啟 Azure 邏輯應用程式時，系統會提示您選取觸發程序。 在搜尋方塊中輸入**服務匯流排**，然後針對在與服務匯流排互動時所要使用的類型，選取適當的觸發程序。 例如，**服務匯流排 - 主題訂用帳戶中收到訊息時 (自動完成)**。
3. 當工作流程設計工具顯示時，指定服務匯流排的連線資訊。
4. 選取訂用帳戶，並指定 **workbench-external** 的主題。
5. 開發應用程式邏輯來利用這個觸發程序所傳來的訊息。

## <a name="notification-message-reference"></a>通知訊息參考

根據 **messageName**而定，通知訊息具有下列其中一種訊息類型。

### <a name="block-message"></a>區塊訊息

包含個別區塊的相關資訊。 *BlockMessage* 包含具有區塊層級資訊的區段和具有交易資訊的區段。

| 名稱 | 描述 |
|------|-------------|
| block | 包含[區塊資訊](#block-information) |
| 交易 | 包含區塊的[交易資訊](#transaction-information)集合 |
| connectionId | 連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本 |
| messageName | **BlockMessage** |
| additionalInformation | 提供的其他資訊 |

#### <a name="block-information"></a>區塊資訊

| 名稱              | 描述 |
|-------------------|-------------|
| blockId           | Azure Blockchain Workbench 內區塊的唯一識別碼 |
| blockNumber       | 總帳上區塊的唯一識別碼 |
| blockHash         | 區塊的雜湊 |
| previousBlockHash | 上一個區塊的雜湊 |
| blockTimestamp    | 區塊的時間戳記 |

#### <a name="transaction-information"></a>交易資訊

| 名稱               | 描述 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 內所含交易的唯一識別碼 |
| transactionHash    | 總帳上交易的雜湊 |
| 從               | 交易來源總帳的唯一識別碼 |
| to                 | 交易目的地總帳的唯一識別碼 |
| provisioningStatus | 識別交易佈建程序的目前狀態。 可能的值包括： </br>0 – API 已在資料庫中建立交易</br>1 – 交易已傳送至總帳</br>2 – 交易已成功認可至總帳</br>3 或 4 – 交易無法認可至總帳</br>5 – 交易已成功認可至總帳 |

Blockchain Workbench 中的 *BlockMessage* 範例：

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>合約訊息

包含合約的相關資訊。 此訊息包含具有合約屬性的區段和具有交易資訊的區段。 已針對特定區塊修改合約的所有交易，都會包含在交易區段中。

| 名稱 | 描述 |
|------|-------------|
| blockId | Azure Blockchain Workbench 內區塊的唯一識別碼 |
| blockHash | 區塊的雜湊 |
| modifyingTransactions | [已修改合約的交易](#modifying-transaction-information) |
| contractId | Azure Blockchain Workbench 內合約的唯一識別碼 |
| contractLedgerIdentifier | 總帳所含合約的唯一識別碼 |
| contractProperties | [合約的屬性](#contract-properties) |
| isNewContract | 指出此合約是否為新建的。 可能的值為：true：此合約是新建的合約。 false：此合約是合約更新。 |
| connectionId | 連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本 |
| messageName | **ContractMessage** |
| additionalInformation | 提供的其他資訊 |

#### <a name="modifying-transaction-information"></a>修改交易資訊

| 名稱               | 描述 |
|--------------------|-------------|
| transactionId | Azure Blockchain Workbench 內所含交易的唯一識別碼 |
| transactionHash | 總帳上交易的雜湊 |
| 從 | 交易來源總帳的唯一識別碼 |
| to | 交易目的地總帳的唯一識別碼 |

#### <a name="contract-properties"></a>合約屬性

| 名稱               | 描述 |
|--------------------|-------------|
| workflowPropertyId | Azure Blockchain Workbench 內所含工作流程屬性的唯一識別碼 |
| NAME | 工作流程屬性的名稱 |
| value | 工作流程屬性的值 |

Blockchain Workbench 中的 *ContractMessage* 範例：

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>事件訊息：合約函式呼叫

包含叫用合約函式時的資訊，例如函式名稱、參數輸入，以及函式的呼叫端。

| 名稱 | 描述 |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| 呼叫者                      | [呼叫者資訊](#caller-information) |
| contractId                  | Azure Blockchain Workbench 內合約的唯一識別碼 |
| contractLedgerIdentifier    | 總帳所含合約的唯一識別碼 |
| functionName                | 函式的名稱 |
| 參數                  | [參數資訊](#parameter-information) |
| 交易                 | 交易資訊 |
| inTransactionSequenceNumber | 交易在區塊中的序號 |
| connectionId                | 連線的唯一識別碼 |
| messageSchemaVersion        | 傳訊結構描述版本 |
| messageName                 | **EventMessage** |
| additionalInformation       | 提供的其他資訊 |

#### <a name="caller-information"></a>呼叫者資訊

| 名稱 | 描述 |
|------|-------------|
| type | 呼叫端的類型，例如使用者或合約 |
| id | Azure Blockchain Workbench 內所含呼叫端的唯一識別碼 |
| ledgerIdentifier | 總帳所含呼叫端的唯一識別碼 |

#### <a name="parameter-information"></a>參數資訊

| 名稱 | 描述 |
|------|-------------|
| NAME | 參數名稱 |
| value | 參數值 |

#### <a name="event-message-transaction-information"></a>事件訊息交易資訊

| 名稱               | 描述 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 內所含交易的唯一識別碼 |
| transactionHash    | 總帳上交易的雜湊 |
| 從               | 交易來源總帳的唯一識別碼 |
| to                 | 交易目的地總帳的唯一識別碼 |

Blockchain Workbench 中的 *EventMessage ContractFunctionInvocation* 範例：

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>事件訊息：應用程式內嵌

包含資訊應用程式上傳至 Workbench 時的資訊，例如，上傳的應用程式名稱和版本。

| 名稱 | 描述 |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Azure Blockchain Workbench 內所含應用程式的唯一識別碼 |
| applicationName | 應用程式名稱 |
| applicationDisplayName | 應用程式顯示名稱 |
| applicationVersion | 應用程式版本 |
| applicationDefinitionLocation | 應用程式組態檔所在的 URL |
| contractCodes | 應用程式的[合約程式碼](#contract-code-information)集合 |
| applicationRoles | 應用程式的[應用程式角色](#application-role-information)集合 |
| applicationWorkflows | 應用程式的[應用程式工作流程](#application-workflow-information)集合 |
| connectionId | 連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本 |
| messageName | **EventMessage** |
| additionalInformation | 此處提供的其他資訊包括應用程式工作流程狀態和轉換資訊。 |

#### <a name="contract-code-information"></a>合約程式碼資訊

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含合約程式碼檔案的唯一識別碼 |
| ledgerId | Azure Blockchain Workbench 內所含總帳的唯一識別碼 |
| location | 合約程式碼檔案所在的 URL |

#### <a name="application-role-information"></a>應用程式角色資訊

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含應用程式角色的唯一識別碼 |
| NAME | 應用程式角色的名稱 |

#### <a name="application-workflow-information"></a>應用程式工作流程資訊

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含應用程式工作流程的唯一識別碼 |
| NAME | 應用程式工作流程名稱 |
| displayName | 應用程式工作流程顯示名稱 |
| functions | [應用程式工作流程](#workflow-function-information)的函式集合|
| states | [應用程式工作流程的狀態](#workflow-state-information)集合 |
| properties | 應用程式[工作流程屬性資訊](#workflow-property-information) |

##### <a name="workflow-function-information"></a>工作流程函式資訊

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含應用程式工作流程函式的唯一識別碼 |
| NAME | 函式名稱 |
| 參數 | 函式的屬性 |

##### <a name="workflow-state-information"></a>工作流程狀態資訊

| 名稱 | 描述 |
|------|-------------|
| NAME | 狀態名稱 |
| displayName | 狀態顯示名稱 |
| 樣式 | 狀態模式 (成功或失敗) |

##### <a name="workflow-property-information"></a>工作流程屬性資訊

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含應用程式工作流程屬性的唯一識別碼 |
| NAME | 屬性名稱 |
| type | 屬性類型 |

Blockchain Workbench 中的 *EventMessage ApplicationIngestion* 範例：

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>事件訊息：角色指派

包含在 Workbench 中為使用者指派角色時的資訊，例如，執行角色指派的人員、角色的名稱和對應的應用程式。

| 名稱 | 描述 |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Azure Blockchain Workbench 內所含應用程式的唯一識別碼 |
| applicationName | 應用程式名稱 |
| applicationDisplayName | 應用程式顯示名稱 |
| applicationVersion | 應用程式版本 |
| applicationRole        | [應用程式角色](#roleassignment-application-role)的相關資訊 |
| 指派者               | [指派者](#roleassignment-assigner)的相關資訊 |
| 受託人               | [受託人](#roleassignment-assignee)的相關資訊 |
| connectionId           | 連線的唯一識別碼 |
| messageSchemaVersion   | 傳訊結構描述版本 |
| messageName            | **EventMessage** |
| additionalInformation  | 提供的其他資訊 |

#### <a name="roleassignment-application-role"></a>RoleAssignment 應用程式角色

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含應用程式角色的唯一識別碼 |
| NAME | 應用程式角色的名稱 |

#### <a name="roleassignment-assigner"></a>RoleAssignment 指派者

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含使用者的唯一識別碼 |
| type | 指派者的類型 |
| chainIdentifier | 總帳所含使用者的唯一識別碼 |

#### <a name="roleassignment-assignee"></a>RoleAssignment 受託人

| 名稱 | 描述 |
|------|-------------|
| id | Azure Blockchain Workbench 內所含使用者的唯一識別碼 |
| type | 受託人的類型 |
| chainIdentifier | 總帳所含使用者的唯一識別碼 |

Blockchain Workbench 中的 *EventMessage RoleAssignment* 範例：

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>後續步驟

- [智慧合約整合模式](integration-patterns.md)
