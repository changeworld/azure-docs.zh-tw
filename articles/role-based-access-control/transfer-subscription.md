---
title: '將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄 (預覽) '
description: 瞭解如何將 Azure 訂用帳戶和已知的相關資源傳輸至不同的 Azure Active Directory (Azure AD) 目錄。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: rolyon
ms.openlocfilehash: ab004c11b46428c5fad28177b0d94edc04b95654
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400539"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory-preview"></a>將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄 (預覽) 

> [!IMPORTANT]
> 遵循這些步驟，將訂用帳戶轉移至不同的 Azure AD 目錄目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

組織可能會有數個 Azure 訂用帳戶。 每個訂用帳戶都會與特定的 Azure Active Directory (Azure AD) 目錄相關聯。 為了讓管理更容易，您可能會想要將訂用帳戶轉移至不同的 Azure AD 目錄。 當您將訂用帳戶轉移至不同的 Azure AD 目錄時，部分資源不會傳送至目標目錄。 例如，Azure 角色型存取控制中的所有角色指派和自訂角色 (Azure RBAC) 會從來原始目錄中 **永久** 刪除，而且不會傳送至目標目錄。

本文說明您可以遵循的基本步驟，將訂用帳戶轉移至不同的 Azure AD 目錄，並在傳輸之後重新建立一些資源。

> [!NOTE]
> 針對 (CSP) 訂用帳戶的 Azure 雲端服務提供者，不支援變更訂用帳戶的 Azure AD 目錄。

## <a name="overview"></a>概觀

將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄是必須謹慎規劃和執行的複雜程式。 許多 Azure 服務都需要安全性主體 (身分識別) ，才能正常運作或甚至管理其他 Azure 資源。 本文嘗試涵蓋大部分相依于安全性主體的 Azure 服務，但不是完整的。

> [!IMPORTANT]
> 在某些案例中，傳輸訂用帳戶可能需要停機才能完成此程式。 需要謹慎規劃，以評估您的傳輸是否需要停機時間。

下圖顯示將訂用帳戶轉移至不同目錄時必須遵循的基本步驟。

1. 準備轉移

1. 將 Azure 訂用帳戶轉移至不同的目錄

