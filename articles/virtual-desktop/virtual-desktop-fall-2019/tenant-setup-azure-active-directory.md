---
title: 在 Windows 虛擬桌面中建立租用戶 - Azure
description: 說明如何設定 Azure Active Directory 中的 Windows 虛擬桌面租用戶。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b08e8e4d6623277d1935fc85e302e8ce3c88eea
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124392"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>教學課程：在 Windows 虛擬桌面中建立租用戶

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。

在 Windows 虛擬桌面中建立租用戶是建置桌面虛擬化解決方案的第一個步驟。 租用戶是包含一或多個主機集區的群組。 每個主機集區是由多個工作階段主機所組成，這些主機會在 Azure 中當作虛擬機器執行並向 Windows 虛擬桌面服務註冊。 每個主機集區也是由一或多個應用程式群組所組成，這些群組用來對使用者發佈遠端桌面和遠端應用程式資源。 透過租用戶，您可以建置主機集區、建立應用程式群組、指派給使用者，以及透過服務進行連線。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 Azure Active Directory 權限授與給 Windows 虛擬桌面服務。
> * 將 TenantCreator 應用程式角色指派給 Azure Active Directory 租用戶中的使用者。
> * 建立 Windows 虛擬桌面租用戶。

## <a name="what-you-need-to-set-up-a-tenant"></a>要設定租用戶所需具備的項目

在開始設定 Windows 虛擬桌面租用戶之前，請確定您有下列項目：

* Windows 虛擬桌面使用者的 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 租用戶識別碼。
* Azure Active Directory 租用戶中的全域管理員帳戶。
   * 這也適用於為其客戶建立 Windows 虛擬桌面租用戶的雲端解決方案提供者 (CSP) 組織。 如果您在 CSP 組織中，則必須能夠以客戶的 Azure Active Directory 執行個體全域管理員身分登入。
   * 系統管理員帳戶必須來自要在其中建立 Windows 虛擬桌面租用戶的 Azure Active Directory 租用戶。 此程序不支援 Azure Active Directory B2B (來賓) 帳戶。
   * 系統管理員帳戶必須是公司或學校帳戶。
* Azure 訂用帳戶。

您必須已備妥租用戶識別碼、全域管理員帳戶和 Azure 訂用帳戶，才能讓教學課程所述的程序正常運作。

## <a name="grant-permissions-to-windows-virtual-desktop"></a>授與權限給 Windows 虛擬桌面

如果您已經將此 Azure Active Directory 執行個體的權限授與 Windows 虛擬桌面，請略過本節。

將權限授與 Windows 虛擬桌面服務，可讓它查詢 Azure Active Directory 以便進行系統管理和使用者工作。

若要授與服務權限：

1. 開啟瀏覽器，並開始進行 [Windows 虛擬桌面伺服器應用程式](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)的管理員同意流程。
   > [!NOTE]
   > 如果您管理客戶，而需要為客戶的目錄授與管理員同意，請在瀏覽器中輸入下列 URL，並將 {tenant} 取代為客戶的 Azure AD 網域名稱。 例如，如果客戶的組織已註冊 contoso.onmicrosoft.com 的 Azure AD 網域名稱，請將 {tenant} 取代為 contoso.onmicrosoft.com。
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. 使用全域管理員帳戶登入 Windows 虛擬桌面同意頁面。 例如，如果您屬於 Contoso 組織，您的帳戶可能是 admin@contoso.com 或 admin@contoso.onmicrosoft.com。
3. 選取 [接受]。
4. 請稍後片刻，讓 Azure AD 可記錄同意。
5. 開啟瀏覽器，並開始進行 [Windows 虛擬桌面用戶端應用程式](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)的管理員同意流程。
   >[!NOTE]
   > 如果您管理客戶，而需要為客戶的目錄授與管理員同意，請在瀏覽器中輸入下列 URL，並將 {tenant} 取代為客戶的 Azure AD 網域名稱。 例如，如果客戶的組織已註冊 contoso.onmicrosoft.com 的 Azure AD 網域名稱，請將 {tenant} 取代為 contoso.onmicrosoft.com。
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 如同步驟 2 一樣，以全域管理員身分登入 Windows 虛擬桌面同意頁面。
7. 選取 [接受]。

