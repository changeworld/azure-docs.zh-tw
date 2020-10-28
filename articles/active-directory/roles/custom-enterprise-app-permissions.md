---
title: Azure Active Directory 中的自訂角色適用的應用程式權限 | Microsoft Docs
description: 在 Azure 入口網站、PowerShell 或圖形 API 中預覽自訂 Azure AD 角色的企業應用程式權限。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 0b1857583696a2a14d6f5d40174c536cf4772845
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378733"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory 中的自訂角色適用的企業應用程式權限

本文包含 Azure Active Directory (Azure AD) 中的自訂角色定義目前可用的企業應用程式權限。 在本文中，您會看到一些常見案例的權限清單，以及企業應用程式權限的完整清單。 目前並未在此版本中推出應用程式 Proxy 權限。

## <a name="required-license-plan"></a>必要授權方案

若要使用此功能，您的 Azure AD 組織必須具備 Azure AD Premium P1 授權。 若要尋找適用於您需求的正確授權，請參閱[比較 Free、Basic 及 Premium 版本的正式運作功能](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="enterprise-application-permissions"></a>企業應用程式權限

如需如何使用這些權限的詳細資訊，請參閱[指派自訂角色來管理企業應用程式](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>將使用者或群組指派給應用程式

委派指派可存取 SAML 型單一登入應用程式的使用者和群組。 所需的權限

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>建立資源庫應用程式

委派建立 Azure AD 資源庫應用程式，例如 ServiceNow、F5、Salesforce 等等。 所需的權限：

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>設定基本 SAML URL

委派更新和讀取 SAML 型單一登入應用程式的基本 SAML 設定。 所需的權限：

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>變換或建立簽署憑證

委派管理 SAML 型單一登入應用程式的簽署憑證。 所需的權限。

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>更新即將到期的登入憑證通知電子郵件地址

委派更新 SAML 型單一登入應用程式即將到期的登入憑證通知電子郵件地址。 所需的權限：

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>管理 SAML 權杖簽章和登入演算法

委派更新 SAML 型單一登入應用程式的 SAML 權杖簽章和登入演算法。 所需的權限：

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>管理使用者屬性和宣告

委派建立、刪除和更新 SAML 型單一登入應用程式的使用者屬性和宣告。 所需的權限：

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>應用程式佈建權限

執行任何寫入作業 (例如，透過 UI 管理作業、結構描述或認證) 時，也需要有讀取權限才能檢視佈建頁面。

目前，若要將範圍設定為所有使用者和群組或已指派的使用者和群組，必須同時具備 synchronizationJob 和 synchronizationCredentials 權限。

### <a name="turn-on-or-restart-provisioning-jobs"></a>開啟或重新啟動佈建作業

委派開啟、關閉和重新啟動佈建作業的能力。 所需的權限：

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>設定佈建結構描述  

委派屬性對應的更新。 所需的權限：

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>讀取與應用程式物件相關聯的佈建設定

委派讀取物件相關佈建設定的能力。 所需的權限：

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>讀取與服務主體相關聯的佈建設定

委派讀取服務主體相關佈建設定的能力。 所需的權限：

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>授與應用程式存取權以進行佈建  

委派授與應用程式存取權以進行佈建的能力。 範例輸入 Oauth 持有人權杖。 所需的權限：

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>權限的完整清單

權限 | 說明
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | 讀取應用程式原則的所有屬性。
microsoft.directory/applicationPolicies/allProperties/update | 更新應用程式原則的所有屬性。
microsoft.directory/applicationPolicies/basic/update | 更新應用程式原則的標準屬性。
microsoft.directory/applicationPolicies/create | 建立應用程式原則。
microsoft.directory/applicationPolicies/createAsOwner | 建立應用程式原則。 建立者會新增為第一個擁有者。
microsoft.directory/applicationPolicies/delete | 刪除應用程式原則。
microsoft.directory/applicationPolicies/owners/read | 讀取應用程式原則的擁有者。
microsoft.directory/applicationPolicies/owners/update | 更新應用程式原則的擁有者屬性。
microsoft.directory/applicationPolicies/policyAppliedTo/read | 讀取套用到物件清單的應用程式原則。
microsoft.directory/applicationPolicies/standard/read | 讀取應用程式原則的標準屬性。
microsoft.directory/servicePrincipals/allProperties/allTasks | 建立和刪除 servicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。
microsoft.directory/servicePrincipals/allProperties/read | 讀取 servicePrincipals 的所有屬性。
microsoft.directory/servicePrincipals/allProperties/update | 更新 servicePrincipals 的所有屬性。
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 讀取服務主體的角色指派。
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服務主體的角色指派。
microsoft.directory/servicePrincipals/appRoleAssignments/read | 讀取指派給服務主體的角色指派。
microsoft.directory/servicePrincipals/audience/update | 更新服務主體的對象屬性。
microsoft.directory/servicePrincipals/authentication/update | 更新服務主體的驗證屬性。
microsoft.directory/servicePrincipals/basic/update | 更新服務主體的基本屬性。
microsoft.directory/servicePrincipals/create | 建立服務主體。
microsoft.directory/servicePrincipals/createAsOwner | 建立服務主體。 建立者會新增為第一個擁有者。
microsoft.directory/servicePrincipals/credentials/update | 更新服務主體的認證屬性。
microsoft.directory/servicePrincipals/delete | 刪除服務主體。
microsoft.directory/servicePrincipals/disable | 停用服務主體。
microsoft.directory/servicePrincipals/enable | 啟用服務主體。
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 讀取服務主體的密碼單一登入認證。
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 管理服務主體的密碼單一登入認證。
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 讀取授與服務主體的委派權限。
microsoft.directory/servicePrincipals/owners/read | 讀取服務主體的擁有者。
microsoft.directory/servicePrincipals/owners/update | 更新服務主體的擁有者。
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | 讀取服務主體的原則。
microsoft.directory/servicePrincipals/policies/update | 更新服務主體的原則。
microsoft.directory/servicePrincipals/standard/read | 讀取服務主體的標準屬性。
microsoft.directory/servicePrincipals/synchronization/standard/read | 讀取與服務主體相關聯的佈建設定。
microsoft.directory/servicePrincipals/tag/update | 更新服務主體的標籤屬性。
microsoft.directory/applicationTemplates/instantiate | 從應用程式範本具現化資源庫應用程式。
microsoft.directory/auditLogs/allProperties/read | 讀取稽核記錄。
microsoft.directory/signInReports/allProperties/read | 讀取登入報表。
microsoft.directory/applications/synchronization/standard/read | 讀取與應用程式物件相關聯的佈建設定。
microsoft.directory/servicePrincipals/synchronizationJobs/manage | 對服務主體資源管理其作業同步處理的各個層面
microsoft.directory/servicePrincipals/synchronization/standard/read | 讀取與服務主體相關聯的佈建設定
microsoft.directory/servicePrincipals/synchronizationSchema/manage | 對服務主體資源管理其結構描述同步處理的各個層面
microsoft.directory/provisioningLogs/allProperties/read | 讀取佈建記錄的所有屬性

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站、Azure AD PowerShell 和圖形 API 建立自訂角色](custom-create.md)
- [檢視自訂角色的指派](custom-view-assignments.md)
