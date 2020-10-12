---
title: 使用 Azure CLI Azure 區塊鏈服務設定區塊鏈資料管理員
description: 使用 Azure CLI 建立和管理 Azure 區塊鏈服務的區塊鏈資料管理員
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: f067f4413f6ad8541cd36a7581f9243bed4e195f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87023733"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>使用 Azure CLI 設定區塊鏈資料管理員

設定 Azure 區塊鏈服務的區塊鏈資料管理員，以區塊鏈資料傳送至 Azure 事件方格主題。

若要設定區塊鏈資料管理員的實例，您可以：

* 建立區塊鏈 Manager 實例
* 建立 Azure 區塊鏈服務交易節點的輸入
* 建立 Azure 事件方格主題的輸出
* 新增區塊鏈應用程式
* 啟動實例

## <a name="prerequisites"></a>Prerequisites

* 安裝最新的 [Azure CLI](/cli/azure/install-azure-cli) ，並使用登入 `az login` 。
* 完成 [快速入門：使用 Visual Studio Code 連接到 Azure 區塊鏈服務聯盟網路](connect-vscode.md)。 使用區塊鏈資料管理員時，建議使用 Azure 區塊鏈服務 *標準* 層。
* 建立[事件方格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.51 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理員實例會監視 Azure 區塊鏈服務交易節點。 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。 區塊鏈資料管理員發佈 **RawBlockAndTransactionMsg** 訊息，此訊息是從 web3 [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) 和 [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) 查詢傳回的資訊超集合。

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
| resource-group | 要在其中建立區塊鏈資料管理員實例的資源組名。 |
| NAME | 區塊鏈資料管理員實例的名稱。 |
| 資源類型 | 區塊鏈資料管理員實例的資源類型是 **區塊鏈/** 監看員。 |
| is-full-object | 指出屬性包含監看員資源的選項。 |
| properties | JSON 格式的字串，包含監看員資源的屬性。 可以用字串或檔案的形式傳遞。  |

### <a name="create-instance-examples"></a>建立實例範例

在 **美國東部** 區域建立區塊鏈管理員實例的 JSON 設定範例。

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| 元素 | 描述 |
|---------|-------------|
| location | 要在其中建立監看員資源的區域 |
| properties | 建立監看員資源時要設定的屬性 |

使用設定的 JSON 字串，建立名為 *mywatcher* 的區塊鏈資料管理員實例。

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

使用 JSON 設定檔，建立名為 *mywatcher* 的區塊鏈資料管理員實例。

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>建立輸入

輸入會將區塊鏈資料管理員連接到 Azure 區塊鏈服務交易節點。 只有具有交易節點存取權的使用者可以建立連接。

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
| resource-group | 要在其中建立輸入資源的資源組名。 |
| NAME | 輸入的名稱。 |
| namespace | 使用 **區塊鏈** 提供者命名空間。 |
| 資源類型 | 區塊鏈資料管理員輸入的資源類型為 **輸入**。 |
| 父系 (parent) | 與輸入相關聯之監看員的路徑。 例如，監看員 **/mywatcher**。 |
| is-full-object | 指出屬性包含輸入資源的選項。 |
| properties | JSON 格式的字串，包含輸入資源的屬性。 可以用字串或檔案的形式傳遞。 |

### <a name="input-examples"></a>輸入範例

設定 JSON 範例，可在連線到的 *美國東部* 區域中建立輸入資源 \<Blockchain member\> 。

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
| location | 要在其中建立輸入資源的區域。 |
| inputType | Azure 區塊鏈服務成員的總帳類型。 目前支援 **乙太坊** 。 |
| resourceId | 輸入所連接的交易節點。 將 \<Subscription ID\> 、 \<Resource group\> 和取代為 \<Blockchain member\> 交易節點資源的值。 輸入會連接到 Azure 區塊鏈服務成員的預設交易節點。 |

使用設定的 JSON 字串，建立名為 *myinput.x* 的 *mywatcher* 輸入。

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

使用 JSON 設定檔，建立名為 *myinput.x* 的 *mywatcher* 輸入。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>建立輸出

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
| resource-group | 要在其中建立輸出資源的資源組名。 |
| NAME | 輸出的名稱。 |
| namespace | 使用 **區塊鏈** 提供者命名空間。 |
| 資源類型 | 區塊鏈資料管理員輸出的資源類型是 **輸出**。 |
| 父系 (parent) | 與輸出相關聯之監看員的路徑。 例如，監看員 **/mywatcher**。 |
| is-full-object | 指出屬性包含輸出資源的選項。 |
| properties | JSON 格式的字串，包含輸出資源的屬性。 可以用字串或檔案的形式傳遞。 |

### <a name="output-examples"></a>輸出範例

設定 JSON 範例，可在 *美國東部* 區域建立輸出資源，該資源會連線到名為的事件方格主題 \<event grid topic\> 。

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
| location | 要在其中建立輸出資源的區域。 |
| outputType | 輸出的類型。 目前支援 **EventGrid** 。 |
| resourceId | 輸出所連接的資源。 \<Subscription ID\> \<Resource group\> \<Blockchain member\> 以事件方格資源的值取代、和。 |

建立名為 *myoutput* 的輸出 *，該輸出* 會使用 JSON 設定字串連接到事件方格主題。

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

建立名為 *myoutput* 的輸出，該 *輸出會使用* JSON 設定檔連接到事件方格主題。

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

## <a name="add-blockchain-application"></a>新增區塊鏈應用程式

如果您新增區塊鏈應用程式，區塊鏈資料管理員會將應用程式的事件和屬性狀態解碼。 否則，只會傳送原始區塊和原始交易資料。 當部署合約時，區塊鏈資料管理員也會探索合約位址。 您可以將多個區塊鏈應用程式新增至區塊鏈資料管理員實例。


> [!IMPORTANT]
> 目前，未完全支援宣告密度 [陣列類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) 或 [對應類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) 的區塊鏈應用程式。 宣告為數組或對應類型的屬性不會在 *ContractPropertiesMsg* 或 *DecodedContractEventsMsg* 訊息中解碼。

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
| resource-group | 用來建立應用程式資源的資源組名。 |
| NAME | 應用程式的名稱。 |
| namespace | 使用 **區塊鏈** 提供者命名空間。 |
| 資源類型 | 區塊鏈資料管理員應用程式的資源類型為成品 **。** |
| 父系 (parent) | 與應用程式相關聯之監看員的路徑。 例如，監看員 **/mywatcher**。 |
| is-full-object | 指出屬性包含應用程式資源的選項。 |
| properties | JSON 格式的字串，其中包含應用程式資源的屬性。 可以用字串或檔案的形式傳遞。 |

### <a name="blockchain-application-examples"></a>區塊鏈應用程式範例

設定 JSON 範例，可在 *美國東部* 區域建立應用程式資源，以監視合約 ABI 和位元組程式碼所定義的智慧型合約。

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
| location | 要在其中建立應用程式資源的區域。 |
| artifactType | 應用程式的類型。 目前支援 **EthereumSmartContract** 。 |
| abiFileUrl | 智慧型合約 ABI JSON 檔案的 URL。 如需取得合約 ABI 和建立 URL 的詳細資訊，請參閱取得合約 abi [和位元組](data-manager-portal.md#get-contract-abi-and-bytecode) 程式碼，以及 [建立合約 abi 和位元組](data-manager-portal.md#create-contract-abi-and-bytecode-url)程式碼 URL。 |
| bytecodeFileUrl | 智慧型合約部署的位元組程式碼 JSON 檔案的 URL。 如需取得智慧型合約部署的位元組程式碼和建立 URL 的詳細資訊，請參閱 [取得合約 abi 和位元組](data-manager-portal.md#get-contract-abi-and-bytecode) 程式碼，並 [建立合約 abi 和位元組](data-manager-portal.md#create-contract-abi-and-bytecode-url)程式碼 url。 注意：區塊鏈資料管理員需要 **部署的位元組的位元組**。 |
| queryTargetTypes | 已發佈的訊息類型。 指定 **>contractproperties** 會發行 *ContractPropertiesMsg* 訊息類型。 指定 **ContractEvents** 會發行 *DecodedContractEventsMsg* 訊息類型。 注意： *RawBlockAndTransactionMsg* 和 *RawTransactionContractCreationMsg* 訊息類型一律會發佈。 |

為*mywatcher*建立名為*myApplication*的應用程式，以監視 JSON 字串所定義的智慧型合約。

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

建立名為 *myApplication* 的應用程式， *mywatcher* 會監看使用 JSON 設定檔定義的智慧型合約。

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

執行時，區塊鏈管理員實例會監視來自訂輸入的區塊鏈事件，並將資料傳送至定義的輸出。

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 參數 | 說明 |
|-----------|-------------|
| 動作 | 使用 [ **開始** ] 執行監看員。 |
| ids | 監看員資源識別碼。 以監看員 \<Subscription ID\> \<Resource group\> \<Watcher name\> 資源的值取代、和。|

### <a name="start-instance-example"></a>啟動實例範例

啟動名為 *mywatcher*的區塊鏈資料管理員實例。

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>停止實例

停止區塊鏈資料管理員實例。

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| 參數 | 說明 |
|-----------|-------------|
| 動作 | 使用 [ **停止** ] 停止監看員。 |
| ids | 監看員的名稱。 以監看員 \<Subscription ID\> \<Resource group\> \<Watcher name\> 資源的值取代、和。 |

### <a name="stop-watcher-example"></a>停止監看員範例

停止名為 *mywatcher*的實例。

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>刪除實例

刪除區塊鏈資料管理員實例。

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| 參數 | 描述 |
|-----------|-------------|
| resource-group | 要刪除之監看員的資源組名。 |
| NAME | 要刪除的監看員名稱。 |
| 資源類型 | 區塊鏈資料管理員監看員的資源類型為 **區塊鏈/** 監看員。 |

### <a name="delete-instance-example"></a>刪除實例範例

刪除*myRG*資源群組中名為*mywatcher*的實例。

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>接下來的步驟

請嘗試下一個教學課程，使用區塊鏈資料管理員和 Azure Cosmos DB 來建立區塊鏈 transaction message explorer。

> [!div class="nextstepaction"]
> [使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)
