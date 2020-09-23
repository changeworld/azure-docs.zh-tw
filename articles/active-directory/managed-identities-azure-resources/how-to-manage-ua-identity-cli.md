---
title: 管理使用者指派的受控識別-Azure CLI-Azure AD
description: 如何使用 Azure CLI 建立、列出和刪除使用者指派之受控識別的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a1a991ab79c38dad1a89533091d80406615d35
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969465"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>使用 Azure CLI 建立、列出和刪除使用者指派的受控識別


適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供具有受控識別的 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您將瞭解如何使用 Azure CLI 來建立、列出和刪除使用者指派的受控識別。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行範例腳本，您有兩個選項：
    - 使用您可以使用程式碼區塊右上角的 [**試試看**] 按鈕開啟的[Azure Cloud Shell](../../cloud-shell/overview.md)。
    - 安裝最新版本的 [Azure CLI](/cli/azure/install-azure-cli)，然後使用 [az Login 登](/cli/azure/reference-index#az-login)入 Azure，以在本機執行腳本。 使用與您想要在其中建立資源的 Azure 訂用帳戶相關聯的帳戶。   

> [!NOTE]
> 若要在使用 app service 主體時修改使用者權限，您必須在 Azure AD 圖形 API 中提供服務主體額外的許可權，因為 CLI 的部分會對圖形 API 執行 GET 要求。 否則，您最後可能會收到「許可權不足，無法完成作業」訊息。 若要這樣做，您必須移至 Azure Active Directory 中的應用程式註冊、選取您的應用程式、按一下 [API 許可權]、向下滾動，然後選取 [Azure Active Directory 圖形]。 從該處選取 [應用程式許可權]，然後新增適當的許可權。 

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別 

若要建立使用者指派的受控識別，您的帳戶需要[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

請使用 [az identity create](/cli/azure/identity#az-identity-create) 命令，建立使用者指派的受控識別。 `-g` 參數會指定要建立使用者指派之受控識別的資源群組，而 `-n` 參數則指定其名稱。 將 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 參數取代為您自己的值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>列出使用者指派的受控識別

若要列出/讀取使用者指派的受控識別，您的帳戶需要[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

若要列出使用者指派的受控識別，請使用 [az identity list](/cli/azure/identity#az-identity-list) 命令。 將 `<RESOURCE GROUP>` 取代為您自己的值：

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

在 json 回應中，使用者指派的受控識別具備`"Microsoft.ManagedIdentity/userAssignedIdentities"`為索引鍵傳回的值`type`。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>刪除使用者指派的受控識別

若要刪除使用者指派的受控識別，您的帳戶需要[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

若要刪除使用者指派的受控識別，請使用 [az identity delete](/cli/azure/identity#az-identity-delete) 命令。  -n 參數會指定其名稱，而 -g 參數會指定建立使用者指派之受控識別所在的資源群組。 將 `<USER ASSIGNED IDENTITY NAME>` 和 `<RESOURCE GROUP>` 參數取代為您自己的值：

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 將使用者指派的受控識別從受指派的任何資源中刪除，並不會移除參考。 請使用 `az vm/vmss identity remove` 命令將其從 VM/VMSS 中移除

## <a name="next-steps"></a>下一步

如需 Azure CLI 身分識別命令的完整清單，請參閱 [az identity](/cli/azure/identity)。

如需如何將使用者指派之受控識別指派至 Azure VM 的相關資訊，請參閱[使用 Azure CLI 對 Azure VM 設定 Azure 資源的受控識別](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
