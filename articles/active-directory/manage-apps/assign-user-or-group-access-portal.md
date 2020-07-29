---
title: 將使用者或群組指派給 Azure AD 中的企業應用程式
description: 如何在 Azure Active Directory 中選取企業應用程式以將使用者或群組指派給它
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeab9cbb68567b6af3b1381b8ec5e67e82461ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763732"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者或群組指派給企業應用程式

本文說明如何從 Azure 入口網站或使用 PowerShell，將使用者或群組指派給 Azure Active Directory (Azure AD) 中的企業應用程式。 當您將使用者指派給應用程式時，應用程式會顯示在使用者的[我的應用程式存取面板](https://myapps.microsoft.com/)中以便存取。 如果應用程式公開角色，您也可以將特定角色指派給使用者。

若要進行更好的控制，可以將特定類型的企業應用程式設為[需要使用者指派](#configure-an-application-to-require-user-assignment)。 

若要[將使用者或群組指派給企業應用程式](#assign-users-or-groups-to-an-app-via-the-azure-portal)，您必須以全域管理員、應用程式管理員、雲端應用程式管理員或企業應用程式的指派擁有者身分登入。

> [!NOTE]
> 以群組為基礎的指派需要 Azure Active Directory Premium P1 或 P2 版本。 以群組為基礎的指派僅支援安全性群組。 目前不支援巢狀群組成員資格和 Office 365 群組。 如需本文所討論功能的詳細授權需求，請參閱 [Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory)。 

## <a name="configure-an-application-to-require-user-assignment"></a>設定應用程式來要求使用者指派

使用下列應用程式類型時，您可選擇要求使用者先指派給應用程式，才能進行存取：

- 設定了同盟單一登入 (SSO) 與 SAML 型驗證的應用程式
- 使用 Azure Active Directory 預先驗證的應用程式 Proxy 應用程式
- 建置於 Azure AD 應用程式平台上，而且在使用者或系統管理員同意該應用程式之後，使用 OAuth 2.0/OpenID Connect 驗證的應用程式。

需要使用者指派時，只有您明確指派給應用程式的使用者 (透過直接使用者指派或根據群組成員資格) 才能登入。 他們可以在其 [我的應用程式] 頁面上或使用直接連結存取應用程式。 

如果因為您已將此選項設為 [否]，或因為應用程式使用另一個 SSO 模式，而「不需要」指派，任何使用者只要具有應用程式的直接連結，或具有應用程式的 [屬性] 頁面中的 [使用者存取 URL]，都可以存取應用程式。 

此設定不會影響應用程式是否出現在 [我的應用程式] 存取面板上。 當您將使用者或群組指派給應用程式後，應用程式就會出現在使用者的 [我的應用程式] 存取面板上。 如需背景，請參閱[管理對應用程式的存取](what-is-access-management.md)。


若要對應用程式要求使用者指派：

1. 使用系統管理員帳戶或以應用程式擁有者的身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 **Azure Active Directory**。 在左側導覽功能表中，選取 [企業應用程式]。

3. 從清單中選取應用程式。 如果您看不到此應用程式，請在搜尋方塊中開始輸入其名稱。 或使用篩選控制項來選取應用程式類型、狀態或可見度，然後選取 [套用]。

4. 在左側導覽功能表中，選取 [屬性]。

5. 務必將 [需要使用者指派嗎?] 切換按鈕設為 [是]。

   > [!NOTE]
   > 如果無法使用 [需要使用者指派嗎?] 切換按鈕，您可使用 PowerShell 在服務主體上設定 appRoleAssignmentRequired 屬性。

6. 選取畫面頂端的 [儲存] 按鈕。

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>透過 Azure 入口網站將使用者或群組指派給應用程式

1. 使用全域管理員、應用程式管理員或雲端應用程式管理員帳戶，以企業應用程式的指派擁有者身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory**。 在左側導覽功能表中，選取 [企業應用程式]。
3. 從清單中選取應用程式。 如果您看不到此應用程式，請在搜尋方塊中開始輸入其名稱。 或使用篩選控制項來選取應用程式類型、狀態或可見度，然後選取 [套用]。
4. 在左側導覽功能表中，選取 [使用者和群組]。
   > [!NOTE]
   > 如果您想要將使用者指派給 Microsoft 應用程式 (例如 Office 365 應用程式)，其中一些應用程式會使用 PowerShell。 
5. 選取 [新增使用者] 按鈕。
6. 在 [新增指派] 頁面上，選取 [使用者和群組]。
7. 選取您要指派給應用程式的使用者或群組，或在搜尋方塊中開始輸入使用者或群組的名稱。 您可以選擇多個使用者和群組，而您的選取項目將會出現在 [選取的項目] 之下。
8. 完成時，按一下 [選取]。

   ![將使用者或群組指派給應用程式](./media/assign-user-or-group-access-portal/assign-users.png)

9. 在 [使用者和群組] 窗格上，從清單中選取一或多個使用者或群組，然後選擇窗格底部的 [選取] 按鈕。
10. 如果應用程式提供支援，您可將角色指派給使用者或群組。 在 [新增指派] 窗格上，選擇 [選取角色]。 然後，在 [選取角色] 窗格上，選擇要套用到所選使用者或群組的角色，然後選取窗格底部的 [確定]。 

    > [!NOTE]
    > 如果應用程式不支援角色選取，則會指派預設存取角色。 在此情況下，應用程式會管理使用者所擁有的存取層級。

2. 在 [新增指派] 窗格上，選取窗格底部的 [指派] 按鈕。

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>透過 PowerShell 將使用者或群組指派給應用程式

1. 開啟已提高權限的 Windows PowerShell 命令提示字元。

   > [!NOTE]
   > 您必須安裝 AzureAD 模組 (使用命令 `Install-Module -Name AzureAD`)。 如果系統提示您安裝 NuGet 模組或新的 Azure Active Directory V2 PowerShell 模組，請輸入 Y 然後按 ENTER。

1. 執行 `Connect-AzureAD` 並以全域管理員的使用者帳戶登入。
1. 您可以使用下列指令碼，將使用者和角色指派給應用程式：

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

如需有關如何將使用者指派給應用程式角色的詳細資訊，請參閱 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文件。

若要將群組指派給企業應用程式，您必須將 `Get-AzureADUser` 取代為 `Get-AzureADGroup` 並將 `New-AzureADUserAppRoleAssignment` 取代為 `New-AzureADGroupAppRoleAssignment`。

如需有關如何將群組指派給應用程式角色的詳細資訊，請參閱 [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0) 的文件。

### <a name="example"></a>範例

此範例會使用 PowerShell 將使用者 Britta Simon 指派至 [Microsoft 工作場所分析](https://products.office.com/business/workplace-analytics)應用程式。

1. 在 PowerShell 中，將對應值指派給變數 $username、$app_name 和 $app_role_name。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. 在此範例中，我們不清楚要指派給 Britta Simon 的應用程式角色是什麼名稱。 執行下列命令，以利用使用者 UPN 和服務主體顯示名稱來取得使用者 ($user) 和服務主體 ($sp)。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. 執行命令 `$sp.AppRoles`，顯示工作場所分析應用程式可用的角色。 在此範例中，我們要指派「分析師」(存取權有限) 角色給 Britta Simon。

   ![顯示使用者使用工作場所分析角色時可用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. 指派角色名稱給 `$app_role_name` 變數。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. 執行下列命令，將使用者指派給應用程式角色：

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>相關文章

- [深入了解終端使用者對應用程式的存取](end-user-experiences.md)
- [規劃 Azure AD 存取面板部署](access-panel-deployment-plan.md)
- [管理應用程式的存取](what-is-access-management.md)
 
## <a name="next-steps"></a>後續步驟

- [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
- [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
- [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
