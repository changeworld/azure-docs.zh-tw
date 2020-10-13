---
title: 受控 HSM 資料平面角色管理 - Azure Key Vault | Microsoft Docs
description: 使用本文來管理受控 HSM 的角色指派
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 814167425fcd39e90edccd952e1a3e4fbd570988
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818026"
---
# <a name="managed-hsm-role-management"></a>受控 HSM 角色管理

> [!NOTE]
> Key Vault 支援兩種類型的資源：保存庫和受控 HSM。 本文討論**受控 HSM**。 如果您想要了解如何管理保存庫，請參閱[使用 Azure CLI 來管理 Key Vault](../general/manage-with-cli2.md)。

如需受控 HSM 的概觀，請參閱[什麼是受控 HSM？](overview.md)。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本文說明如何管理受控 HSM 資料平面的角色。 若要了解受控 HSM 存取控制模型，請參閱[受控 HSM 存取控制](access-control.md)。

若要允許安全性主體 (例如使用者、服務主體、群組或受控識別) 執行受控 HSM 資料平面作業，他們必須被指派允許執行這些作業的角色。 例如，如果您想要允許應用程式使用金鑰執行簽署作業，則必須為其指派以 "Microsoft.KeyVault/managedHSM/keys/sign/action" 作為其中一個資料動作的角色。 角色可指派於特定範圍。 受控 HSM 本機 RBAC 支援兩個範圍：HSM 範圍 (`/` 或 `/keys`) 和每個金鑰 (`/keys/<keyname>`)。

如需所有受控 HSM 內建角色及其允許之作業的清單，請參閱[受控 HSM 內建角色](built-in-roles.md)。

## <a name="prerequisites"></a>Prerequisites

若要使用本文中的 Azure CLI 命名，您必須具有下列項目：

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

## <a name="create-a-new-role-assignment"></a>建立新的角色指派

### <a name="assign-roles-for-all-keys"></a>指派所有金鑰的角色

使用 `az keyvault role assignment create` 命令，針對 ContosoHSM 中的所有**金鑰** (範圍 `/keys`)，將**受控 HSM 密碼編譯金鑰**角色指派給使用者主體名稱 **user2\@contoso.com** 所識別的使用者。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>指派特定金鑰的角色

使用 `az keyvault role assignment create` 命令，針對名為 **myrsakey** 的特定金鑰，將**受控 HSM 密碼編譯金鑰**角色指派給使用者主體名稱 **user2\@contoso.com** 所識別的使用者。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>列出現有的角色指派

使用 `az keyvault role assignment list` 列出角色指派。

在範圍 / (未指定 --scope 時的預設值) 為所有使用者 (指定了 --assignee 時的預設值) 進行的所有角色指派

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

在 HSM 層級為特定使用者 **user1@contoso.com** 進行的所有角色指派。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

針對特定金鑰 **myrsakey** 的特定使用者 **user2@contoso.com** 進行的所有角色指派。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

針對特定金鑰 **myrsakey** 的特定使用者 **user2@contoso.com** 進行的**受控 HSM 密碼編譯人員**角色的特定角色指派


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>刪除角色指派

使用 `az keyvault role assignment delete` 命令，刪除指派給金鑰 **myrsakey2** 的使用者 **user2\@contoso.com** 的**受控 HSM 密碼編譯人員**角色。

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>列出所有可用的角色定義

使用 `az keyvault role definition list` 命令列出所有角色定義。

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>下一步

- 請參閱 [Azure 角色型存取控制 (RBAC)](../../role-based-access-control/overview.md) 的概觀。
- 請參閱[受控 HSM 角色管理](role-management.md)的教學課程
- 深入了解[受控 HSM 存取控制模型](access-control.md)
- 查看所有[受控 HSM 本機 RBAC 的內建角色](built-in-roles.md)
