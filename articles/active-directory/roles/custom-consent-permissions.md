---
title: Azure Active Directory 中自訂角色的應用程式同意權限 | Microsoft Docs
description: 在 Azure 入口網站、PowerShell 或圖形 API 中預覽自訂 Azure AD 角色的應用程式同意權限。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 10/06/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: 372ae994c62767032f1bfb069093f66d738c23b3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378738"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory 中自訂角色的應用程式同意權限

本文包含 Azure Active Directory (Azure AD) 中自訂角色定義目前可用的應用程式同意權限。 在本文中，您會找到一些常見案例所需的許可權，這些案例與應用程式同意和許可權相關。

## <a name="required-license-plan"></a>必要授權方案

若要使用此功能，您的 Azure AD 組織必須具備 Azure AD Premium P1 授權。 若要尋找適用於您需求的正確授權，請參閱[比較 Free、Basic 及 Premium 版本的正式運作功能](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="app-consent-permissions"></a>應用程式同意權限

請使用本文中所列的權限來管理應用程式同意原則，以及將同意授與應用程式的權限。

> [!NOTE]
> Azure AD 管理入口網站尚未支援將本文所列的權限新增至自訂目錄角色定義。 您必須[使用 Azure AD PowerShell 來建立自訂目錄角色](custom-create.md#create-a-role-using-powershell)，其具有本文所列的權限。

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>代表自身將委派的許可權授與應用程式 (使用者同意)

允許使用者代表自身將同意授與應用程式 (使用者同意)，但受限於應用程式同意原則。

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

其中 `{id}` 會被[應用程式同意原則](../manage-apps/manage-app-consent-policies.md)的識別碼取代，這會設定必須符合才能讓此權限成為作用中的條件。

例如，若要允許使用者代表自己授與同意，但受限於識別碼為 `microsoft-user-default-low` 的內建應用程式同意原則，您將會使用權限 `...managePermissionGrantsForSelf.microsoft-user-default-low`。

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>代表全部將權限授與應用程式 (管理員同意)

若要同時針對委派的權限和應用程式權限，將全租用戶管理員同意委派給應用程式 (應用程式角色)：

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

其中 `{id}` 會被[應用程式同意原則](../manage-apps/manage-app-consent-policies.md)的識別碼取代，這會設定必須符合才能讓此權限成為可用的條件。

例如，若要允許角色受託人將全租用戶的管理員同意授與應用程式，但受限於識別碼為 `low-risk-any-app` 的自訂[應用程式同意原則](../manage-apps/manage-app-consent-policies.md)，您將會使用權限 `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app`。

### <a name="managing-app-consent-policies"></a>管理應用程式同意原則

委派[應用程式同意原則的建立、更新和刪除](../manage-apps/manage-app-consent-policies.md)。

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>權限的完整清單

權限 | 說明
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | 代表自身將同意能力授與應用程式 (使用者同意)，但受限於應用程式同意原則 `{id}`。
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | 代表自身將同意權限授與應用程式 (全租用戶管理員同意)，但受限於應用程式同意原則 `{id}`。
microsoft.directory/permissionGrantPolicies/standard/read | 授與讀取應用程式同意原則的能力。
microsoft.directory/permissionGrantPolicies/basic/update | 授與更新現有應用程式同意原則上基本屬性的能力。
microsoft.directory/permissionGrantPolicies/create | 授與建立應用程式同意原則的能力。
microsoft.directory/permissionGrantPolicies/delete | 授與刪除應用程式同意原則的能力。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站、Azure AD PowerShell 和圖形 API 建立自訂角色](custom-create.md)
- [檢視自訂角色的指派](../roles/view-assignments.md)
