---
title: 使用 Azure CLI 管理 Azure 區塊鏈 Service
description: 如何使用 Azure CLI 管理 Azure 區塊鏈 Service
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 4b41bc44c9efbf71621fcfba06e668f42caa0f8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076942"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>使用 Azure CLI 管理 Azure 區塊鏈 Service

除了 Azure 入口網站以外，您還可以使用 Azure CLI 來管理 Azure 區塊鏈服務的區塊鏈成員和交易節點。

請確定您已安裝最新的[Azure CLI](/cli/azure/install-azure-cli) ，並使用登入 Azure 帳戶 `az login` 。

在下列範例中，將 example 取代 `<parameter names>` 成您自己的值。

## <a name="create-blockchain-member"></a>建立區塊鏈成員

範例會在 Azure 區塊鏈 Service 中建立區塊鏈成員，以在新的聯盟中執行仲裁總帳通訊協定。

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
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com` 。 |
| **location** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **password** | 成員帳戶密碼。 成員帳戶密碼會用來對使用基本驗證的區塊鏈成員公用端點進行驗證。 密碼必須符合下列四個需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號（#）、百分比（%）、逗號（，）、星號（*）、後引號（ \` ）、雙引號（-）、單引號（'）、虛線（-）和 semicolumn （;)|
| **protocol** | 公開預覽支援仲裁。 |
| **consortium** | 要加入或建立的聯盟名稱。 |
| **consortiumManagementAccountPassword** | 聯盟管理密碼。 密碼會用於加入聯盟。 |
| **ruleName** | 用來允許清單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。|
| **startIpAddress** | 允許清單的 IP 位址範圍的開頭。 防火牆規則的選擇性參數。 |
| **Endipaddress 設** | 允許清單的 IP 位址範圍結尾。 防火牆規則的選擇性參數。 |
| **skuName** | 服務層級類型。 使用 S0 表示「標準」，B0 表示「基本」。 |

## <a name="change-blockchain-member-password"></a>變更區塊鏈成員密碼

範例會變更區塊鏈成員的密碼。

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
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | 識別您的 Azure 區塊鏈服務成員的名稱。 |
| **password** | 成員帳戶密碼。 密碼必須符合下列四個需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號（#））、百分比（%）、逗號（，）、星號（*）、後引號（ \` ）、虛線（-）和分號（;)。 |

## <a name="create-transaction-node"></a>建立交易節點

在現有的區塊鏈成員內建立交易節點。 藉由新增交易節點，您可以增加安全性隔離並分散負載。 例如，您可能會有不同用戶端應用程式的交易節點端點。

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
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱，其中也包含新的交易節點名稱。 |
| **location** | 區塊鏈成員建立所在的 Azure 區域。 例如： `eastus` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **password** | 交易節點密碼。 密碼必須符合下列四個需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號（#））、百分比（%）、逗號（，）、星號（*）、後引號（ \` ）、虛線（-）和分號（;)。 |
| **ruleName** | 用來允許清單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。 |
| **startIpAddress** | 允許清單的 IP 位址範圍的開頭。 防火牆規則的選擇性參數。 |
| **Endipaddress 設** | 允許清單的 IP 位址範圍結尾。 防火牆規則的選擇性參數。|

## <a name="change-transaction-node-password"></a>變更交易節點密碼

範例會變更交易節點密碼。

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱，其中也包含新的交易節點名稱。 |
| **password** | 交易節點密碼。 密碼必須符合下列四個需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號（#））、百分比（%）、逗號（，）、星號（*）、後引號（ \` ）、虛線（-）和分號（;)。 |

## <a name="change-consortium-management-account-password"></a>變更聯盟管理帳戶密碼

聯盟管理帳戶用於聯盟成員資格管理。 每個成員都是由聯盟管理帳戶唯一識別，而您可以使用下列命令來變更此帳戶的密碼。

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
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 |
| **name** | 識別您的 Azure 區塊鏈服務成員的名稱。 |
| **consortiumManagementAccountPassword** | 聯盟管理帳戶密碼。 密碼必須符合下列四個需求的其中三項：長度必須介於 12 & 72 個字元、1個小寫字元、1個大寫字元、1個數字和1個特殊字元（不是數位記號（#））、百分比（%）、逗號（，）、星號（*）、後引號（ \` ）、虛線（-）和分號（;)。 |
  
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
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱。 |
| **ruleName** | 用來允許清單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。|
| **startIpAddress** | 允許清單的 IP 位址範圍的開頭。 防火牆規則的選擇性參數。|
| **Endipaddress 設** | 允許清單的 IP 位址範圍結尾。 防火牆規則的選擇性參數。|

## <a name="list-api-keys"></a>列出 API 金鑰

API 金鑰可用於節點存取，類似于使用者名稱和密碼。 有兩個 API 金鑰可支援金鑰輪替。 使用下列命令來列出您的 API 金鑰。

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱，其中也包含新的交易節點名稱。 |

## <a name="regenerate-api-keys"></a>重新產生 API 金鑰

使用下列命令來重新產生您的 API 金鑰。

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
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱，其中也包含新的交易節點名稱。 |
| **名** | 取代 \<keyValue\> 為 key1 或 key2。 |

## <a name="delete-a-transaction-node"></a>刪除交易節點

範例會刪除區塊鏈成員交易節點。

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱，其中也包含要刪除的交易節點名稱。 |

## <a name="delete-a-blockchain-member"></a>刪除區塊鏈成員

範例會刪除區塊鏈成員。

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | 資源組名，其中有 Azure 區塊鏈服務資源存在。 |
| **name** | 要刪除之 Azure 區塊鏈服務區塊鏈成員的名稱。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>授與 Azure AD 使用者的存取權

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

將 Azure AD 使用者的節點存取權授與區塊鏈**成員**：

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**範例︰**

將 Azure AD 使用者的節點存取權授與區塊鏈**交易節點**：

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

### <a name="remove-azure-ad-node-access"></a>移除 Azure AD 節點存取

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

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 Azure 入口網站來設定 Azure 區塊鏈 Service 交易節點](configure-transaction-nodes.md)。
