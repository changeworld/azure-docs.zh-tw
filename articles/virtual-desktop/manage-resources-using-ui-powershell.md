---
title: 使用服務主體部署 Windows 虛擬桌面的管理工具-Azure
description: 如何使用 PowerShell 部署 Windows 虛擬桌面的管理工具。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127788"
---
# <a name="deploy-a-management-tool-with-powershell"></a>使用 PowerShell 部署管理工具

本文將說明如何使用 PowerShell 部署管理工具。

## <a name="important-considerations"></a>重要考量︰

每個 Azure Active Directory （Azure AD）租使用者的訂用帳戶都需要它自己個別部署的管理工具。 此工具不支援企業對企業（B2B）案例 Azure AD。 

這個管理工具很簡單。 Microsoft 將提供重要安全性與品質更新。 [原始程式碼可在 GitHub 中](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)取得。 無論您是客戶或合作夥伴，我們都建議您自訂工具以滿足您的商務需求。

下列瀏覽器與管理工具相容：

- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本 (僅限 macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>部署管理工具需要具備的條件

部署管理工具之前，您需要 Azure Active Directory (Azure AD) 使用者建立應用程式註冊，並部署管理 UI。 此使用者必須：

- 擁有在您的 Azure 訂用帳戶中建立資源的權限
- 擁有建立 Azure AD 應用程式的權限。 遵循[必要權限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)中的指示，依下列步驟檢查您的使用者是否具有必要的權限。

若要成功部署及設定管理工具，您必須先從[RDS 範本 GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)存放庫下載下列 PowerShell 腳本，並將其儲存至本機電腦上的相同資料夾。

  - createWvdMgmtUxAppRegistration. ps1
  - updateWvdMgmtUxApiUrl. ps1

部署及設定管理工具之後，建議您要求使用者啟動管理 UI，以確保可正常運作。 啟動管理 UI 的使用者必須具備角色指派，才能檢視或編輯 Windows 虛擬桌面租用戶。

## <a name="set-up-powershell"></a>設定 PowerShell

從登入 Az 和 Azure AD PowerShell 模組開始著手。 以下是登入的方式：

1. 以系統管理員身分開啟 PowerShell，然後流覽至您儲存 PowerShell 腳本的目錄。
2. 藉由執行下列 Cmdlet，在您打算用來建立管理工具的 Azure 訂用帳戶上，使用具有擁有者或參與者許可權的帳戶登入 Azure：

    ```powershell
    Login-AzAccount
    ```

3. 執行下列 Cmdlet，使用您用於 Az PowerShell 模組的相同帳戶來登入 Azure AD：

    ```powershell
    Connect-AzureAD
    ```

4. 之後，流覽至您從 RDS 範本 GitHub 存放庫儲存兩個 PowerShell 腳本的資料夾。

將您用來登入的 PowerShell 視窗保持開啟，以在登入時執行其他 PowerShell Cmdlet。

## <a name="create-an-azure-active-directory-app-registration"></a>建立 Azure Active Directory 應用程式註冊

執行下列命令，以必要的 API 許可權建立應用程式註冊：

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

既然您已完成 Azure AD 應用程式註冊，就可以部署管理工具。

## <a name="deploy-the-management-tool"></a>部署管理工具

執行下列 PowerShell 命令來部署管理工具，並將它與您剛建立的服務主體產生關聯：
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

建立 web 應用程式之後，您必須將重新導向 URI 新增至 Azure AD 應用程式，才能成功登入使用者。

## <a name="set-the-redirect-uri"></a>設定重新導向 URI

執行下列 PowerShell 命令來抓取 web 應用程式 URL，並將它設定為驗證重新導向 URI （也稱為回復 URL）：

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

既然您已新增重新導向 URI，接下來您必須更新 API URL，讓管理工具可以與 API 後端服務互動。

## <a name="update-the-api-url-for-the-web-application"></a>更新 web 應用程式的 API URL

執行下列腳本，以更新 web 應用程式前端中的 API URL 設定：

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

既然您已完全設定管理工具 web 應用程式，現在可以驗證 Azure AD 應用程式並提供同意。

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>驗證 Azure AD 應用程式並提供同意

若要確認 Azure AD 的應用程式設定並提供同意：

1. 開啟網際網路瀏覽器，並使用您的系統管理帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站頂端的搜尋列中，搜尋**應用程式註冊**並選取 [**服務**] 底下的專案。
3. 選取 [**所有應用程式**]，並搜尋您在[建立 Azure Active Directory 應用程式註冊](#create-an-azure-active-directory-app-registration)中為 PowerShell 腳本提供的唯一應用程式名稱。
4. 在瀏覽器左側的面板中，選取 [**驗證**]，並確認 [重新導向 URI] 與管理工具的 web 應用程式 URL 相同，如下圖所示。
   
   [![具有所輸入重新導向 URI](media/management-ui-redirect-uri-inline.png)的驗證頁面](media/management-ui-redirect-uri-expanded.png#lightbox)

5. 在左面板中，選取 [ **API 許可權**] 以確認已新增許可權。 如果您是全域系統管理員，請選取 [**授與系統`tenantname`管理員同意**] 按鈕，並遵循對話方塊提示，為您的組織提供系統管理員同意。
    
    [![[API 許可權]](media/management-ui-permissions-inline.png)頁面](media/management-ui-permissions-expanded.png#lightbox)

您現在可以開始使用管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

既然您已隨時設定管理工具，就可以隨時隨地啟動它。 以下是啟動工具的方法：

1. 在網頁瀏覽器中開啟 web 應用程式的 URL。 如果您不記得 URL，您可以登入 Azure，尋找您為管理工具部署的 app service，然後選取該 URL。
2. 使用您的 Windows 虛擬桌面認證登入。
   
   > [!NOTE]
   > 如果您在設定管理工具時未授與系統管理員同意，則每位登入的使用者都必須提供他們自己的使用者同意，才能使用此工具。

3. 當系統提示您選擇租使用者群組時，從下拉式清單中選取 [**預設租使用者群組**]。
4. 選取 [預設租用戶群組]**** 時，視窗的左側應該會出現一個功能表。 在此功能表上，尋找租用戶群組的名稱並選取。
   
   > [!NOTE]
   > 若您有自訂租用戶群組，請手動輸入名稱，不要從下拉式清單選擇。

## <a name="report-issues"></a>回報問題

如果管理工具或其他 Windows 虛擬桌面工具發生任何問題，請依照[遠端桌面服務的 Azure Resource Manager 範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的指示，在 GitHub 上回報問題。

## <a name="next-steps"></a>後續步驟

既然您已學到如何部署並連線到管理工具，您可以學習如何使用 Azure 服務健康狀態來監視服務問題與健康情況諮詢。 若要深入了解，請參閱我們的[設定服務警示教學課程](./set-up-service-alerts.md)。
