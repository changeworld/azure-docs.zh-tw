---
title: 管理使用者和系統管理員許可權-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD 上審核和管理應用程式的許可權。 例如，撤銷授與應用程式的擁有權限。
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
ms.openlocfilehash: 95e13cedc0cdbaedc8c00b9d855057da7e631c19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510873"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>在 Azure Active Directory 中對 overprivileged 或可疑應用程式採取動作

瞭解如何審核和管理應用程式許可權。 本文提供不同的動作，可讓您根據案例來保護您的應用程式。 這些動作適用于透過使用者或系統管理員同意新增至 Azure Active Directory (Azure AD) 租使用者的所有應用程式。

如需同意應用程式的詳細資訊，請參閱 [Azure Active Directory 同意架構](../develop/consent-framework.md)。

## <a name="prerequisites"></a>先決條件

若要執行下列動作，您必須以全域管理員、應用程式系統管理員或雲端應用程式系統管理員的身分登入。

若要限制應用程式的存取權，您必須要求指派使用者，然後將使用者或群組指派給應用程式。  如需詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

您可以存取 Azure AD 入口網站，以取得內容相關的 PowerShell 腳本來執行動作。
 
1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**  >  **企業應用程式**。
3. 選取您要限制存取的應用程式。
4. 選取 [權限]。 在命令列中，選取 [ **審核許可權**]。

![[審核許可權] 視窗的螢幕擷取畫面。](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>控制應用程式的存取

建議您開啟 **使用者指派** 設定，以限制應用程式的存取權。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**  >  **企業應用程式**。
3. 選取您要限制存取的應用程式。
4. 選取 [ **屬性**]，然後將 [ **需要使用者需求** ] 設定為 **[是]**。
5. 選取 [ **使用者和群組**]，然後移除指派給該應用程式的不必要使用者。
6. 將使用者或群組指派給應用程式。

（選擇性）您可以使用 PowerShell 來移除指派給應用程式的所有使用者。

## <a name="revoke-all-permissions-for-an-application"></a>撤銷應用程式的擁有權限

使用 PowerShell 腳本撤銷授與此應用程式的擁有權限。

> [!NOTE]
> 撤銷目前授與的許可權並不會阻止使用者重新同意應用程式。 如果您想要禁止使用者同意，請參閱 [設定使用者同意應用程式的方式](configure-user-consent.md)。

（選擇性）您可以停用應用程式，讓使用者無法存取應用程式，並讓應用程式無法存取您的資料。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**  >  **企業應用程式**。
3. 選取您要限制存取的應用程式。
4. 選取 [ **屬性**]，然後將 [ **啟用使用者登入？** ] 設定為 [ **否**]。

## <a name="investigate-a-suspicious-application"></a>調查可疑的應用程式

建議您開啟 **使用者指派** 設定，以限制應用程式的存取權。 然後檢查使用者和系統管理員已授與應用程式的許可權。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
3. 選取**Azure Active Directory**  >  **企業應用程式**。
5. 選取您要限制存取的應用程式。
6. 選取 [ **屬性**]，然後將 [ **需要使用者需求** ] 設定為 **[是]**。
7. 選取 **許可權**，並檢查系統管理員和使用者同意的許可權。

您也可以選擇使用 PowerShell 來：

- 移除所有指派的使用者，使其無法登入應用程式。
- 使可存取應用程式之使用者的重新整理權杖失效。
- 撤銷應用程式的擁有權限。

或者，您可以停用應用程式，以封鎖使用者的存取權，並停止應用程式對您資料的存取。


## <a name="disable-a-malicious-application"></a>停用惡意應用程式 

建議您停用應用程式，以封鎖使用者的存取權，並讓應用程式無法存取您的資料。 如果您改為刪除應用程式，則使用者可以重新同意應用程式，並將存取權授與您的資料。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**  >  **企業應用程式**。
3. 選取您要限制存取的應用程式。
4. 選取 [ **屬性**]，然後複製物件識別碼。

### <a name="powershell-commands"></a>PowerShell 命令


取得服務主體物件識別碼。

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**  >  **企業應用程式**。
3. 選取您要限制存取的應用程式。
4. 選取 [ **屬性**]，然後複製物件識別碼。

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
移除已指派給應用程式的所有使用者。
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

撤銷授與應用程式的許可權。

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
使重新整理權杖失效。
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
- [管理應用程式的同意並評估同意要求](manage-consent-requests.md)
- [設定使用者同意](configure-user-consent.md)
- [設定管理員同意工作流程](configure-admin-consent-workflow.md)