## <a name="assign-the-tenantcreator-application-role"></a>指派 TenantCreator 應用程式角色

將 TenantCreator 應用程式角色指派給 Azure Active Directory 使用者，可讓該使用者建立與 Azure Active Directory 執行個體相關聯的 Windows 虛擬桌面租用戶。 您必須使用全域管理員帳戶來指派 TenantCreator 角色。

若要指派 TenantCreator 應用程式角色：

1. 移至 [Azure 入口網站](https://portal.azure.com)以管理 TenantCreator 應用程式角色。 搜尋並選取 [企業應用程式]。 如果您正在處理多個 Azure Active Directory 租用戶，最佳做法是開啟私人瀏覽器工作階段，然後將 URL 複製並貼入位址列中。

   ![在 Azure 入口網站中搜尋企業應用程式的螢幕擷取畫面](../media/azure-portal-enterprise-applications.png)
2. 在 [企業應用程式] 中，搜尋 [Windows 虛擬桌面]。 您會看到您在上一節中同意的兩個應用程式。 在這兩個應用程式中，選取 [Windows 虛擬桌面]。
   
   ![在「企業應用程式」中搜尋「Windows 虛擬桌面」時的搜尋結果螢幕擷取畫面。 已醒目提示名為「Windows 虛擬桌面」的應用程式。](../media/tenant-enterprise-app.png)
3. 選取 [使用者和群組]。 您可能會看到對應用程式授與同意權的系統管理員已列出，並且已獲派**預設存取**角色。 這還不足以建立 Windows 虛擬桌面租用戶。 請繼續遵循這些指示來對使用者新增 **TenantCreator** 角色。
   
   ![指派來管理「Windows 虛擬桌面」企業應用程式之使用者和群組的螢幕擷取畫面。 螢幕擷取畫面顯示只有一個指派，其適用於「預設存取」。](../media/tenant-default-access.png)
4. 選取 [新增使用者]，然後在 [新增指派] 索引標籤中選取 [使用者和群組]。
5. 搜尋您將建立 Windows 虛擬桌面租用戶的使用者帳戶。 為了簡單起見，這可以是全域管理員帳戶。
   - 如果您使用的是 Microsoft 身分識別提供者，例如，contosoadmin@live.com 或 contosoadmin@outlook.com，您可能無法登入 Windows 虛擬桌面。 我們建議使用類似 admin@contoso.com 或 admin@contoso.onmicrosoft.com 的網域特定帳戶。

   ![選取要新增為 "TenantCreator" 的使用者螢幕擷取畫面。](../media/tenant-assign-user.png)
   > [!NOTE]
   > 您必須選取來自此 Azure Active Directory 執行個體的使用者 (或包含使用者的群組)。 您無法選擇來賓 (B2B) 使用者或服務主體。

6. 選取使用者帳戶，選擇 [選取] 按鈕，然後選取 [指派]。
7. 在 [Windows 虛擬桌面 - 使用者和群組] 頁面上，確認您有看到新項目，其中 **TenantCreator** 角色已指派給將要建立 Windows 虛擬桌面租用戶的使用者。

   ![指派來管理「Windows 虛擬桌面」企業應用程式之使用者和群組的螢幕擷取畫面。 螢幕擷取畫面現在包含使用者獲派 "TenantCreator" 角色的第二個項目。](../media/tenant-tenant-creator-added.png)

在繼續建立您的 Windows 虛擬桌面租用戶之前，您需要兩項資訊：

   - 您的 Azure Active Directory 租用戶識別碼 (或**目錄識別碼**)
   - 您的 Azure 訂用帳戶 ID

若要尋找您的 Azure Active Directory 租用戶識別碼 (或**目錄識別碼**)：
1. 在相同的 [Azure 入口網站](https://portal.azure.com)工作階段中，搜尋並選取 [Azure Active Directory]。

   ![在 Azure 入口網站中搜尋 "Azure Active Directory" 之結果的螢幕擷取畫面。 [服務] 底下的搜尋結果已醒目提示。](../media/tenant-search-azure-active-directory.png)
2. 向下捲動，直到您找到**屬性**，然後將其選取。
3. 尋找**目錄識別碼**，然後選取剪貼簿圖示。 將其貼在方便取得的位置，以便稍後用來作為 **AadTenantId** 值。

   ![Azure Active Directory 屬性的螢幕擷取畫面。 滑鼠停留在 [目錄識別碼] 的剪貼簿圖示上，以便複製及貼上。](../media/tenant-directory-id.png)

若要尋找您的 Azure 訂用帳戶識別碼：
1. 在相同的 [Azure 入口網站](https://portal.azure.com)工作階段中，搜尋並選取 [訂用帳戶]。
   
   ![在 Azure 入口網站中搜尋 "Azure Active Directory" 之結果的螢幕擷取畫面。 [服務] 底下的搜尋結果已醒目提示。](../media/tenant-search-subscription.png)
2. 選取您想要用來接收 Windows 虛擬桌面服務通知的 Azure 訂用帳戶。
3. 尋找**訂用帳戶識別碼**，然後將滑鼠移到該值上，直到剪貼簿圖示出現。 選取 [剪貼簿] 圖示，然後將其貼在方便取得的位置，以便稍後用來作為 **AzureSubscriptionId** 值。
   
   ![Azure 訂用帳戶屬性的螢幕擷取畫面。 滑鼠停留在 [訂用帳戶識別碼] 的剪貼簿圖示上，以便複製及貼上。](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>建立 Windows 虛擬桌面租用戶

既然您已授與 Windows 虛擬桌面服務查詢 Azure Active Directory 的權限，並將 TenantCreator 角色指派給使用者帳戶，您可以建立 Windows 虛擬桌面租用戶。

首先，[下載並匯入 Windows 虛擬桌面的模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

透過此 Cmdlet，使用 TenantCreator 使用者帳戶登入 Windows 虛擬桌面：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

然後，建立與 Azure Active Directory 租用戶相關聯的新 Windows 虛擬桌面租用戶：

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

以貴組織和租用戶相關的值取代以方括號括住的值。 您選擇用於新 Windows 虛擬桌面租用戶的名稱應該要是全域唯一的。 例如，假設您是 Contoso 組織的 Windows 虛擬桌面 TenantCreator。 您會執行的 Cmdlet 如下所示：

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

您最好將系統管理存取權指派給第二名使用者，以免您遭到鎖定而無法進入自己的帳戶，或因為休假而需要讓某人在您不在時擔任租用戶的管理員。 若要指派管理員存取權給第二名使用者，請使用 `<TenantName>` 和 `<Upn>` (請將這兩項取代為您的租用戶名稱和第二名使用者的 UPN) 來執行下列 Cmdlet。

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>後續步驟

如需更深入的逐步解說，請參閱我們的 [Windows 虛擬桌面學習路徑](https://docs.microsoft.com/learn/paths/m365-wvd/)。

建立好您的租用戶後，您必須在 Azure Active Directory 中建立服務主體，並對其指派 Windows 虛擬桌面內的角色。 服務主體可讓您順利部署 Windows 虛擬桌面的 Azure Marketplace 供應項目，進而建立主機集區。 若要深入了解主機集區，請繼續進行在 Windows 虛擬桌面中建立主機集區的教學課程。

> [!div class="nextstepaction"]
> [使用 PowerShell 建立服務主體和角色指派](create-service-principal-role-powershell.md)
