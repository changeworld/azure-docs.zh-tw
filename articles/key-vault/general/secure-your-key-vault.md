---
title: 針對金鑰保存庫的存取進行保護 - Azure Key Vault | Microsoft Docs
description: 針對 Azure Key Vault、金鑰和祕密進行存取權限管理。 包含 Key Vault 的驗證和授權模型以及如何保護金鑰保存庫。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 6a838455bfda47dee55e8726e5eab071d2dfe4f7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121145"
---
# <a name="secure-access-to-a-key-vault"></a>針對金鑰保存庫的存取進行保護

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 本文提供 Key Vault 存取模型的概觀。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>存取模型概觀

金鑰保存庫的存取權可透過兩個介面來控制︰管理平面**** 和資料平面****。 管理平面可讓您管理 Key Vault 本身。 此平面中的作業包括建立和刪除金鑰保存庫、擷取 Key Vault 屬性，以及更新存取原則。 資料平面則可讓您處理金鑰保存庫中儲存的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

若要在任一平面存取金鑰保存庫，所有呼叫者 (使用者或應用程式) 都必須有適當的驗證和授權。 驗證會建立呼叫者的身分識別。 授權則會判斷呼叫者可以執行哪些作業。

兩個平面都會使用 Azure Active Directory (Azure AD) 來進行驗證。 至於授權，管理平面會使用角色型存取控制 (RBAC)，資料平面則會使用 Key Vault 存取原則。

## <a name="active-directory-authentication"></a>Active Directory 驗證

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 這兩個平面中的所有呼叫者都必須在此租用戶中註冊，並經過驗證才能存取金鑰保存庫。 在這兩種情況中，應用程式均可透過兩種方式存取 Key Vault︰

- **使用者加上應用程式存取**：應用程式會代表已登入的使用者存取 Key Vault。 舉例來說，這類存取包括 Azure PowerShell 和 Azure 入口網站。 使用者存取的授與則是透過兩種方式來完成。 使用者可以從任何應用程式存取 Key Vault，或者必須使用特定應用程式來進行 (稱為「複合身分識別」__)。
- **僅限應用程式存取**：應用程式會以 daemon 服務或背景作業的形式執行。 系統會將金鑰保存庫的存取權授與給該應用程式識別碼。

