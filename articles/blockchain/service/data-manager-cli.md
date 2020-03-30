---
title: 使用 Azure CLI 配置區塊鏈資料管理器 - Azure 區塊鏈服務
description: 使用 Azure CLI 創建和管理 Azure 區塊鏈服務的區塊鏈資料管理器
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455931"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>使用 Azure CLI 設定區塊鏈資料管理員

為 Azure 區塊鏈服務配置區塊鏈資料管理器，以捕獲將其發送到 Azure 事件網格主題的區塊鏈資料。

要配置區塊鏈資料管理器實例，您需要：

* 創建區塊鏈管理器實例
* 創建 Azure 區塊鏈服務事務節點的輸入
* 創建 Azure 事件網格主題的輸出
* 新增區塊鏈應用程式
* 啟動實例

## <a name="prerequisites"></a>Prerequisites

* 安裝最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)並使用`az login`登錄。
* 完整的[快速入門：使用視覺化工作室代碼連接到 Azure 區塊鏈服務聯盟網路](connect-vscode.md)
* 創建[事件網格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]**** 即可。 您還可以通過 訪問[https://shell.azure.com/bash](https://shell.azure.com/bash)在單獨的瀏覽器選項卡中啟動雲外殼。 選擇 **"複製"** 以複製代碼塊，將其粘貼到雲外殼中，然後按 Enter 以運行它。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.51 版或更新版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下面的示例在*東部*位置創建名為*myResourceGroup*的資源組：

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理器實例監視 Azure 區塊鏈服務事務節點。 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 資源組名稱，用於創建區塊鏈資料管理器實例的位置。 |
| NAME | 區塊鏈資料管理器實例的名稱。 |
| 資源類型 | 區塊鏈資料管理器實例的資源類型是**微軟.** |
| is-full-object | 指示屬性包含觀察程式資源的選項。 |
| properties | 包含觀察程式資源屬性的 JSON 格式字串。 可以作為字串或檔傳遞。  |

### <a name="create-instance-examples"></a>創建實例示例

JSON 配置示例，用於**在美國東部**區域創建區塊鏈管理器實例。

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| 元素 | 描述 |
|---------|-------------|
| location | 創建觀察程式資源的區域 |
| properties | 創建觀察程式資源時要設置的屬性 |

使用 JSON 字串創建名為 *"我的觀察程式"* 的區塊鏈資料管理器實例進行配置。

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

使用 JSON 設定檔創建名為 *"我的觀察程式"* 的區塊鏈資料管理器實例。

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>建立輸入

輸入將區塊鏈資料管理器連接到 Azure 區塊鏈服務事務節點。 只有有權訪問事務節點的使用者才能創建連接。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 資源組名稱，用於創建輸入資源。 |
| NAME | 輸入的名稱。 |
| namespace | 使用**Microsoft.區塊鏈**提供程式命名空間。 |
| 資源類型 | 區塊鏈資料管理器輸入的資源類型是**輸入**。 |
| 父系 (parent) | 輸入關聯的觀察器的路徑。 例如，**觀察者/我的觀察者**。 |
| is-full-object | 指示屬性包含輸入資源的選項。 |
| properties | 包含輸入資源屬性的 JSON 格式字串。 可以作為字串或檔傳遞。 |

### <a name="input-examples"></a>輸入示例

配置 JSON 示例，用於*在美國東部*區域創建連接到\<區塊鏈成員\>的輸入資源。

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| 元素 | 描述 |
|---------|-------------|
| location | 創建輸入資源的區域。 |
| 輸入類型 | Azure 區塊鏈服務成員的分類帳類型。 目前，**支援以圖姆**。 |
| resourceId | 輸入連接到的事務節點。 將\<訂閱\>ID、\<資源\>組和\<區塊鏈成員\>替換為事務節點資源的值。 輸入連接到 Azure 區塊鏈服務成員的預設事務節點。 |

使用 JSON 字串為*我的觀察程式*創建名為*myInput*的輸入進行配置。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

使用 JSON 設定檔為*觀察程式*創建名為*myInput*的輸入。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>創建輸出

輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 您可將區塊鏈資料傳送至單一目的地，或將區塊鏈資料傳送至多個目的地。 區塊鏈資料管理員會針對任何指定的區塊鏈資料管理員執行個體，支援多個事件方格主題輸出連線。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 要在何處創建輸出資源的資源組名稱。 |
| NAME | 輸出的名稱。 |
| namespace | 使用**Microsoft.區塊鏈**提供程式命名空間。 |
| 資源類型 | 區塊鏈資料管理器輸出的資源類型是**輸出**。 |
| 父系 (parent) | 與輸出關聯的觀察器的路徑。 例如，**觀察者/我的觀察者**。 |
| is-full-object | 指示屬性包含輸出資源的選項。 |
| properties | 包含輸出資源屬性的 JSON 格式字串。 可以作為字串或檔傳遞。 |

### <a name="output-examples"></a>輸出示例

配置 JSON 示例，用於*在美國東部*區域創建連接到事件網格主題（稱為\<事件網格主題）\>的輸出資源。

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| 元素 | 描述 |
|---------|-------------|
| location | 創建輸出資源的區域。 |
| 輸出類型 | 輸出類型。 目前，**事件網格**受支援。 |
| resourceId | 與輸出連接的資源。 將\<訂閱\>ID、\<資源\>組和\<區塊鏈成員\>替換為事件網格資源的值。 |

為我*的觀程式*創建一個名為 *"我的輸出"* 的輸出，該輸出使用 JSON 配置字串連接到事件網格主題。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

為我*的觀程式*創建一個名為 *"我的輸出"* 的輸出，該輸出使用 JSON 設定檔連接到事件網格主題。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>添加區塊鏈應用程式

如果您添加區塊鏈應用程式，區塊鏈資料管理器會解碼應用程式的事件和屬性狀態。 否則，僅發送原始塊和原始交易資料。 區塊鏈資料管理器還會在部署合同時發現合約地址。 您可以將多個區塊鏈應用程式添加到區塊鏈資料管理器實例中。


> [!IMPORTANT]
> 目前，聲明實體陣列[類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)或[映射類型的](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)區塊鏈應用程式並不完全受支援。 聲明為數組或映射類型的屬性不會在*合同屬性Msg*或*解碼合同事件Mg*消息中解碼。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 要在何處創建應用程式資源的資源組名稱。 |
| NAME | 應用程式的名稱。 |
| namespace | 使用**Microsoft.區塊鏈**提供程式命名空間。 |
| 資源類型 | 區塊鏈資料管理器應用程式的資源類型是**工件**。 |
| 父系 (parent) | 應用程式關聯的觀察程式的路徑。 例如，**觀察者/我的觀察者**。 |
| is-full-object | 指示屬性包含應用程式資源的選項。 |
| properties | 包含應用程式資源屬性的 JSON 格式字串。 可以作為字串或檔傳遞。 |

### <a name="blockchain-application-examples"></a>區塊鏈應用程式示例

配置 JSON 示例用於*在美國東部*區域創建應用程式資源，用於監視由協定 ABI 和位元組碼定義的智慧協定。

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| 元素 | 描述 |
|---------|-------------|
| location | 創建應用程式資源的區域。 |
| 工件類型 | 應用程式的類型。 目前，**支援以天智慧合約**。 |
| 阿比菲勒 | 智慧合約 ABI JSON 檔的 URL。 有關獲取合同 ABI 和創建 URL 的詳細資訊，請參閱[獲取合同 ABI 和位元組碼](data-manager-portal.md#get-contract-abi-and-bytecode)以及[創建合同 ABI 和位元組碼 URL](data-manager-portal.md#create-contract-abi-and-bytecode-url)。 |
| 位元組碼檔Url | 智慧合約部署位元組碼 JSON 檔的 URL。 有關獲取智慧合約部署位元組碼和創建 URL 的詳細資訊，請參閱[獲取合同 ABI 和位元組碼](data-manager-portal.md#get-contract-abi-and-bytecode)以及[創建協定 ABI 和位元組碼 URL](data-manager-portal.md#create-contract-abi-and-bytecode-url)。 注意：區塊鏈資料管理器需要**部署的位元組碼**。 |
| 查詢目標型別 | 已發佈的訊息類型。 指定**合同屬性**將發佈*合同屬性Msg*訊息類型。 指定**合同事件**將發佈 *"解碼合同事件"消息*類型。 注意：*原始塊和事務Msg*和*原始事務合同創建Msg*訊息類型始終發佈。 |

為我*的觀程式*創建名為*myApplication*的應用程式，用於監視由 JSON 字串定義的智慧協定。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

為我*的觀程式*創建名為*myApplication*的應用程式，該應用程式監視使用 JSON 設定檔定義的智慧合約。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>啟動實例

運行時，區塊鏈管理器實例從定義的輸入監控區塊鏈事件，並將資料發送到定義的輸出。

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 參數 | 描述 |
|-----------|-------------|
| 動作 | 使用**開始**運行觀察程式。 |
| ids | 觀察程式資源識別碼。 將\<訂閱\>ID、\<資源\>組和\<觀察程式\>名稱替換為觀察程式資源的值。|

### <a name="start-instance-example"></a>啟動實例示例

啟動名為 *"我的觀察器"的*區塊鏈資料管理器實例。

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>停止實例

停止區塊鏈資料管理器實例。

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 參數 | 描述 |
|-----------|-------------|
| 動作 | 使用**停止**停止觀察器。 |
| ids | 觀察程式的名稱。 將\<訂閱\>ID、\<資源\>組和\<觀察程式\>名稱替換為觀察程式資源的值。 |

### <a name="stop-watcher-example"></a>停止觀察器示例

停止名為 *"我的觀察器"的*實例。

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>刪除實例

刪除區塊鏈資料管理器實例。

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 要刪除的觀察程式的資源組名稱。 |
| NAME | 要刪除的觀察程式的名稱。 |
| 資源類型 | 區塊鏈資料管理器觀察者的資源類型是**微軟.** |

### <a name="delete-instance-example"></a>刪除實例示例

刪除*myRG*資源組中名為 *"我的觀察程式"* 的實例。

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>後續步驟

嘗試使用區塊鏈資料管理器和 Azure Cosmos DB 創建區塊鏈事務消息資源管理器的下一教程。

> [!div class="nextstepaction"]
> [使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)
