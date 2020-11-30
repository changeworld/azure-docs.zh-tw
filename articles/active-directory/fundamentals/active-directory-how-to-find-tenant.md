---
title: 如何尋找您的租使用者識別碼-Azure Active Directory
description: 有關如何尋找和 Azure Active Directory 租使用者識別碼加入現有 Azure 訂用帳戶的指示。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326585"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>如何尋找您的 Azure Active Directory 租使用者識別碼

Azure 訂用帳戶具有與 Azure Active Directory (Azure AD) 之間的信任關係。 Azure AD 受信任，可驗證訂用帳戶的使用者、服務和裝置。 每個訂用帳戶都有與其相關聯的租使用者識別碼，您可以透過幾種方式來尋找訂用帳戶的租使用者識別碼。

## <a name="find-tenant-id-through-the-azure-portal"></a>透過 Azure 入口網站尋找租使用者識別碼

1. 登入 [Azure 入口網站](https://portal.azure.com)。
 
1. 選取 **Azure Active Directory**。

1. 選取 [屬性] 。

1. 然後，向下卷到 [ **租使用者識別碼** ] 欄位。 您的租使用者識別碼將會在 box 中。

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-屬性-租使用者識別碼-租使用者識別碼欄位":::

## <a name="find-tenant-id-with-powershell"></a>使用 PowerShell 尋找租使用者識別碼

您也可以透過程式設計的方式找到租使用者。 若要尋找具有 Azure PowerShell 的租使用者識別碼，請使用 Cmdlet `Get-AzTenant` 。

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
如需詳細資訊，請參閱這 Azure PowerShell [AzTenant](/powershell/module/az.accounts/get-aztenant)的 Cmdlet 參考。


## <a name="find-tenant-id-with-cli"></a>使用 CLI 尋找租使用者識別碼
如果您想要使用命令列介面來尋找租使用者識別碼，您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)來執行此動作。 

針對 Azure CLI，請使用 **az login**、 **az account list** 或 **az 帳戶租使用者清單** 其中一個命令，如下列範例所示。 請注意每個命令的輸出中每個訂用帳戶的 **tenantId** 屬性。

```azurecli-interactive
az login
az account list
az account tenant list
```

如需詳細資訊，請參閱 [az 登](/cli/azure/reference-index#az_login) 入命令參考、 [az 帳戶](/cli/azure/ext/account/account) 命令參考或 [az 帳戶租](/cli/azure/ext/account/account/tenant) 使用者命令參考。


針對 Microsoft 365 CLI，請使用 Cmdlet **租使用者識別碼** ，如下列範例所示：
 
```cli
m365 tenant id get
```

如需詳細資訊，請參閱 Microsoft 365 [租使用者識別碼取得](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) 命令參考。


## <a name="next-steps"></a>後續步驟

- 若要建立新的 Azure AD 租使用者，請參閱 [快速入門：在 Azure Active Directory 中建立新的租](active-directory-access-create-new-tenant.md)使用者。

- 若要瞭解如何將訂用帳戶關聯或新增至租使用者，請參閱 [將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租使用者](active-directory-how-subscriptions-associated-directory.md)。

- 若要瞭解如何尋找物件識別碼，請參閱 [尋找使用者物件識別碼](/partner-center/find-ids-and-domain-names#find-the-user-object-id)。