對於這兩種類型的存取權，應用程式都會向 Azure AD 進行驗證。 應用程式會根據應用程式類型使用任何[支援的驗證方法](../../active-directory/develop/authentication-scenarios.md)。 應用程式會取得平面中資源的權杖以便授與存取權。 視 Azure 環境而定，資源會是管理或資料平面中的端點。 應用程式會使用此權杖，對 Key Vault 傳送 REST API 要求。 若要深入了解，請參閱[整個驗證流程](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

這兩個平面的單一驗證機制模型有幾個優點：

- 組織可以集中控制其組織內所有金鑰保存庫的存取權。
- 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
- 組織可以藉由使用 Azure AD 中的選項 (例如，啟用多重要素驗證以提升安全性) 來自訂驗證。

## <a name="resource-endpoints"></a>資源端點

應用程式會透過端點來存取平面。 這兩個平面的存取控制各自獨立運作。 若要對應用程式授與存取權以使用金鑰保存庫中的金鑰，您可以藉由使用 Key Vault 存取原則來對資料平面授與存取權。 若要對使用者授與讀取 Key Vault 屬性和標籤的存取權，但不授與讀取資料 (金鑰、密碼或憑證) 的存取權，您可以使用 RBAC 來對管理平面授與存取權。

下表顯示管理和資料平面的端點。

| 存取&nbsp;平面 | 存取端點 | 作業 | 存取&nbsp;控制機制 |
| --- | --- | --- | --- |
| 管理平面 | **全域：**<br> management.azure.com:443<br><br> **Azure 中國世紀：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 | 建立、讀取、更新及刪除金鑰保存庫<br><br>設定 Key Vault 存取原則<br><br>設定 Key Vault 標籤 | Azure Resource Manager RBAC |
| 資料平面 | **全域：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 中國世紀：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Gov︰**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 金鑰︰解密、加密、<br> 解除包裝、包裝、驗證、登入、<br> 取得、列出、更新、建立、<br> 匯入、刪除、備份、還原<br><br> 祕密︰取得、列出、設定、刪除 | Key Vault 存取原則 |

## <a name="management-plane-and-rbac"></a>管理平面和 RBAC

在管理平面中，您可以使用 RBAC （以角色為基礎的存取控制）來授權呼叫者可以執行的作業。 在 RBAC 模型中，每個 Azure 訂用帳戶都會有一個 Azure AD 執行個體。 您可以對來自該目錄的使用者、群組和應用程式授與存取權。 授與存取權即可在 Azure 訂用帳戶中管理使用 Azure Resource Manager 部署模型的資源。 若要授與存取權，請使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

您可以使用 Azure AD 在資源群組中建立金鑰保存庫和管理存取權。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以藉由指派適當的 RBAC 角色，來授與特定範圍層級的存取權。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 您可以對 RBAC 角色指派下列範圍層級：

- **訂**用帳戶：在訂用帳戶層級指派的 RBAC 角色會套用至該訂用帳戶內的所有資源群組和資源。
- **資源群組**：在資源群組層級指派的 RBAC 角色會套用至該資源群組中的所有資源。
- **特定資源**：指派給特定資源的 RBAC 角色會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱[RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>資料平面和存取原則

您可以藉由設定金鑰保存庫的 Key Vault 存取原則來授與資料平面的存取權。 若要設定這些存取原則，使用者、群組或應用程式必須具有該金鑰保存庫管理平面的 `Contributor` 權限。

您可以對使用者、群組或應用程式授與權限，來為金鑰保存庫中的金鑰或密碼執行特定作業。 Key Vault 最多可支援 1,024 個存取原則項目。 若要對數名使用者授與資料平面存取權，請建立 Azure AD 安全性群組並在該群組中新增使用者。

<a id="key-vault-access-policies"></a>Key Vault 存取原則可分別授與金鑰、祕密和憑證的權限。 您只能對使用者授與存取金鑰 (而非祕密) 的權限。 金鑰、祕密和憑證的存取權限位於保存庫層級。 Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 若要設定金鑰保存庫的存取原則，請使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

> [!IMPORTANT]
> Key Vault 存取原則會套用在保存庫層級。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。
>

您可以使用[Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)）來限制資料平面存取。 您可以設定[防火牆和虛擬網路規則](network-security.md)來額外加上一層安全性。

## <a name="example"></a>範例

在此範例中，我們開發的應用程式會使用 TLS/SSL 的憑證、Azure 儲存體來儲存資料，以及用於簽署作業的 RSA 2048 位金鑰。 我們的應用程式會在 Azure 虛擬機器 (VM) (或虛擬機器擴展集) 中執行。 我們可以使用金鑰保存庫來儲存應用程式祕密。 我們可以儲存供應用程式用來向 Azure AD 進行驗證的啟動程序憑證。

我們需要存取下列預存金鑰和祕密：
- **Tls/ssl 憑證**：用於 TLS/ssl。
- **儲存體金鑰**：用來存取儲存體帳戶。
- **RSA 2048 位金鑰**：用於簽署作業。
- **啟動程式憑證**：用來驗證 Azure AD。 獲得存取權後，我們就可以擷取儲存體金鑰，以及使用 RSA 金鑰進行簽署。

我們必須定義下列角色，以指定誰可以管理、部署及稽核我們的應用程式：
- **安全性小組**：來自于 CSO （首席安全性長）或類似參與者的 IT 人員。 安全性小組負責妥善保管祕密。 秘密可以包含 TLS/SSL 憑證、用於簽署的 RSA 金鑰、連接字串和儲存體帳戶金鑰。
- **開發人員和操作員**：開發應用程式並將它部署在 Azure 中的員工。 此小組的成員不屬於安全性工作人員。 他們不應該存取敏感性資料，例如 TLS/SSL 憑證和 RSA 金鑰。 其所部署的應用程式才應具備敏感性資料的存取權。
- **審計員**：此角色適用于不屬於開發或一般 IT 人員成員的參與者。 其會檢閱憑證、金鑰和祕密的使用與維護情形，以確保各項作業符合安全性標準。

另外還有一個不在我們應用程式範圍內的角色：訂用帳戶 (或資源群組) 系統管理員。 訂用帳戶管理員會設定安全性小組的初始存取權限。 其會使用資源群組 (具有此應用程式所需的資源)，將存取權授與安全性小組。

我們需要對我們的角色授權下列作業：

**安全性小組**
- 建立金鑰保存庫。
- 開啟 Key Vault 記錄。
- 新增金鑰和祕密。
- 建立金鑰備份以用於災害復原。
- 設定金鑰保存庫存取原則，對使用者和應用程式授與特定作業的權限。
- 定期輪替金鑰和祕密。

**開發人員和操作員**
- 從安全性小組取得啟動程式和 TLS/SSL 憑證（指紋）、儲存體金鑰（密碼 URI）和 RSA 金鑰（金鑰 URI）的參考以進行簽署。
- 開發和部署以程式設計方式存取金鑰和密碼的應用程式。

**稽核員**
- 檢閱 Key Vault 記錄，確認金鑰和密碼的使用是否適當，以及是否符合資料安全性標準。

下表摘要說明角色和應用程式的存取權限。

| 角色 | 管理平面權限 | 資料平面權限 |
| --- | --- | --- |
| 安全性小組 | Key Vault 參與者 | 金鑰︰備份、建立、刪除、取得、匯入、列出、還原<br>祕密：所有作業 |
| 開發人員和&nbsp;操作員 | Key Vault 部署權限<br><br> **注意**：此許可權可讓已部署的 vm 從金鑰保存庫提取秘密。 | 無 |
| 稽核員 | 無 | 金鑰︰列出<br>密碼︰列出<br><br> **注意**：此許可權可讓審計員檢查未在記錄中發出之金鑰和密碼的屬性（標記、啟用日期、到期日）。 |
| 應用程式 | 無 | 金鑰︰簽署<br>密碼︰取得 |

這三個小組角色需要其他資源的存取權以及 Key Vault 權限。 若要部署 VM (或 Azure App Service 的 Web Apps 功能)，開發人員和操作員需要這些資源類型的 `Contributor` 存取權。 稽核員需要儲存 Key Vault 記錄所在儲存體帳戶的讀取權限。

如需如何以程式設計方式部署憑證、存取金鑰和祕密的詳細資訊，請參閱下列資源：
- 了解如何[將憑證從客戶管理的金鑰保存庫部署到 VM](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (部落格文章)。
- 查看[Azure Key Vault 用戶端範例](https://docs.microsoft.com/samples/browse/?term=Key%20Vault)。 此內容會示範如何使用啟動程序憑證，向 Azure AD 驗證以存取金鑰保存庫。

您可以使用 Azure 入口網站授與大部分的存取權限。 若要授與細微權限，您可以使用 Azure PowerShell 或 Azure CLI。

本節中的 PowerShell 程式碼片段是使用下列假設來建置的︰
- Azure AD 系統管理員已建立安全性群組來代表三個角色： Contoso Security Team、Contoso App DevOps 和 Contoso App 審計員。 系統管理員已將使用者新增到其各自的群組。
- 所有資源都位於 **ContosoAppRG** 資源群組。
- Key Vault 記錄會儲存在 **contosologstorage** 儲存體帳戶。
- **ContosoKeyVault** 金鑰保存庫和 **contosologstorage** 儲存體帳戶位於相同的 Azure 位置。

訂用帳戶管理員會將 `key vault Contributor` 和 `User Access Administrator` 角色指派給安全性小組。 這些角色可讓安全性小組管理其他資源和金鑰保存庫 (兩者均位於 **ContosoAppRG** 資源群組) 的存取權。

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

安全性小組會建立金鑰保存庫，並設定記錄和存取權限。

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

我們定義的自訂角色只能指派給 **ContosoAppRG** 資源群組建立所在的訂用帳戶。 若要針對其他訂用帳戶中的其他專案使用自訂角色，請新增其他訂用帳戶至該角色的範圍中。

針對我們的 DevOps 人員，金鑰保存庫 `deploy/action` 權限的自訂角色指派僅限於該資源群組範圍內。 只有在**ContosoAppRG**資源群組中建立的 vm 才允許存取秘密（TLS/SSL 和啟動程式憑證）。 由 DevOps 成員在其他資源群組中建立的 VM 即使有祕密的 URI，也無法存取這些祕密。

我們的範例會說明一個簡單的案例。 真實案例可能會更複雜。 您可以根據需求調整金鑰保存庫的權限。 我們假設該安全性小組會提供 DevOps 人員在其應用程式中所使用的金鑰和密碼參考 (URI 和指紋)。 開發人員和操作員不需要任何資料平面存取權。 我們的重點放在如何保護您的金鑰保存庫。 在保護[您的 VM](https://azure.microsoft.com/services/virtual-machines/security/)、[儲存體帳戶](../../storage/blobs/security-recommendations.md)和其他 Azure 資源時，請做出類似的考量。

> [!NOTE]
> 此範例示範生產環境中會如何鎖定 Key Vault 的存取權。 開發人員應該有自己的訂用帳戶或資源群組，並擁有完整權限以便管理其用來開發應用程式的保存庫、VM 和儲存體帳戶。

建議您透過[設定 Key Vault 防火牆和虛擬網路](network-security.md)，來設定其他的金鑰保存庫安全存取權。

## <a name="resources"></a>資源

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC：內建角色](../../role-based-access-control/built-in-roles.md)

* [了解資源管理員部署和傳統部署](../../azure-resource-manager/management/deployment-models.md)

* [使用 Azure PowerShell 管理 RBAC](../../role-based-access-control/role-assignments-powershell.md)

* [使用 REST API 管理 RBAC](../../role-based-access-control/role-assignments-rest.md)

* [RBAC for Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    這個 2015 Microsoft Ignite 大會影片討論 Azure 中的存取管理和報告功能。 此外，也會探討使用 Azure AD 保護 Azure 訂用帳戶存取權的最佳做法。

* [使用 OAuth 2.0 和 Azure AD 授權存取 Web 應用程式](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault 管理 REST Api](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    本文件是可供以程式設計方式管理金鑰保存庫 (包括設定 Key Vault 存取原則) 的 REST API 參考。

* [Key Vault REST Api](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [金鑰存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [密碼存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* 使用 PowerShell [設定](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)和[移除](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) Key Vault 存取原則。

## <a name="next-steps"></a>後續步驟

設定 [Key Vault 防火牆和虛擬網路](network-security.md)。

如需系統管理員的快速入門教學課程，請參閱[什麼是 Azure Key Vault？](overview.md)）。

如需 Key Vault 使用方式記錄的詳細資訊，請參閱[Azure Key Vault 記錄](logging.md)）。

如需搭配 Azure Key Vault 使用金鑰和密碼的詳細資訊，請參閱[關於金鑰和密碼](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如果您有關於 Key Vault 的問題，請造訪[論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
