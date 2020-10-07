---
title: 使用 Azure AD 設定許可權分類
description: 瞭解如何管理委派的許可權分類。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0add5001e2e62e26d448b06927210c14f17729f1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804295"
---
# <a name="configure-permission-classifications"></a>設定許可權分類

權限分類可讓您根據組織的原則和風險評估，識別不同權限的影響。 例如，您可以在同意原則中使用權限分類，以識別允許使用者同意的權限集。

## <a name="manage-permission-classifications"></a>管理許可權分類

目前僅支援「低影響」權限分類。 只有不需要管理員同意的委派權限可以分類為「低影響」。

> [!TIP]
> 進行基本登入所需的最小許可權 `openid` 為 `profile` 、 `email` 、 `User.Read` 和 `offline_access` ，這些都是 Microsoft Graph 上的所有委派許可權。 使用這些許可權時，應用程式可以讀取已登入使用者的完整設定檔詳細資料，即使使用者不再使用應用程式，也可以維持此存取權。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

遵循下列步驟以使用 Azure 入口網站分類許可權：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **權限分類**。
1. 選擇 [新增權限]，將另一個權限分類為「低影響」。
1. 選取 API，然後選取委派的權限。

在此範例中，我們已將單一登入所需的最小權限集合分類：

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="權限分類":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用最新的 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)，來分類權限。 權限分類是在 API (發佈權限) 的 **ServicePrincipal** 物件上設定。

#### <a name="list-the-current-permission-classifications-for-an-api"></a>列出 API 目前的許可權分類

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 讀取 API 的委派權限分類：

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>將許可權分類為「低影響」

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 尋找您想要分類的委派權限：

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 使用權限名稱和識別碼來設定權限分類：

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>移除委派的許可權分類

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 尋找您想要移除的委派權限分類：

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 刪除權限分類：

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>後續步驟

若要深入了解：

* [設定使用者同意設定](configure-user-consent.md)
* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [了解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)
* [對應用程式授與全租用戶的管理員同意](grant-admin-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/active-directory-v2-scopes.md)

若要取得協助或尋找您的問題解答：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
