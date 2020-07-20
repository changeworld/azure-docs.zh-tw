---
title: 管理使用者和系統管理員許可權-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD 上檢查和管理應用程式的許可權。 例如，如果您想要撤銷所有授與給應用程式的許可權。
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277622"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>對 overpriviledged 或可疑的應用程式採取行動 Azure Active Directory

瞭解如何審查和管理應用程式許可權。 根據案例，本文將提供您可執行檔不同動作來保護您的應用程式。 這適用于透過使用者或系統管理員同意新增至 Azure Active Directory (Azure AD) 租使用者的所有應用程式。

如需同意應用程式的詳細資訊，請參閱 [Azure Active Directory 同意架構](../develop/consent-framework.md)。

## <a name="prerequisites"></a>必要條件

若要能夠執行下列動作，您必須以全域管理員、應用程式系統管理員或雲端應用程式系統管理員的身分登入。

若要限制應用程式的存取權，您必須要求指派使用者，然後將使用者或群組指派給應用程式。  如需詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

您可以存取 Azure AD 入口網站，取得內容相關的 PowerShell 腳本來執行動作。
 
1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
3. 選取您想要限制存取的應用程式。
4. 選取 [權限]****。 在命令列中，選取 [**審查許可權**]。

![檢閱權限](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>我想要控制應用程式的存取

我們建議您在上開啟 [使用者指派] 設定，以限制對此應用程式的存取。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
3. 選取您想要限制存取的應用程式。
4. 選取 [**屬性**]，然後將 [需要使用者需求] 設定設為 [是]。
5. 選取 [**使用者和群組**]，然後移除指派給應用程式的不必要使用者。
6. 將使用者 (s) 或群組 (s) 指派給應用程式。

（選擇性）您可以使用 PowerShell 移除指派給應用程式的所有使用者。

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>我想要撤銷應用程式的擁有權限

使用 PowerShell 會撤銷授與此應用程式的擁有權限。

> [!NOTE]
> 撤銷目前授與的許可權，並不會阻止使用者 reconseing 應用程式。 如果您想要封鎖使用者同意應用程式，請參閱[設定終端使用者同意應用程式的方式](configure-user-consent.md)。

（選擇性）您可以停用應用程式，以防止使用者存取應用程式和應用程式，使其無法存取您的資料。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
3. 選取您想要限制存取的應用程式。
4. 選取 [**屬性**]，然後將 [已啟用讓使用者登入？]為否。

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>應用程式可疑，我想要調查

我們建議您將 [使用者指派] 設定設為 [開啟]，並檢查使用者和系統管理員已授與應用程式的許可權，以限制此應用程式的存取權。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
3. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
5. 選取您想要限制存取的應用程式。
6. 選取 [**屬性**]，然後將 [需要使用者需求] 設定設為 [是]。
7. 選取 [**許可權**]，並查看系統管理員和使用者同意的許可權。

（選擇性）您可以：

- 使用 PowerShell，移除所有指派的使用者，以防止他們登入應用程式。
- 使用 PowerShell，可讓具有應用程式存取權的使用者失效重新整理權杖。
- 使用 PowerShell，撤銷此應用程式的擁有權限
- 停用應用程式以封鎖使用者存取，並停止此應用程式存取您的資料。


## <a name="application-is-malicious-and-im-compromised"></a>應用程式是惡意的，我受到入侵

我們建議您停用應用程式，以防止使用者存取應用程式和應用程式，使其無法存取您的資料。 如果您改為刪除應用程式，使用者將能夠重新同意至應用程式，並授與存取權給您的資料。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
3. 選取您想要限制存取的應用程式。
4. 選取 [**屬性**]，然後複製 [物件識別碼]。

### <a name="powershell-commands"></a>PowerShell 命令


取得服務主體物件識別碼

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory**  >  **企業應用程式**]。
3. 選取您想要限制存取的應用程式。
4. 選取 [**屬性**]，然後複製 [物件識別碼]。

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
移除指派給應用程式的所有使用者。
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

撤銷授與應用程式的許可權

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
使重新整理權杖失效
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>後續步驟
- [管理對應用程式的同意並評估同意要求](manage-consent-requests.md)
- [設定使用者同意](configure-user-consent.md)
- [設定系統管理員同意工作流程](configure-admin-consent-workflow.md)
