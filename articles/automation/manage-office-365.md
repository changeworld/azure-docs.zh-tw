---
title: 使用 Azure 自動化管理 Office 365 服務
description: 介紹如何使用 Azure 自動化來管理 Office 365 訂閱服務。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550418"
---
# <a name="manage-office-365-services-using-azure-automation"></a>使用 Azure 自動化管理 Office 365 服務

對於 Microsoft Word 和 Microsoft Outlook 等產品,可以使用 Azure 自動化管理 Office 365 訂閱服務。 與 Office 365 的互動由[Azure 活動目錄 (Azure AD) 啟用](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 請參考[Azure 自動化中使用 Azure AD 以對 Azure 進行身分驗證](automation-use-azure-ad.md)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

在 Azure 自動化中管理 Office 365 訂閱服務需要以下操作。

* Azure 訂用帳戶。 請參考[訂閱決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* Azure 中用於保存使用者帳戶憑據和 Runbook 的自動化物件。 請參考[Azure 自動化 簡介](https://docs.microsoft.com/azure/automation/automation-intro)。
* 。 請參考[Azure 自動化中使用 Azure AD 以對 Azure 進行身分驗證](automation-use-azure-ad.md)。
* 具有帳戶的 Office 365 租戶。 請參閱[設定 Office 365 租戶](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="installing-the-msonline-and-msonlineext-modules"></a>安裝 MSOnline 與 MSOnlineExt 模組

在 Azure 自動化中使用 Office 365 需要為`MSOnline`Windows PowerShell( 模組)使用 Microsoft Azure 活動目錄。 您還需要該模組,該模組[`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)簡化了單租戶和多租戶環境中的 Azure AD 管理。 安裝 Azure[自動化中使用 Azure AD 中描述的模組,以便對 Azure 進行身分驗證](automation-use-azure-ad.md)。

>[!NOTE]
>要使用 MSOnline PowerShell,您必須是 Azure AD 的成員。 來賓用戶無法使用該模組。

## <a name="creating-an-azure-automation-account"></a>建立 Azure 自動化帳戶

要完成本文中的步驟,需要在 Azure 自動化中建立一個帳戶。 請參考[Azure 自動化帳號](automation-quickstart-create-account.md)。
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>將 MSOnline 與 MSOnlineExt 新增為資產

現在添加已安裝的 MSOnline 和 MSOnlineExt 模組,以啟用 Office 365 功能。 請參考[Azure 自動化中的管理模組](shared-resources/modules.md)。

1. 在 Azure 門戶中,選擇 **「自動化帳戶**」。。
2. 選擇您的自動化帳戶。
3. 在**分享資源**下選擇**模組庫**。
4. 搜索MSOnline。
5. 選擇`MSOnline`PowerShell 模組,然後單擊 **「導入**」以將模組導入為資產。
6. 重複步驟 4 和`MSOnlineExt`5 以 定位和導入模組。 

## <a name="creating-a-credential-asset-optional"></a>建立認證資產(可選)

為具有運行腳本許可權的 Office 365 管理用戶創建憑據資產是可選的。 但是,它可以説明防止在 PowerShell 腳本中公開使用者名和密碼。 有關說明,請參閱[創建憑據資產](automation-use-azure-ad.md#creating-a-credential-asset)。

## <a name="creating-an-office-365-service-account"></a>新增 Office 365 服務帳戶

要運行 Office 365 訂閱服務,您需要一個具有許可權的 Office 365 服務帳戶來執行所需的操作。 您可以使用一個全域管理員帳戶,每個服務一個帳戶,或者要執行一個函數或腳本。 在任何情況下,服務帳戶都需要複雜和安全的密碼。 有關[業務,請參閱設置 Office 365。](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide) 

## <a name="connecting-to-the-azure-ad-online-service"></a>連接到 Azure AD 連線服務

>[!NOTE]
>要使用 MSOnline 模組 cmdlet,必須從 Windows PowerShell 運行它們。 PowerShell 核心不支援這些 cmdlet。

您可以使用 MSOnline 模組從 Office 365 訂閱連接到 Azure AD。 連接使用 Office 365 使用者名和密碼或使用多重身份驗證 (MFA)。 您可以使用 Azure 門戶或 Windows PowerShell 命令提示符進行連接(不必提升)。

PowerShell 示例如下所示。 [取得認證的](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)cmdlet 提示認證, 並將儲存在變數`Msolcred`中 。 然後,[連接-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet 使用認證到 Azure 目錄連線服務。 如果要連線到特定的 Azure 環境,請`AzureEnvironment`使用 參數 。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果未收到任何錯誤,則已成功連接。 例如,快速測試是運行 Office 365 cmdlet,`Get-MsolUser`並查看結果。 如果收到錯誤,請注意,常見問題是密碼不正確。

>[!NOTE]
>您還可以使用 AzureRM 模組或 Az 模組從 Office 365 訂閱連接到 Azure AD。 主連線 cmdlet 是[連線 AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 cmdlet`AzureEnvironmentName`支援特定 Office 365 環境的參數。

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>從現有文稿建立 PowerShell 執行簿

您從 PowerShell 腳本訪問 Office 365 功能。 下面是名為`Office-Credentials`的 憑`admin@TenantOne.com`據的 腳本示例, 它用於`Get-AutomationPSCredential`導入 Office 365 認證。

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>在 Runbook 中執行文稿

您可以在 Azure 自動化手冊中使用腳本。 例如,我們將使用 PowerShell 運行簿類型。

1. 創建新的 PowerShell 執行簿。 請參考[Azure 自動化執行簿](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)。
2. 從您的自動化帳戶中,在 **「流程自動化**」下選擇**Runbook。**
3. 選擇新的 Runbook,然後單擊 **「編輯**」。
4. 複製文稿並將其貼上到 Runbook 文字編輯器中。
5. 選擇**ASSETS,** 然後展開**認證**Office 365 認證是否在那裡。
6. 按一下 [儲存]****。
7. 選擇 **「測試」窗格**,然後按下「**開始」** 開始測試 Runbook。 請參閱[在 Azure 自動化中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)
8. 測試完成後,退出"測試"窗格。

## <a name="publishing-and-scheduling-the-runbook"></a>發佈和調度 Runbook

要發佈並安排 Runbook,請參閱[在 Azure 自動化 中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)

## <a name="next-steps"></a>後續步驟

* 您可以在[Azure 自動化 中的認證資產中找到](shared-resources/credentials.md)有關自動化認證資產的資訊。
* 請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md),瞭解如何使用自動化模組。
* 有關 Runbook 管理的概述,請參閱[在 Azure 自動化 中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)
* 要瞭解有關可用於在 Azure 自動化中啟動 Runbook 的方法,請參閱[在 Azure 自動化 中啟動 Runbook。](automation-starting-a-runbook.md)
* 有關 PowerShell 的詳細資訊(包括語言參考和學習模組),請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。