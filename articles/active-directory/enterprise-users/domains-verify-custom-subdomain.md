---
title: 使用 PowerShell 和圖形 Azure Active Directory 來變更子域驗證類型 |Microsoft Docs
description: 變更從 Azure Active Directory 中的根網域設定繼承的預設子域驗證設定。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330a34f38aaa73fca8290d1638219fa8f517e1cb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647370"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>在 Azure Active Directory 中變更子域驗證類型

將根域新增至 Azure Active Directory (Azure AD) 之後，新增至 Azure AD 組織中該根目錄的所有後續子域都會自動繼承根域的驗證設定。 但是，如果您想要從根域設定獨立管理網域驗證設定，您現在可以使用 Microsoft Graph API。 例如，如果您有同盟根域（例如 contoso.com），本文可協助您驗證子域（例如 child.contoso.com），而不是同盟。

在 Azure AD 入口網站中，當父系網域是同盟的，且系統管理員嘗試驗證 [ **自訂功能變數名稱** ] 頁面上的受控子域時，您將會收到「無法新增網域」錯誤，原因是「一或多個屬性包含不正確值」。 如果您嘗試從 Microsoft 365 系統管理中心新增此子域，您將會收到類似的錯誤。 如需有關錯誤的詳細資訊，請參閱 [子域不會繼承 Office 365、Azure 或 Intune 中的父網域變更](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)。

## <a name="how-to-verify-a-custom-subdomain"></a>如何驗證自訂子域

因為子域預設會繼承根域的驗證類型，所以您必須使用 Microsoft Graph 將子域升級為 Azure AD 中的根域，如此您才能將驗證類型設為您想要的類型。

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>新增子域並查看其驗證類型

1. 使用 PowerShell 新增具有根域預設驗證類型的新子域。 Azure AD 和 Microsoft 365 的系統管理中心尚未支援這種操作。

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. 使用 [Azure AD Graph EXPLORER](https://graphexplorer.azurewebsites.net) 取得網域。 因為網域不是根域，所以它會繼承根域驗證類型。 使用您自己的租使用者識別碼，您的命令和結果看起來可能如下所示：

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>使用 Azure AD Graph Explorer API 將此設為根域

使用下列命令來升級子域：

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>變更子域驗證類型

1. 使用下列命令來變更子域驗證類型：

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. 透過 Azure AD Graph Explorer 中的 GET 驗證現在已管理子域驗證類型：

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>後續步驟

- [新增自訂網域名稱](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [管理網域名稱](domains-manage.md)
- [使用 Microsoft Graph API 以強制刪除自訂網域名稱](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)