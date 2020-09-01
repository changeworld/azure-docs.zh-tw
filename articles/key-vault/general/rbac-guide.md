---
title: 使用 Azure RBAC 將許可權授與應用程式以存取 Azure 金鑰保存庫 |Microsoft Docs
description: 瞭解如何使用 Azure 角色型存取控制來提供金鑰、秘密和憑證的存取權。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f1e95c1244d327478862c3919481394d974ea42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270080"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>使用 Azure 角色型存取控制 (預覽，提供 Key Vault 金鑰、憑證和秘密的存取權) 

Azure 角色型存取控制 (Azure RBAC) 是以 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 為基礎的授權系統，可提供更細緻的 Azure 資源存取管理。

Azure RBAC 可讓使用者管理金鑰、秘密和憑證的許可權。 它提供一個位置來管理所有金鑰保存庫的擁有權限。 

Azure RBAC 模型可讓您在不同的範圍層級上設定許可權：管理群組、訂用帳戶、資源群組或個別資源。  適用于 key vault 的 Azure RBAC 也可讓您在個別金鑰、秘密和憑證上擁有個別的許可權

如需詳細資訊，請參閱 [azure 角色型存取控制 (AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/overview)。

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>個別金鑰、秘密和憑證的最佳作法

我們的建議是在每個環境中使用每個應用程式的保存庫 (開發、進入生產階段前和生產) 。

個別的金鑰、秘密和憑證許可權只能用於特定案例：

-   需要在各層之間分開存取控制的多層式應用程式

-   當應用程式需要存取該金鑰保存庫中的秘密子集時，共用金鑰保存庫與一般秘密

如需 Azure Key Vault 管理指導方針的詳細資訊，請參閱：

- [Azure Key Vault 最佳作法](best-practices.md)
- [Azure Key Vault 服務限制](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>適用于 Key Vault 資料平面作業的 Azure 內建角色 (preview) 

| 內建角色 | 描述 | ID |
| --- | --- | --- |
| Key Vault 系統管理員 (預覽)  | 在金鑰保存庫和其中的所有物件上執行所有資料平面作業，包括憑證、金鑰和秘密。 無法管理金鑰保存庫資源或管理角色指派。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault 憑證官員 (preview)  | 對金鑰保存庫的憑證執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault 加密長 (預覽) | 在金鑰保存庫的金鑰上執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault 加密服務加密 (預覽)  | 讀取金鑰的中繼資料，並執行包裝/解除包裝作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault 加密使用者 (預覽)  | 使用金鑰執行密碼編譯作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault 讀者 (預覽) | 讀取金鑰保存庫的中繼資料及其憑證、金鑰和秘密。 無法讀取敏感性值，例如秘密內容或金鑰內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault 秘密長 (預覽) | 對金鑰保存庫的秘密執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault 秘密使用者 (預覽) | 讀取秘密內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 4633458b-17de-408a-b874-0445c86b69e6 |

如需有關 Azure 內建角色定義的詳細資訊，請參閱 [azure 內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>搭配 Key Vault 使用 Azure RBAC 秘密、金鑰和憑證許可權

Key vault 的新 Azure RBAC 許可權模型提供了保存庫存取原則許可權模型的替代方案。 

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>在 Key Vault 上啟用 Azure RBAC 許可權

> [!IMPORTANT]
> 設定 Azure RBAC 許可權模型會使所有存取原則許可權失效。 如果未指派對等的 Azure 角色，可能會導致中斷。

1.  在新的金鑰保存庫上啟用 Azure RBAC 許可權：

    ![啟用 RBAC 許可權-新增保存庫](../media/rbac/image-1.png)

2.  在現有的金鑰保存庫上啟用 Azure RBAC 許可權：

    ![啟用 RBAC 許可權-現有的保存庫](../media/rbac/image-2.png)

### <a name="assign-role"></a>指派角色

> [!Note]
> 建議您在腳本中使用唯一角色識別碼，而不是角色名稱。 因此，如果角色已重新命名，您的腳本將會繼續運作。 在預覽期間，每個角色都會有 " (preview) " 尾碼，稍後將會移除。 本檔中的角色名稱只用于可讀性。

Azure CLI 命令以建立角色指派：

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

在 Azure 入口網站中，[存取控制] (IAM) ] 索引標籤上的所有資源都可使用 [Azure 角色指派] 畫面。

![角色指派- (IAM) 索引標籤](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>資源群組領域角色指派

1.  移至 key vault 資源群組。
    ![角色指派-資源群組](../media/rbac/image-4.png)

2.  按一下 [存取控制] (IAM) \> 新增角色指派 \> 新增

3.  為目前的使用者建立 Key Vault 讀者角色 "Key Vault Reader (preview) "

    ![新增角色-資源群組](../media/rbac/image-5.png)

Azure CLI：
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

上述角色指派提供在金鑰保存庫中列出金鑰保存庫物件的功能。

### <a name="key-vault-scope-role-assignment"></a>Key Vault 範圍角色指派

1. 移至 Key Vault \> 存取控制 (IAM) ] 索引標籤

2. 按一下 [新增角色指派 \> 新增]

3. 為目前的使用者建立金鑰秘密長角色 "Key Vault 秘密長 (預覽) "。

    ![角色指派-金鑰保存庫](../media/rbac/image-6.png)

 Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

建立上述角色指派之後，您可以建立/更新/刪除秘密。

4. 建立新秘密 ( 秘密 \> + 產生/匯入) 以測試秘密層級角色指派。

    ![新增角色-金鑰保存庫](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>秘密範圍角色指派

1. 開啟其中一個先前建立的秘密、通知總覽和存取控制 (IAM)  (preview) 

2. 按一下 [存取控制] (IAM) # B2 preview) 索引標籤

    ![角色指派-秘密](../media/rbac/image-8.png)

3. 為目前的使用者建立金鑰秘密長角色 "Key Vault 秘密長 (預覽) 」，如同上述 Key Vault 所做的一樣。

Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>測試和驗證

> [!NOTE]
> 移除角色指派之後，瀏覽器會使用快取，且需要重新整理頁面。<br>
> 允許幾分鐘的時間來重新整理角色指派

1. 驗證在金鑰保存庫層級上新增密碼，但不含 "Key Vault 秘密長" 角色。

移至 key vault 存取控制 (IAM) 索引標籤，並移除此資源的 [Key Vault 秘密長 (預覽) ] 角色指派。

![移除指派-金鑰保存庫](../media/rbac/image-9.png)

流覽至先前建立的秘密。 您可以看到所有秘密屬性。

![具有存取權的秘密視圖](../media/rbac/image-10.png)

建立新的秘密 ( 秘密 \> + 產生/匯入) 應該會顯示下列錯誤：

   ![建立新的秘密](../media/rbac/image-11.png)

2.  驗證秘密層級上沒有「Key Vault 秘密長」角色的密碼編輯。

-   移至先前建立的秘密存取控制 (IAM)  (preview) 索引標籤，並移除此資源的 [Key Vault 秘密長 (預覽) ] 角色指派。

-   流覽至先前建立的秘密。 您可以看到秘密屬性。

   ![沒有存取權的秘密視圖](../media/rbac/image-12.png)

3. 在金鑰保存庫層級驗證未讀取讀取者角色的秘密。

-   移至 key vault 資源群組存取控制 (IAM) 索引標籤，並移除「Key Vault 讀取器 (預覽) 」角色指派。

-   流覽至金鑰保存庫的 [密碼] 索引標籤應該會顯示下列錯誤：

   ![秘密索引標籤-錯誤](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>建立自訂角色 

[az role definition create 命令](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

** (CLI bash 腳本) </br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

如需有關如何建立自訂角色的詳細資訊，請參閱：

[Azure 自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) (機器翻譯)

如需可用的動作，請參閱附錄： **可用的動作**

## <a name="known-limits-and-performance"></a>已知的限制和效能

-   2000每個訂用帳戶的 Azure 角色指派

-   角色指派延遲：目前預期的效能，在角色指派變更為要套用的角色之後，最多需要10分鐘的時間)  (600 秒

## <a name="learn-more"></a>深入了解

- [Azure RBAC 總覽](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [自訂角色教學課程](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
