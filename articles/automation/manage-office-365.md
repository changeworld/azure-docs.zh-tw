---
title: 使用 Azure 自動化管理 Office 365 服務
description: 本文說明如何使用 Azure 自動化來管理 Office 365 訂閱服務。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 322e2a3679ed29ab9ecc4cdc3c6e1fe4d0f20276
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831157"
---
# <a name="manage-office-365-services"></a>管理 Office 365 服務

您可以使用 Azure 自動化來管理 Office 365 訂閱服務，例如 Microsoft Word 和 Microsoft Outlook 等產品。 與 Office 365 的互動是由 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 啟用。 請參閱[在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證](automation-use-azure-ad.md)。

## <a name="prerequisites"></a>Prerequisites

您需要下列各項，才能在 Azure 自動化中管理 Office 365 訂閱服務。

* Azure 訂用帳戶。 請參閱[訂用帳戶決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* Azure 中的自動化物件，用來保存使用者帳戶認證和 Runbook。 請參閱 [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)。
* Azure AD。 請參閱[在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證](automation-use-azure-ad.md)。
* 具有帳戶的 Office 365 租用戶。 請參閱[設定您的 Office 365 租用戶](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="install-the-msonline-and-msonlineext-modules"></a>安裝 MSOnline 和 MSOnlineExt 模組

在 Azure 自動化中使用 Office 365 需要適用於 Windows PowerShell 的 Microsoft Azure Active Directory (`MSOnline` 模組)。 您也需要模組 [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)，這可簡化單一和多租用戶環境中的 Azure AD 管理。 如[在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證](automation-use-azure-ad.md)中所述安裝模組。

>[!NOTE]
>若要使用 MSOnline PowerShell，您必須是 Azure AD 的成員。 來賓使用者無法使用模組。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

若要完成本文中的步驟，您需要 Azure 自動化的帳戶。 請參閱[建立 Azure 自動化帳戶](automation-quickstart-create-account.md)。
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>將 MSOnline 和 MSOnlineExt 新增為資產

現在新增已安裝的 MSOnline 和 MSOnlineExt 模組，以啟用 Office 365 功能。 請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。

1. 在 Azure 入口網站中，選取 [自動化帳戶]。
2. 選擇您的自動化帳戶。
3. 選取 [共用資源] 底下的 [模組庫]。
4. 搜尋 MSOnline。
5. 選取 `MSOnline` PowerShell 模組，然後按一下 [匯入] 以匯入模組作為資產。
6. 重複步驟 4 和 5，以找出 `MSOnlineExt` 模組並匯入。 

## <a name="create-a-credential-asset-optional"></a>建立認證資產 (選擇性)

您可以選擇是否要針對具有執行指令碼權限的 Office 365 系統管理使用者，建立認證資產。 不過，這有助於保護 PowerShell 指令碼中的使用者名稱和密碼免於公開。 如需相關指示，請參閱[建立認證資產](automation-use-azure-ad.md#create-a-credential-asset)。

## <a name="create-an-office-365-service-account"></a>建立 Office 365 服務帳戶

若要執行 Office 365 訂閱服務，您需要具備權限可執行所需動作的 Office 365 服務帳戶。 您可以使用一個全域管理員帳戶、每個服務一個帳戶，或有一個要執行的函式或指令碼。 在任何情況下，服務帳戶都需要複雜且安全的密碼。 請參閱[設定商務用 Office 365](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)。 

## <a name="connect-to-the-azure-ad-online-service"></a>連線到 Azure AD 線上服務

>[!NOTE]
>若要使用 MSOnline 模組 Cmdlet，您必須從 Windows PowerShell 加以執行。 PowerShell Core 不支援這些 Cmdlet。

您可以使用 MSOnline 模組，從 Office 365 訂閱連線到 Azure AD。 連線會使用 Office 365 使用者名稱和密碼，或使用多重要素驗證 (MFA)。 您可以使用 Azure 入口網站或 Windows PowerShell 命令提示字元 (不需要提升權限) 進行連線。

PowerShell 範例顯示如下。 [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) Cmdlet 會提示您輸入認證，並將其儲存在 `Msolcred` 變數中。 然後，[Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) Cmdlet 會使用認證來連線到 Azure Directory 線上服務。 如果您想要連線到特定 Azure 環境，請使用 `AzureEnvironment` 參數。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果您未收到任何錯誤，則您已成功連線。 快速測試是執行 Office 365 Cmdlet，例如 `Get-MsolUser`，並查看結果。 如果您收到錯誤，請注意，常見的問題是密碼不正確。

>[!NOTE]
>您也可以使用 AzureRM 模組或 Az 模組，從 Office 365 訂閱連線到 Azure AD。 主要的連線 Cmdlet 是 [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 Cmdlet 支援特定 Office 365 環境的 `AzureEnvironmentName` 參數。

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>從現有的指令碼建立 PowerShell Runbook

您可以從 PowerShell 指令碼存取 Office 365 功能。 以下是名為 `Office-Credentials` 的認證 (使用者名稱為 `admin@TenantOne.com`) 的指令碼範例。 會使用 `Get-AutomationPSCredential` 來匯入 Office 365 認證。

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

## <a name="run-the-script-in-a-runbook"></a>在 Runbook 中執行指令碼

您可以在 Azure 自動化 Runbook 中使用您的指令碼。 例如，我們將使用 PowerShell Runbook 類型。

1. 建立新的 PowerShell Runbook。 請參閱[建立 Azure 自動化 Runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)。
2. 從您的自動化帳戶中，選取 [程序自動化] 下的 [Runbook]。
3. 選取新的 Runbook，然後按一下 [編輯]。
4. 複製您的指令碼，並將其貼到 Runbook 的文字編輯器中。
5. 選取 [資產]，然後展開 [認證]，並確認 Office 365 認證存在。
6. 按一下 [檔案] 。
7. 選取 [測試] 窗格，然後按一下 [開始] 開始測試您的 Runbook。 請參閱[管理 Azure 自動化中的 Runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。
8. 測試完成時，請從 [測試] 窗格結束。

## <a name="publish-and-schedule-the-runbook"></a>發佈和排程 Runbook

若要發佈然後排程您的 Runbook，請參閱[管理 Azure 自動化中的 Runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。

## <a name="next-steps"></a>後續步驟

* 如需認證使用的詳細資訊，請參閱[管理 Azure 自動化中的認證](shared-resources/credentials.md)。
* 如需模組的詳細資訊，請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。
* 如果您需要啟動 Runbook，請參閱[在 Azure 自動化中啟動 Runbook](start-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。