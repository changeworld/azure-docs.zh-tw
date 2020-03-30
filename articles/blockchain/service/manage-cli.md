---
title: 使用 Azure CLI 管理 Azure 區塊鏈服務
description: 如何使用 Azure CLI 管理 Azure 區塊鏈服務
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455591"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>使用 Azure CLI 管理 Azure 區塊鏈服務

除了 Azure 門戶之外，您還可以使用 Azure CLI 管理 Azure 區塊鏈服務的區塊鏈成員和事務節點。

請確保已安裝最新的[Azure CLI，](https://docs.microsoft.com/cli/azure/install-azure-cli)並登錄到 中的`az login`Azure 帳戶。

在以下示例中，將示例`<parameter names>`替換為您自己的值。

## <a name="create-blockchain-member"></a>創建區塊鏈成員

示例在 Azure 區塊鏈服務中創建一個區塊鏈成員，該成員在新聯合體中運行 Quorum 分類帳協定。

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **名稱** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com` 。 |
| **位置** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **密碼** | 成員帳戶密碼。 成員帳戶密碼會用來對使用基本驗證的區塊鏈成員公用端點進行驗證。 密碼必須滿足以下四個要求中的三個：長度必須在 12 & 72 個字元、1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元之間，不是數位記號（*）、百分比（）、逗號（、星\`形）、回引號（）、雙引號（）、單引號（）、破折號（-）和半列（;)|
| **協定** | 公共預覽支援仲裁。 |
| **財團** | 要加入或建立的聯盟名稱。 |
| **consortiumManagementAccountPassword** | 聯盟管理密碼。 密碼用於加入聯合體。 |
| **規則名稱** | 將 IP 位址範圍列入白名單的規則名稱。 防火牆規則的可選參數。|
| **啟動 Ip 位址** | 開始用於白名單的 IP 位址範圍。 防火牆規則的可選參數。 |
| **端Ip位址** | 用於白名單的 IP 位址範圍的末尾。 防火牆規則的可選參數。 |
| **skuName** | 服務層級類型。 使用 S0 表示「標準」，B0 表示「基本」。 |

## <a name="change-blockchain-member-password"></a>更改區塊鏈成員密碼

更改區塊鏈成員密碼的示例。

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **名稱** | 標識 Azure 區塊鏈服務成員的名稱。 |
| **密碼** | 成員帳戶密碼。 密碼必須滿足以下四個要求中的三個：長度必須在 12 & 72 個字元、1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元之間，這些字元不是數位記號（*）、百分比（）、逗號（、星形）、回引號（）、\`雙引號（）、單引號（）、單引號（'）、破折號（-）和分號（;)。 |

## <a name="create-transaction-node"></a>建立交易節點

在現有區塊鏈成員內部創建交易節點。 通過添加事務節點，可以增加安全隔離和分配負載。 例如，您可以為不同的用戶端應用程式提供事務節點終結點。

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱，該成員還包括新的交易節點名稱。 |
| **位置** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **密碼** | 事務節點密碼。 密碼必須滿足以下四個要求中的三個：長度必須在 12 & 72 個字元、1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元之間，這些字元不是數位記號（*）、百分比（）、逗號（、星形）、回引號（）、\`雙引號（）、單引號（）、單引號（'）、破折號（-）和分號（;)。 |
| **規則名稱** | 將 IP 位址範圍列入白名單的規則名稱。 防火牆規則的可選參數。 |
| **啟動 Ip 位址** | 開始用於白名單的 IP 位址範圍。 防火牆規則的可選參數。 |
| **端Ip位址** | 用於白名單的 IP 位址範圍的末尾。 防火牆規則的可選參數。|

## <a name="change-transaction-node-password"></a>更改事務節點密碼

示例更改事務節點密碼。

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱，該成員還包括新的交易節點名稱。 |
| **密碼** | 事務節點密碼。 密碼必須滿足以下四個要求中的三個：長度必須在 12 & 72 個字元、1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元之間，這些字元不是數位記號（*）、百分比（）、逗號（、星形）、回引號（）、\`雙引號（）、單引號（）、單引號（'）、破折號（-）和分號（;)。 |

## <a name="change-consortium-management-account-password"></a>更改聯合體管理帳戶密碼

聯營管理帳戶用於聯營成員管理。 每個成員都由聯合體管理帳戶唯一標識，您可以使用以下命令更改此帳戶的密碼。

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **名稱** | 標識 Azure 區塊鏈服務成員的名稱。 |
| **consortiumManagementAccountPassword** | 聯營管理帳戶密碼。 密碼必須滿足以下四個要求中的三個：長度必須在 12 & 72 個字元、1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元之間，這些字元不是數位記號（*）、百分比（）、逗號（、星形）、回引號（）、\`雙引號（）、單引號（）、單引號（'）、破折號（-）和分號（;)。 |
  
## <a name="update-firewall-rules"></a>更新防火牆規則

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱。 |
| **規則名稱** | 將 IP 位址範圍列入白名單的規則名稱。 防火牆規則的可選參數。|
| **啟動 Ip 位址** | 開始用於白名單的 IP 位址範圍。 防火牆規則的可選參數。|
| **端Ip位址** | 用於白名單的 IP 位址範圍的末尾。 防火牆規則的可選參數。|

## <a name="list-api-keys"></a>列出 API 金鑰

API 金鑰可用於類似于使用者名和密碼的節點訪問。 有兩個 API 金鑰支援金鑰輪換。 使用以下命令列出 API 金鑰。

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱，該成員還包括新的交易節點名稱。 |

## <a name="regenerate-api-keys"></a>重新生成 API 金鑰

使用以下命令重新生成 API 金鑰。

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱，該成員還包括新的交易節點名稱。 |
| **鍵名稱** | 將\<鍵值\>替換為鍵 1 或鍵 2。 |

## <a name="delete-a-transaction-node"></a>刪除事務節點

示例刪除區塊鏈成員交易節點。

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | Azure 區塊鏈服務區塊鏈成員的名稱，該成員還包括要刪除的交易節點名稱。 |

## <a name="delete-a-blockchain-member"></a>刪除區塊鏈成員

示例刪除區塊鏈成員。

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **資源組** | Azure 區塊鏈服務資源所在的資源組名稱。 |
| **名稱** | 要刪除的 Azure 區塊鏈服務區塊鏈成員的名稱。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>授予 Azure AD 使用者的許可權

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 參數 | 描述 |
|---------|-------------|
| **作用** | Azure AD 角色的名稱。 |
| **受託人** | Azure AD 使用者 ID。 例如， `user@contoso.com` |
| **範圍** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

**例子：**

將 Azure AD 使用者節點存取權限授予區塊鏈**成員**：

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**例子：**

授予 Azure AD 使用者對區塊鏈**事務節點**的節點存取權限：

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>授予 Azure AD 組或應用程式角色的節點存取權限

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 參數 | 描述 |
|---------|-------------|
| **作用** | Azure AD 角色的名稱。 |
| **受讓人-物件-id** | Azure AD 組 ID 或應用程式 ID。 |
| **範圍** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

**例子：**

授予**應用程式角色的**節點存取權限

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>刪除 Azure AD 節點訪問

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 參數 | 描述 |
|---------|-------------|
| **作用** | Azure AD 角色的名稱。 |
| **受託人** | Azure AD 使用者 ID。 例如， `user@contoso.com` |
| **範圍** | 角色指派的範圍。 可以是區塊鏈成員或交易節點。 |

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure 門戶配置 Azure 區塊鏈服務事務節點](configure-transaction-nodes.md)。
