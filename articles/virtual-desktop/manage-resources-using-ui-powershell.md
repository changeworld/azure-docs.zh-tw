---
title: 使用服務主體部署 Windows 虛擬桌面的管理工具 - Azure
description: 如何使用 PowerShell 部署 Windows 虛擬桌面的管理工具。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127788"
---
# <a name="deploy-a-management-tool-with-powershell"></a>使用 PowerShell 部署管理工具

本文將介紹如何使用 PowerShell 部署管理工具。

## <a name="important-considerations"></a>重要考量︰

每個 Azure 活動目錄 （Azure AD） 租戶的訂閱都需要單獨部署管理工具。 此工具不支援 Azure AD 企業對企業 （B2B） 方案。 

這個管理工具很簡單。 Microsoft 將提供重要安全性與品質更新。 [原始程式碼在 GitHub 中可用](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 無論您是客戶還是合作夥伴，我們鼓勵您定制該工具以滿足您的業務需求。

以下瀏覽器與管理工具相容：

- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本 (僅限 macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>部署管理工具需要具備的條件

部署管理工具之前，您需要 Azure Active Directory (Azure AD) 使用者建立應用程式註冊，並部署管理 UI。 此使用者必須：

- 擁有在您的 Azure 訂用帳戶中建立資源的權限
- 擁有建立 Azure AD 應用程式的權限。 遵循[必要權限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)中的指示，依下列步驟檢查您的使用者是否具有必要的權限。

為了成功部署和建構管理工具，您首先需要從[RDS 範本 GitHub 存儲庫](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)下載以下 PowerShell 腳本，並將其保存到本地電腦上的同一資料夾中。

  - 創建WvdMgmtUxApp註冊.ps1
  - 更新WvdMgmtUxApiUrl.ps1

部署及設定管理工具之後，建議您要求使用者啟動管理 UI，以確保可正常運作。 啟動管理 UI 的使用者必須具備角色指派，才能檢視或編輯 Windows 虛擬桌面租用戶。

## <a name="set-up-powershell"></a>設定 PowerShell

通過登錄到 Az 和 Azure AD PowerShell 模組開始。 以下是登錄方式：

1. 以管理員身份打開 PowerShell 並導航到保存 PowerShell 腳本的目錄。
2. 使用對計畫用於創建管理工具的 Azure 訂閱具有擁有者或參與者許可權的帳戶登錄到 Azure，通過運行以下 Cmdlet：

    ```powershell
    Login-AzAccount
    ```

3. 運行以下 Cmdlet 以使用與 Az PowerShell 模組相同的帳戶登錄到 Azure AD：

    ```powershell
    Connect-AzureAD
    ```

4. 之後，導航到從 RDS 範本 GitHub 存儲庫中保存兩個 PowerShell 腳本的資料夾。

保持用於登錄的 PowerShell 視窗處於打開狀態，以在登錄時運行其他 PowerShell Cmdlet。

## <a name="create-an-azure-active-directory-app-registration"></a>創建 Azure 活動目錄應用註冊

運行以下命令以創建具有所需 API 許可權的應用註冊：

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

完成 Azure AD 應用註冊後，可以部署管理工具。

## <a name="deploy-the-management-tool"></a>部署管理工具

運行以下 PowerShell 命令以部署管理工具並將其與您剛剛創建的服務主體相關聯：
     
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

創建 Web 應用後，必須將 URI 重定向到 Azure AD 應用程式才能成功登錄使用者。

## <a name="set-the-redirect-uri"></a>設置重定向 URI

運行以下 PowerShell 命令以檢索 Web 應用 URL 並將其設置為身份驗證重定向 URI（也稱為回復 URL）：

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

現在，您已經添加了重定向 URI，接下來需要更新 API URL，以便管理工具可以與 API 後端服務進行交互。

## <a name="update-the-api-url-for-the-web-application"></a>更新 Web 應用程式的 API URL

運行以下腳本以更新 Web 應用程式前端中的 API URL 配置：

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

現在，您已經完全配置了管理工具 Web 應用，是時候驗證 Azure AD 應用程式並提供同意了。

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>驗證 Azure AD 應用程式並提供同意

要驗證 Azure AD 應用程式佈建並提供同意：：

1. 打開 Internet 瀏覽器並使用管理帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 從 Azure 門戶頂部的搜索欄中，搜索**應用註冊**並選擇**服務**下的項。
3. 選擇**所有應用程式**，並在[創建 Azure 活動目錄應用註冊](#create-an-azure-active-directory-app-registration)中搜索為 PowerShell 腳本提供的唯一應用名稱。
4. 在瀏覽器左側的面板中，選擇 **"身份驗證**"並確保重定向 URI 與管理工具的 Web 應用 URL 相同，如下圖所示。
   
   [![具有輸入重定向 URI](media/management-ui-redirect-uri-inline.png)的身份驗證頁](media/management-ui-redirect-uri-expanded.png#lightbox)

5. 在左側面板中，選擇**API 許可權**以確認已添加許可權。 如果您是全域管理員，請選擇 **"授予管理員同意"`tenantname`** 按鈕，然後按照對話方塊提示為您的組織提供管理員同意。
    
    [![API 許可權頁](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

您現在可以開始使用管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

現在，您已隨時設置管理工具，您可以隨時隨地啟動它。 以下是啟動該工具的方式：

1. 在 Web 瀏覽器中打開 Web 應用的 URL。 如果您不記得 URL，則可以登錄到 Azure，查找為管理工具部署的應用服務，然後選擇 URL。
2. 使用您的 Windows 虛擬桌面認證登入。
   
   > [!NOTE]
   > 如果您在建構管理工具時未授予管理員同意，則登錄的每個使用者都需要提供自己的使用者同意才能使用該工具。

3. 當系統提示您選擇租戶組時，請從下拉清單中選擇**預設租戶組**。
4. 選取 [預設租用戶群組]**** 時，視窗的左側應該會出現一個功能表。 在此功能表上，尋找租用戶群組的名稱並選取。
   
   > [!NOTE]
   > 若您有自訂租用戶群組，請手動輸入名稱，不要從下拉式清單選擇。

## <a name="report-issues"></a>報告問題

如果管理工具或其他 Windows 虛擬桌面工具發生任何問題，請依照[遠端桌面服務的 Azure Resource Manager 範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的指示，在 GitHub 上回報問題。

## <a name="next-steps"></a>後續步驟

既然您已學到如何部署並連線到管理工具，您可以學習如何使用 Azure 服務健康狀態來監視服務問題與健康情況諮詢。 若要深入了解，請參閱我們的[設定服務警示教學課程](./set-up-service-alerts.md)。
