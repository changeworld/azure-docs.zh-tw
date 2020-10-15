---
title: 保護對受控 HSM 的存取 - Azure Key Vault 受控 HSM
description: 了解如何使用 Azure RBAC 和本機受控 HSM RBAC 保護對受控 HSM 的存取
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 18ffa0f878effda8888200c13ab312851aaebdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992159"
---
# <a name="secure-access-to-your-managed-hsms"></a>保護對受控 HSM 的存取

Azure Key Vault 受控 HSM 是一項雲端服務，可保護加密金鑰。 這是敏感且具業務關鍵性的資料，因此您必須藉由僅允許獲得授權的應用程式和使用者存取受控 HSM，來保護其存取。 本文提供受控 HSM 控制存取模型的概觀。 文中會說明驗證和授權，並說明如何保護對受控 HSM 的存取權。

本教學課程將逐步引導您完成簡單的範例，以說明如何使用 Azure RBAC 和本機受控 HSM RBAC 來達成職責區分和存取控制。 請參閱[受控 HSM 存取控制](access-control.md)，以了解受控 HSM 存取控制模型。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須具有下列項目︰

* Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* 訂用帳戶中的受控 HSM。 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以佈建並啟動受控 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="example"></a>範例

在此範例中，我們要開發一個使用 RSA 2,048 位元金鑰進行簽署作業的應用程式。 我們的應用程式會在具有[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)的 Azure 虛擬機器 (VM) 中執行。 用於簽署的 RSA 金鑰會儲存在我們的受控 HSM 中。

我們已識別下列負責管理、部署和稽核應用程式的角色：

- **安全性小組**：CSO (首席安全官) 辦公室的 IT 人員或類似的參與者。 安全性小組負責妥善保管金鑰。 用於簽署的 RSA 金鑰或 EC 金鑰，以及用於資料加密的 RSA 或 AES 金鑰。
- **開發人員和操作員**：這些人負責開發應用程式，並將其部署在 Azure 中。 此小組的成員不屬於安全性工作人員。 這些人不應具備敏感資料 (例如 RSA 金鑰) 的存取權。 其所部署的應用程式才應具備這類敏感資料的存取權。
- **稽核員**：此角色適用於不是開發小組成員或一般 IT 人員的參與者。 其會檢閱憑證、金鑰和祕密的使用與維護情形，以確保各項作業符合安全性標準。

另外還有一個不在我們應用程式範圍內的角色：訂用帳戶 (或資源群組) 系統管理員。 訂用帳戶管理員會設定安全性小組的初始存取權限。 其會使用資源群組 (具有此應用程式所需的資源)，將存取權授與安全性小組。

我們需要對我們的角色授權下列作業：

**安全性小組**
- 建立受控 HSM。
- 下載受控 HSM 安全性網域 (用於災害復原)
- 開啟記錄功能。
- 產生或匯入金鑰
- 建立災害復原所需的受控 HSM 備份。
- 設定受控 HSM 本機 RBAC 存取原則，為使用者和應用程式授與特定作業的權限。
- 定期輪替金鑰。

**開發人員和操作員**
- 向安全性小組取得用於簽署之 RSA 金鑰的參考 (金鑰 URI)。
- 開發和部署以程式設計方式存取金鑰的應用程式。

**稽核員**
- 檢閱金鑰到期日，以確保金鑰是最新的
- 監視角色指派，以確保金鑰只能由授權使用者/應用程式存取
- 檢閱受控 HSM 記錄，以確認金鑰的使用是否適當，以及是否符合資料安全性標準。

下表摘要說明指派給小組的角色以及用來存取受控 HSM 的資源。

| 角色 | 管理平面角色 | 資料平面角色 |
| --- | --- | --- |
| 安全性小組 | 受控 HSM 參與者 | 受控 HSM 管理員 |
| 開發人員與操作人員 | 無 | None |
| 稽核員 | None | 受控 HSM 密碼編譯稽核員 |
| 應用程式所使用之 VM 的受控識別| 無 | 受控 HSM 密碼編譯使用者 |
| 應用程式所使用之儲存體帳戶的受控識別| 無| 受控 HSM 服務加密 |

這三個小組角色需要其他資源的存取權以及受控 HSM 權限。 若要部署 VM (或 Azure App Service 的 Web Apps 功能)，開發人員和操作員需要這些資源類型的 `Contributor` 存取權。 稽核員需要受控 HSM 記錄儲存所在之儲存體帳戶的讀取權限。

若要指派管理平面角色 (Azure RBAC)，您可以使用 Azure 入口網站或任何其他管理介面，例如 Azure CLI 或 Azure PowerShell。 若要指派受控 HSM 資料平面角色，您必須使用 Azure CLI。

本節中的 Azure CLI 程式碼片段是基於下列假設而建置的︰

- Azure Active Directory 管理員已建立安全性群組來代表三個角色：Contoso Security Team、Contoso App DevOps 和 Contoso App Auditors。 系統管理員已將使用者新增到其各自的群組。
- 所有資源都位於 **ContosoAppRG** 資源群組。
- 受控 HSM 記錄儲存在 **contosologstorage** 儲存體帳戶中。
- **ContosoMHSM** 受控 HSM 和 **contosologstorage** 儲存體帳戶位於相同的 Azure 位置。

訂用帳戶管理員會將 `Managed HSM Contributor` 角色指派給安全性小組。 此角色可讓安全性小組管理現有的受控 HSM，以及建立新的受控 HSM。 如果有現有的受控 HSM，他們必須獲得「受控 HSM 管理員」角色的指派，才能加以管理。

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

安全性小組會設定記錄，並將角色指派給稽核員和 VM 應用程式。

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

本教學課程大多僅說明與存取控制有關的動作。 在此並不說明有關於在您的 VM 中部署應用程式、使用客戶管理的金鑰為儲存體帳戶開啟加密、建立受控 HSM 的其他動作和作業，以便讓範例著重於存取控制和角色管理。

我們的範例會說明一個簡單的案例。 真實案例可能會更複雜。 您可以根據需求調整金鑰保存庫的權限。 我們假設該安全性小組會提供 DevOps 人員在其應用程式中所使用的金鑰和密碼參考 (URI 和指紋)。 開發人員和操作員不需要任何資料平面存取權。 我們的重點放在如何保護您的金鑰保存庫。 在保護[您的 VM](https://azure.microsoft.com/services/virtual-machines/security/)、[儲存體帳戶](../../storage/blobs/security-recommendations.md)和其他 Azure 資源時，請做出類似的考量。

## <a name="resources"></a>資源

- [Azure RBAC 文件](../../role-based-access-control/overview.md)
- [Azure RBAC：內建角色](../../role-based-access-control/built-in-roles.md)
- [使用 Azure CLI 管理 Azure RBAC](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>下一步

如需適用於管理員的開始使用教學課程，請參閱[什麼是受控 HSM？](overview.md)。

若要進一步了解受控 HSM 記錄的使用記錄，請參閱[受控 HSM 記錄](logging.md)。
