---
title: 使用系統配置的託管識別存取 Azure 金鑰保管庫
description: 瞭解如何為應用服務應用程式建立託管識別,以及如何使用它存取 Azure 密鑰保管庫
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432121"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>使用託管識別提供金鑰保存庫身分驗證

Azure 活動目錄的託管標識允許應用輕鬆存取其他受 Azure AD 保護的資源。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 

本文介紹如何為應用服務應用程式創建託管標識,並使用它訪問 Azure 密鑰保管庫。 您要在 Azure VM 中託管的應用程式,請參閱[使用 Windows VM 系統分配的託管識別存取 Azure 金鑰保管庫](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites 

要完成本指南,您必須具有以下資源。 

- 一個金鑰保存庫。 您可以使用現有的金鑰保存庫，或依照下列其中一個快速入門中的步驟建立新的金鑰保存庫：
   - [使用 Azure CLI 建立金鑰保存庫](../secrets/quick-create-cli.md)
   - [使用 Azure PowerShell 建立金鑰保存庫](../secrets/quick-create-powershell.md)
   - [使用 Azure 入口網站建立金鑰保存庫](../secrets/quick-create-portal.md)。
- 要向其授予金鑰保管庫訪問許可權的現有應用服務應用程式。 您可以按照[應用服務文檔中](../../app-service/overview.md)的步驟快速創建一個。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或[Azure 電源外殼](/powershell/azure/overview)。 或者，您可以使用 [Azure 入口網站](https://portal.azure.com)。


## <a name="adding-a-system-assigned-identity"></a>新增系統指派的身分識別 

首先,必須向應用程式添加系統分配的標識。 
 
### <a name="azure-portal"></a>Azure 入口網站 

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。 

1. 如果您使用函式應用程式，請瀏覽至 [平台功能]****。 若使用類型的應用程式，請在左側導覽列中向下捲動到 [設定]****。 

1. 選取 [受控身分識別]****。 

1. 在 [系統指派]**** 索引標籤內，將 [狀態]**** 切換為 [開啟]****。 按一下 [檔案]  。 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

此快速入門需要 Azure CLI 版本 2.0.4 或更高版本。 執行 `az --version` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

要使用 Azure CLI 登入,請使用[az 登入](/cli/azure/reference-index?view=azure-cli-latest#az-login)指令:

```azurecli-interactive
az login
```

有關使用 Azure CLI 的登入選項的詳細資訊,請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。 

要為此應用程式建立識別,請使用 Azure CLI [az Webapp 識別分配](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign)指令或[az 函數應用識別配置](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)指令:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

將下下一個`PrincipalId`節會需要的 。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>給應用程式配對金鑰保存的權限 

### <a name="azure-portal"></a>Azure 入口網站

1.  導航到密鑰保管庫資源。 

1.  選擇**訪問策略**,然後按下 **「添加存取策略**」。 

1.  在 **「機密權限**」中,選擇 **「獲取、清單**」。 

1.  選擇 **「選擇主體**」,並在搜尋欄位中輸入應用的名稱。  在結果清單中選擇應用,然後按一下**選擇。** 

1.  按下「**新增**」 以完成新增新存取策略。

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

要授予應用程式對金鑰保管庫的存取許可權,請使用 Azure CLI [az 金鑰保管庫集原則](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)命令,將**ObjectId**參數提供上面提到的**主體 Id。**

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>後續步驟

- [Azure 金鑰保管庫安全性:識別和存取管理](overview-security.md#identity-and-access-management)
- [使用存取控制原則提供 Key Vault 驗證](group-permissions-for-apps.md)
- [保護您的金鑰保存函式庫](secure-your-key-vault.md)。
- [Azure 金鑰保管庫開發人員指南](developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](best-practices.md)