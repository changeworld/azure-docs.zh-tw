---
title: 將使用者或組分配給 Azure AD 中的企業應用
description: 如何在 Azure Active Directory 中選取企業應用程式以將使用者或群組指派給它
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409187"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者或群組指派給企業應用程式

本文介紹如何從 Azure 門戶或使用 PowerShell 將使用者或組分配給 Azure 活動目錄 （Azure AD） 中的企業應用程式。 將使用者分配給應用程式時，應用程式將顯示在使用者的["我的應用"訪問面板](https://myapps.microsoft.com/)中，以便輕鬆訪問。 如果應用程式公開角色，您還可以向使用者分配特定角色。

為了更好地控制，某些類型的企業應用程式可以配置為[需要使用者分配](#configure-an-application-to-require-user-assignment)。 

要[將使用者或組分配給企業應用](#assign-users-or-groups-to-an-app-via-the-azure-portal)，您需要以全域管理員、應用程式管理員、雲應用程式管理員或企業應用的分配擁有者身份登錄。

> [!NOTE]
> 基於組的分配需要 Azure 活動目錄高級 P1 或 P2 版本。 僅支援安全性群組支援基於組的分配。 當前不支援嵌套組成員身份和 Office 365 組。 有關本文中討論的功能的更多許可要求，請參閱[Azure 活動目錄定價頁](https://azure.microsoft.com/pricing/details/active-directory)。 

## <a name="configure-an-application-to-require-user-assignment"></a>將應用程式佈建為需要使用者分配

對於以下類型的應用程式，您可以選擇要求使用者在使用者訪問應用程式之前將其分配到該應用程式：

- 為基於 SAML 的身份驗證為聯合單一登入 （SSO） 配置的應用程式
- 使用 Azure 活動目錄預身份驗證的應用程式代理應用程式
- 在 Azure AD 應用程式平臺上構建的應用程式，在使用者或管理員同意該應用程式後使用 OAuth 2.0 / OpenID 連接身份驗證。

當需要使用者分配時，只有您顯式分配給應用程式的使用者才能登錄。 他們可以訪問其"我的應用"頁面上的應用或使用直接連結。 

當分配*不需要*時，由於您將此選項設置為 **"否**"，或者由於應用程式使用另一個 SSO 模式，如果任何使用者具有指向應用程式的直接連結或應用程式**屬性**頁中的**使用者訪問 URL，** 則任何使用者都可以訪問該應用程式。 

此設置不會影響應用程式是否顯示在"我的應用訪問"面板上。 將使用者或組分配給應用程式後，應用程式將顯示在使用者的"我的應用"訪問面板上。 有關背景，請參閱[管理對應用的訪問](what-is-access-management.md)。


要要求應用程式的使用者分配：

1. 使用管理員帳戶或應用程式擁有者登錄到[Azure 門戶](https://portal.azure.com)。

2. 選擇**Azure 活動目錄**。 在左側導航功能表中，選擇**企業應用程式**。

3. 從清單中選擇應用程式。 如果看不到應用程式，請開始在搜索框中鍵入其名稱。 或者使用篩選器控制項選擇應用程式類型、狀態或可見度，然後選擇 **"應用**"。

4. 在左側導航功能表中，選擇 **"屬性**"。

5. 確保**所需的使用者分配？** 切換設置為 **"是**"。

   > [!NOTE]
   > 如果 **"需要使用者分配？"** 切換不可用，則可以使用 PowerShell 在服務主體上設置 AppRoleAssignment 需要屬性。

6. 選擇螢幕頂部的 **"保存**"按鈕。

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>通過 Azure 門戶將使用者或組分配給應用

1. 使用全域管理員、應用程式管理員或雲應用程式管理員帳戶或作為企業應用的分配擁有者登錄到[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 活動目錄**。 在左側導航功能表中，選擇**企業應用程式**。
3. 從清單中選擇應用程式。 如果看不到應用程式，請開始在搜索框中鍵入其名稱。 或者使用篩選器控制項選擇應用程式類型、狀態或可見度，然後選擇 **"應用**"。
4. 在左側導航功能表中，選擇 **"使用者"和"組**"。
   > [!NOTE]
   > 如果要將使用者分配給 Microsoft 應用程式（如 Office 365 應用），其中一些應用將使用 PowerShell。 
5. 選擇"**添加使用者"** 按鈕。
6. 在"**添加分配"** 窗格中，選擇 **"使用者"和"組**"。
7. 選擇要分配給應用程式的使用者或組，或在搜索框中開始鍵入使用者或組的名稱。 您可以選擇多個使用者和組，您的選擇將顯示在 **"選定專案**"下。
8. 完成後，按一下 **"選擇**"。

   ![將使用者或群組指派給應用程式](./media/assign-user-or-group-access-portal/assign-users.png)

9. 在"**使用者和組**"窗格中，從清單中選擇一個或多個使用者或組，然後選擇窗格底部的 **"選擇**"按鈕。
10. 如果應用程式支援它，則可以為使用者或組分配角色。 在"**添加分配"** 窗格中，**選擇"選擇角色**"。 然後，在 **"選擇角色"** 窗格中，選擇要應用於所選使用者或組的角色，然後在窗格底部選擇 **"確定**"。 

    > [!NOTE]
    > 如果應用程式不支援角色選擇，則分配預設訪問角色。 在這種情況下，應用程式管理使用者具有的存取層級。

2. 在"**添加分配"** 窗格中，選擇窗格底部的 **"分配**"按鈕。

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>通過 PowerShell 將使用者或組分配給應用

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

如需有關如何將使用者指派給應用程式角色的詳細資訊，請瀏覽 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文件

若要將群組指派給企業應用程式，您必須將 `Get-AzureADUser` 取代為 `Get-AzureADGroup`。

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

   ![使用工作區分析角色顯示可供使用者使用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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

- [瞭解有關最終使用者訪問應用程式的更多資訊](end-user-experiences.md)
- [規劃 Azure AD 訪問面板部署](access-panel-deployment-plan.md)
- [管理對應用的訪問](what-is-access-management.md)
 
## <a name="next-steps"></a>後續步驟

- [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
- [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
- [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