1. 重新建立目標目錄中的資源，例如角色指派、自訂角色和受控識別

    ![轉移訂用帳戶圖表](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>決定是否要將訂用帳戶轉移至不同的目錄

以下是您可能想要轉移訂用帳戶的一些原因：

- 由於公司的合併或收購，您想要在主要 Azure AD 目錄中管理取得的訂用帳戶。
- 您組織中的某個人建立了訂用帳戶，而您想要將管理合併到特定的 Azure AD 目錄。
- 您有相依于特定訂用帳戶識別碼或 URL 的應用程式，而且不容易修改應用程式設定或程式碼。
- 您部分的公司已分割成不同的公司，而您需要將某些資源移至不同的 Azure AD 目錄。
- 基於安全隔離的目的，您想要在不同的 Azure AD 目錄中管理部分資源。

傳送訂用帳戶需要停機才能完成此程式。 視您的案例而定，只需重新建立資源並將資料複製到目標目錄和訂用帳戶，可能會比較好。

### <a name="understand-the-impact-of-transferring-a-subscription"></a>瞭解傳輸訂用帳戶的影響

有數個 Azure 資源相依于訂用帳戶或目錄。 根據您的情況，下表列出傳送訂閱的已知影響。 藉由執行此文章中的步驟，您可以重新建立在訂用帳戶轉移之前存在的一些資源。

> [!IMPORTANT]
> 此區段會列出相依于您訂用帳戶的已知 Azure 服務或資源。 由於 Azure 中的資源類型不斷演進，此處可能會有未列出的其他相依性，而可能導致您的環境中斷變更。 

| 服務或資源 | 影響 | 可復原 | 您會受到影響嗎？ | 您可以採取的方法 |
| --------- | --------- | --------- | --------- | --------- |
| 角色指派 | 是 | 是 | [列出角色指派](#save-all-role-assignments) | 所有角色指派都會永久刪除。 您必須將使用者、群組和服務主體對應到目標目錄中的對應物件。 您必須重新建立角色指派。 |
| 自訂角色 | 是 | 是 | [列出自訂角色](#save-custom-roles) | 所有自訂角色都會永久刪除。 您必須重新建立自訂角色和任何角色指派。 |
| 系統指派的受控識別 | 是 | 是 | [列出受控識別](#list-role-assignments-for-managed-identities) | 您必須停用並重新啟用受控識別。 您必須重新建立角色指派。 |
| 使用者指派的受控識別 | 是 | 是 | [列出受控識別](#list-role-assignments-for-managed-identities) | 您必須刪除、重新建立，然後將受控識別附加至適當的資源。 您必須重新建立角色指派。 |
| Azure 金鑰保存庫 | 是 | 是 | [列出 Key Vault 存取原則](#list-key-vaults) | 您必須更新與金鑰保存庫相關聯的租使用者識別碼。 您必須移除並新增新的存取原則。 |
| 已啟用 Azure AD authentication 整合的 Azure SQL 資料庫 | 是 | 否 | [使用 Azure AD authentication 檢查 Azure SQL 資料庫](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Azure 儲存體和 Azure Data Lake Storage Gen2 | 是 | 是 |  | 您必須重新建立任何 Acl。 |
| Azure Data Lake Storage Gen1 | 是 | 是 |  | 您必須重新建立任何 Acl。 |
| Azure 檔案 | 是 | 是 |  | 您必須重新建立任何 Acl。 |
| Azure 檔案同步 | 是 | 是 |  |  |
| Azure 受控磁碟 | 是 | N/A |  |  |
| 適用于 Kubernetes 的 Azure Container Service | 是 | 是 |  |  |
| Azure Active Directory Domain Services | 是 | 否 |  |  |
| 應用程式註冊 | 是 | 是 |  |  |

> [!WARNING]
> 如果您針對資源（例如儲存體帳戶或 SQL database）使用待用資料加密，但其相依于金鑰保存庫，但該金鑰保存庫 **不** 在要傳輸的相同訂用帳戶中，則可能會導致無法復原的情況。 如果您有這種情況，您應該採取步驟來使用不同的金鑰保存庫，或暫時停用客戶管理的金鑰，以避免此無法復原的情況。

## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您將需要：

- Azure Cloud Shell 或[Azure CLI](https://docs.microsoft.com/cli/azure) [中的 Bash](/azure/cloud-shell/overview)
- 您要在來原始目錄中傳輸之訂用帳戶的帳戶管理員
- 目標目錄中的[擁有](built-in-roles.md#owner)者角色

## <a name="step-1-prepare-for-the-transfer"></a>步驟1：準備傳送

### <a name="sign-in-to-source-directory"></a>登入來原始目錄

1. 以系統管理員身分登入 Azure。

1. 使用 [az account list](/cli/azure/account#az-account-list) 命令取得訂用帳戶清單。

    ```azurecli
    az account list --output table
    ```

1. 使用 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 設定您要傳送的使用中訂用帳戶。

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>安裝 resource graph 延伸模組

 Resource graph 延伸模組可讓您使用 [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 命令來查詢受 Azure Resource Manager 管理的資源。 您將在稍後的步驟中使用此命令。

1. 使用 [az 延伸模組清單](https://docs.microsoft.com/cli/azure/extension#az-extension-list) ，查看是否已安裝 *resource graph* 延伸模組。

    ```azurecli
    az extension list
    ```

1. 如果沒有，請安裝 *resource graph* 延伸模組。

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>儲存所有角色指派

1. 使用 [az 角色指派清單](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-list) 來列出所有角色指派 (包括) 繼承的角色指派。

    為了讓您更輕鬆地查看清單，您可以將輸出匯出為 JSON、TSV 或資料表。 如需詳細資訊，請參閱 [使用 AZURE RBAC 和 Azure CLI 列出角色指派](role-assignments-list-cli.md)。

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. 儲存角色指派清單。

    當您傳輸訂用帳戶時，會 **永久** 刪除所有角色指派，因此請務必儲存複本。

1. 檢查角色指派清單。 您可能不需要在目標目錄中有角色指派。

### <a name="save-custom-roles"></a>儲存自訂角色

1. 使用 [az 角色定義清單](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-list) 來列出您的自訂角色。 如需詳細資訊，請參閱 [使用 Azure CLI 建立或更新 Azure 自訂角色](custom-roles-cli.md)。

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. 將目標目錄中所需的每個自訂角色儲存為個別的 JSON 檔案。

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. 製作自訂角色檔案的複本。

1. 修改每個複本以使用下列格式。

    您稍後將會使用這些檔案，在目標目錄中重新建立自訂角色。

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>判斷使用者、群組和服務主體對應

1. 根據您的角色指派清單，決定您將在目標目錄中對應的使用者、群組和服務主體。

    您可以藉由查看 `principalType` 每個角色指派中的屬性來識別主體的類型。

1. 如有必要，請在目標目錄中建立您將需要的任何使用者、群組或服務主體。

### <a name="list-role-assignments-for-managed-identities"></a>列出受控識別的角色指派

當訂用帳戶轉移至另一個目錄時，不會更新受控識別。 因此，任何現有的系統指派或使用者指派的受控識別將會中斷。 傳輸之後，您可以重新啟用任何系統指派的受控識別。 針對使用者指派的受控識別，您必須在目標目錄中重新建立並附加它們。

1. 請參閱 [支援受控識別的 Azure 服務清單，](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 以記下您可能使用受控識別的位置。

1. 使用 [az ad sp list](/cli/azure/identity?view=azure-cli-latest#az-identity-list) 列出系統指派和使用者指派的受控識別。

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. 在受控識別清單中，判斷哪些是系統指派的，哪些是使用者指派的。 您可以使用下列準則來判斷類型。

    | 準則 | 受控識別類型 |
    | --- | --- |
    | `alternativeNames` 屬性包含 `isExplicit=False` | 系統指派 |
    | `alternativeNames` 屬性不包含 `isExplicit` | 系統指派 |
    | `alternativeNames` 屬性包含 `isExplicit=True` | 使用者指派 |

    您也可以使用 [az identity list](https://docs.microsoft.com/cli/azure/identity#az-identity-list) 只列出使用者指派的受控識別。 如需詳細資訊，請參閱 [使用 Azure CLI 建立、列出或刪除使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。

    ```azurecli
    az identity list
    ```

1. 取得受控識別的 `objectId` 值清單。

1. 搜尋您的角色指派清單，以查看您的受控識別是否有任何角色指派。

### <a name="list-key-vaults"></a>列出金鑰保存庫

當您建立金鑰保存庫時，它會自動系結至其建立所在之訂用帳戶的預設 Azure Active Directory 租使用者識別碼。 所有存取原則項目也會繫結到此租用戶識別碼。 如需詳細資訊，請參閱 [將 Azure Key Vault 移至另一個訂](../key-vault/general/move-subscription.md)用帳戶。

> [!WARNING]
> 如果您針對資源（例如儲存體帳戶或 SQL database）使用待用資料加密，但其相依于金鑰保存庫，但該金鑰保存庫 **不** 在要傳輸的相同訂用帳戶中，則可能會導致無法復原的情況。 如果您有這種情況，您應該採取步驟來使用不同的金鑰保存庫，或暫時停用客戶管理的金鑰，以避免此無法復原的情況。

- 如果您有金鑰保存庫，請使用 [az keyvault show](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-show) 來列出存取原則。 如需詳細資訊，請參閱 [指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-cli.md)。

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>列出具有 Azure AD authentication 的 Azure SQL 資料庫

- 使用 [az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) 和 [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 擴充功能來查看您是否使用已啟用 Azure AD AUTHENTICATION 整合的 Azure sql database。 如需詳細資訊，請參閱 [設定和管理使用 SQL 的 Azure Active Directory 驗證](../azure-sql/database/authentication-aad-configure.md)。

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>列出 Acl

1. 如果您使用 Azure Data Lake Storage Gen1，請使用 Azure 入口網站或 PowerShell 列出套用到任何檔案的 Acl。

1. 如果您使用 Azure Data Lake Storage Gen2，請使用 Azure 入口網站或 PowerShell 列出套用到任何檔案的 Acl。

1. 如果您使用 Azure 檔案儲存體，請列出適用于任何檔案的 Acl。

### <a name="list-other-known-resources"></a>列出其他已知的資源

1. 使用 [az account show](https://docs.microsoft.com/cli/azure/account#az-account-show) 來取得您的訂用帳戶識別碼。

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. 使用 [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 擴充功能來列出具有已知 Azure AD 目錄相依性的其他 Azure 資源。

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>步驟2：轉移訂用帳戶

在此步驟中，您會將訂用帳戶從來原始目錄傳送至目標目錄。 這些步驟會根據您是否也要轉移帳單擁有權而有所不同。

> [!WARNING]
> 當您傳輸訂用帳戶時，來原始目錄中的所有角色指派都會 **永久** 刪除，而且無法還原。 一旦轉移訂用帳戶，您就無法返回。 執行此步驟之前，請務必先完成上述步驟。

1. 判斷您是否也要將帳單擁有權轉移至另一個帳戶。

1. 將訂用帳戶轉移至不同的目錄。

    - 如果您想要保留目前的帳單擁有權，請遵循 [將 Azure 訂用帳戶關聯或新增至您 Azure Active Directory 租使用者](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)的步驟。
    - 如果您想要同時轉移帳單擁有權，請遵循將 [Azure 訂用帳戶的帳單擁有權轉移至另一個帳戶](../cost-management-billing/manage/billing-subscription-transfer.md)的步驟。 若要將訂用帳戶轉移至不同的目錄，您必須選取 [訂用帳戶 **Azure AD 租** 使用者] 核取方塊。

1. 當您完成傳送訂用帳戶之後，請回到本文以重新建立目標目錄中的資源。

## <a name="step-3-re-create-resources"></a>步驟3：重新建立資源

### <a name="sign-in-to-target-directory"></a>登入目標目錄

1. 在目標目錄中，以接受轉移要求的使用者登入。

    只有新帳戶中接受轉移要求的使用者才能存取管理資源。

1. 使用 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list) 命令取得訂用帳戶清單。

    ```azurecli
    az account list --output table
    ```

1. 使用 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 設定您要使用的使用中訂用帳戶。

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>建立自訂角色
        
- 使用 [az role definition create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create) ，從您稍早建立的檔案建立每個自訂角色。 如需詳細資訊，請參閱 [使用 Azure CLI 建立或更新 Azure 自訂角色](custom-roles-cli.md)。

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>建立角色指派

- 使用 [az role 指派 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 來建立使用者、群組和服務主體的角色指派。 如需詳細資訊，請參閱 [使用 AZURE RBAC 和 Azure CLI 新增或移除角色指派](role-assignments-cli.md)。

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>更新系統指派的受控識別

1. 停用並重新啟用系統指派的受控識別。

    | Azure 服務 | 詳細資訊 | 
    | --- | --- |
    | 虛擬機器 | [使用 Azure CLI 在 Azure VM 上設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 虛擬機器擴展集 | [使用 Azure CLI 在虛擬機器擴展集上設定 Azure 資源受控識別](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | 其他服務 | [支援適用於 Azure 資源的受控識別服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. 使用 [az role 指派 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 來建立系統指派的受控識別的角色指派。 如需詳細資訊，請參閱 [使用 Azure CLI 將受控識別存取權指派給資源](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>更新使用者指派的受控識別

1. 刪除、重新建立及附加使用者指派的受控識別。

    | Azure 服務 | 詳細資訊 | 
    | --- | --- |
    | 虛擬機器 | [使用 Azure CLI 在 Azure VM 上設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 虛擬機器擴展集 | [使用 Azure CLI 在虛擬機器擴展集上設定 Azure 資源受控識別](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | 其他服務 | [支援適用於 Azure 資源的受控識別服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[使用 Azure CLI 建立、列出和刪除使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. 使用 [az role 指派 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 來建立使用者指派的受控識別的角色指派。 如需詳細資訊，請參閱 [使用 Azure CLI 將受控識別存取權指派給資源](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>更新金鑰保存庫

本節說明更新金鑰保存庫的基本步驟。 如需詳細資訊，請參閱 [將 Azure Key Vault 移至另一個訂](../key-vault/general/move-subscription.md)用帳戶。

1. 將訂用帳戶中與所有現有金鑰保存庫相關聯的租使用者識別碼，更新為目標目錄。

1. 移除所有現有的存取原則項目。

1. 新增與目標目錄相關聯的存取原則專案。

### <a name="update-acls"></a>更新 Acl

1. 如果您使用 Azure Data Lake Storage Gen1，請指派適當的 Acl。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](../data-lake-store/data-lake-store-secure-data.md)。

1. 如果您使用 Azure Data Lake Storage Gen2，請指派適當的 Acl。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../storage/blobs/data-lake-storage-access-control.md)。

1. 如果您使用 Azure 檔案儲存體，請指派適當的 Acl。

### <a name="review-other-security-methods"></a>檢查其他安全性方法

即使在轉移期間移除角色指派，原始擁有者帳戶中的使用者仍可透過其他安全性方法繼續存取訂用帳戶，包括：

- 服務 (例如儲存體) 的存取金鑰。
- 將訂用帳戶資源的存取權授與使用者系統管理員的[管理憑證](../cloud-services/cloud-services-certs-create.md)。
- 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果您的目的是要從來原始目錄中的使用者移除存取權，讓他們在目標目錄中沒有存取權，您應該考慮輪替任何認證。 在認證更新之前，使用者將會在傳輸之後繼續具有存取權。

1. 輪替儲存體帳戶存取金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。

1. 如果您使用其他服務的存取金鑰，例如 Azure SQL Database 或 Azure 服務匯流排訊息，請輪替存取金鑰。

1. 針對使用秘密的資源，請開啟資源的設定，並更新秘密。

1. 若為使用憑證的資源，請更新憑證。

## <a name="next-steps"></a>接下來的步驟

- [將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶](../cost-management-billing/manage/billing-subscription-transfer.md)
- [在訂閱者與 CSP 之間轉移 Azure 訂用帳戶](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
