---
title: 使用 Azure CLI 管理 Azure 區塊鏈服務
description: 如何使用 Azure CLI 管理 Azure 區塊鏈服務
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87170850"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>使用 Azure CLI 管理 Azure 區塊鏈服務

除了 Azure 入口網站之外，您還可以使用 Azure CLI 來管理 Azure 區塊鏈服務的區塊鏈成員和交易節點。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您想要在本機安裝和使用 CLI，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prepare-your-environment"></a>準備您的環境

1. 登入。

    如果您使用的是 CLI 的本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令登入。

    ```azurecli
    az login
    ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

1. 安裝 Azure CLI 擴充功能。

    當搭配 Azure CLI 的延伸模組參考一起使用時，您必須先安裝延伸模組。  Azure CLI 延伸模組可讓您存取核心 CLI 尚未隨附的實驗性與發行前版本命令。  若要深入了解延伸模組 (包括更新及解除安裝)，請參閱[使用 Azure CLI 延伸模組](/cli/azure/azure-cli-extensions-overview) (英文)。

    執行下列命令，以安裝[適用於 Azure 區塊鏈服務的擴充功能](/cli/azure/ext/blockchain/blockchain)：

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>建立區塊鏈成員

範例會在 Azure 區塊鏈服務中 [建立區塊鏈成員](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) ，以在新的聯盟中執行仲裁總帳通訊協定。

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com` 。 |
| **location** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 某些區域可能無法使用這些功能。 |
| **password** | 成員預設交易節點的密碼。 連線至區塊鏈成員的預設交易節點公用端點時，請使用此密碼進行基本驗證。 密碼必須符合下列四項需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號） ( # ) 、百分比 (% ) 、逗號 (、) 、星星 ( * ) 、雙引號 () 、雙引號 ( ) 、單引號 ( ) 、 \` 雙引號 ( ) 和 semicolumn (; ) |
| **protocol** | 區塊鏈通訊協定。 目前支援 Quorum 通訊協定。 |
| **consortium** | 要加入或建立的聯盟名稱。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。 |
| **consortium-management-account-password** | 聯盟帳戶密碼也稱為成員帳戶密碼。 成員帳戶密碼可用來對為您的成員建立的 Ethereum 帳戶進行私密金鑰加密。 您可以使用帳戶成員和成員帳戶密碼進行聯盟管理。 |
| **sku** | 服務層級類型。 「標準」或「基本」。 使用「基本」層來進行開發、測試和概念證明。 使用「標準」層來進行生產等級的部署。 如果您使用區塊鏈資料管理員或傳送大量的私人交易，也應該使用*標準*層。 不支援在建立成員之後變更基本和標準間的定價層。 |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>變更區塊鏈成員密碼或防火牆規則

範例會 [更新區塊鏈成員](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)的密碼、聯盟管理密碼和防火牆規則。

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | 識別您 Azure 區塊鏈服務成員的名稱。 |
| **password** | 成員預設交易節點的密碼。 連線至區塊鏈成員的預設交易節點公用端點時，請使用此密碼進行基本驗證。 密碼必須符合下列四項需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號） ( # ) 、百分比 (% ) 、逗號 (、) 、星星 ( * ) 、雙引號 () 、雙引號 ( ) 、單引號 ( ) 、 \` 雙引號 ( ) 和 semicolumn (; ) |
| **consortium-management-account-password** | 聯盟帳戶密碼也稱為成員帳戶密碼。 成員帳戶密碼可用來對為您的成員建立的 Ethereum 帳戶進行私密金鑰加密。 您可以使用帳戶成員和成員帳戶密碼進行聯盟管理。 |
| **防火牆-規則** | IP 允許清單的開始和結束 IP 位址。 |

## <a name="create-transaction-node"></a>建立交易節點

在現有的區塊鏈成員內[建立交易節點](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create)。 藉由新增交易節點，您可以提高安全性隔離和散發負載。 例如，您可能會有不同用戶端應用程式的交易節點端點。

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **location** | 區塊鏈成員的 Azure 區域。 |
| **成員名稱** | 識別您 Azure 區塊鏈服務成員的名稱。 |
| **password** | 交易節點的密碼。 連接到交易節點公用端點時，請使用密碼進行基本驗證。 密碼必須符合下列四項需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號） ( # ) 、百分比 (% ) 、逗號 (、) 、星星 ( * ) 、雙引號 () 、雙引號 ( ) 、單引號 ( ) 、 \` 雙引號 ( ) 和 semicolumn (; ) |
| **name** | 交易節點名稱。 |

## <a name="change-transaction-node-password"></a>變更交易節點密碼

範例會 [更新交易節點](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) 密碼。

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中 Azure 區塊鏈服務資源存在。 |
| **成員名稱** | 識別您 Azure 區塊鏈服務成員的名稱。 |
| **password** | 交易節點的密碼。 連接到交易節點公用端點時，請使用密碼進行基本驗證。 密碼必須符合下列四項需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號） ( # ) 、百分比 (% ) 、逗號 (、) 、星星 ( * ) 、雙引號 () 、雙引號 ( ) 、單引號 ( ) 、 \` 雙引號 ( ) 和 semicolumn (; ) |
| **name** | 交易節點名稱。 |

## <a name="list-api-keys"></a>列出 API 金鑰

API 金鑰可用於節點存取，類似于使用者名稱和密碼。 有兩個 API 金鑰可支援金鑰輪替。 使用下列命令來 [列出您的 API 金鑰](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)。

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈服務區塊鏈成員的名稱 |

## <a name="regenerate-api-keys"></a>重新產生 API 金鑰

使用下列命令重新產生 [您的 API 金鑰](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)。

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈服務區塊鏈成員的名稱。 |
| **keyName** | 請 \<keyValue\> 以 key1、key2 或兩者取代。 |

## <a name="delete-a-transaction-node"></a>刪除交易節點

範例會 [刪除區塊鏈成員交易節點](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete)。

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中 Azure 區塊鏈服務資源存在。 |
| **成員名稱** | 也包含要刪除之交易節點名稱的 Azure 區塊鏈服務區塊鏈成員名稱。 |
| **name** | 要刪除的交易節點名稱。 |

## <a name="delete-a-blockchain-member"></a>刪除區塊鏈成員

範例會 [刪除區塊鏈成員](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete)。

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中 Azure 區塊鏈服務資源存在。 |
| **name** | 要刪除的 Azure 區塊鏈服務區塊鏈成員名稱。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>為 Azure AD 使用者授與存取權

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 參數 | 說明 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **受託人** | Azure AD 使用者識別碼。 例如， `user@contoso.com` |
| **範圍 (scope)** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

**範例︰**

將 Azure AD 使用者的節點存取權授與區塊鏈 **成員**：

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**範例︰**

將 Azure AD 使用者的節點存取權授與區塊鏈 **交易節點**：

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>授與 Azure AD 群組或應用程式角色的節點存取權

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 參數 | 說明 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **受託人-物件識別碼** | Azure AD 群組識別碼或應用程式識別碼。 |
| **範圍 (scope)** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

**範例︰**

授與**應用程式角色**的節點存取權

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>移除 Azure AD 節點存取權

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 參數 | 說明 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **受託人** | Azure AD 使用者識別碼。 例如， `user@contoso.com` |
| **範圍 (scope)** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

## <a name="next-steps"></a>接下來的步驟

瞭解如何 [使用 Azure 入口網站設定 Azure 區塊鏈服務的交易節點](configure-transaction-nodes.md)。
