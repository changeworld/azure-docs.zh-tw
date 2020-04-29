---
title: 使用 Azure 自動化管理 Office 365 服務
description: 告訴您如何使用 Azure 自動化來管理 Office 365 訂閱服務。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550418"
---
# <a name="manage-office-365-services-using-azure-automation"></a>使用 Azure 自動化管理 Office 365 服務

您可以使用 Azure 自動化來管理 Office 365 訂閱服務，例如 Microsoft Word 和 Microsoft Outlook 等產品。 與 Office 365 的互動是由[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)所啟用。 請參閱[使用 Azure 自動化中的 Azure AD 向 Azure 進行驗證](automation-use-azure-ad.md)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>Prerequisites

您需要下列各項，才能在 Azure 自動化中管理 Office 365 訂閱服務。

* Azure 訂用帳戶。 請參閱訂用帳戶[決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* Azure 中的自動化物件，用來保存使用者帳號憑證和 runbook。 請參閱[Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)。
* 。 請參閱[使用 Azure 自動化中的 Azure AD 向 Azure 進行驗證](automation-use-azure-ad.md)。
* 具有帳戶的 Office 365 租使用者。 請參閱[設定您的 Office 365 租使用者](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="installing-the-msonline-and-msonlineext-modules"></a>安裝 MSOnline 和 MSOnlineExt 模組

在 Azure 自動化中使用 Office 365 需要 Windows PowerShell （`MSOnline`模組）的 Microsoft Azure Active Directory。 您也需要模組[`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)，以簡化單一和多租使用者環境中的 Azure AD 管理。 如[使用 Azure 自動化中的 Azure AD 中所述，安裝模組以向 Azure 進行驗證](automation-use-azure-ad.md)。

>[!NOTE]
>若要使用 MSOnline PowerShell，您必須是 Azure AD 的成員。 來賓使用者無法使用模組。

## <a name="creating-an-azure-automation-account"></a>建立 Azure 自動化帳戶

若要完成本文中的步驟，您需要 Azure 自動化中的帳戶。 請參閱[建立 Azure 自動化帳戶](automation-quickstart-create-account.md)。
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>將 MSOnline 和 MSOnlineExt 新增為資產

現在新增已安裝的 MSOnline 和 MSOnlineExt 模組，以啟用 Office 365 功能。 請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)。

1. 在 [Azure 入口網站中，選取 [**自動化帳戶**]。
2. 選擇您的自動化帳戶。
3. 選取 [**共用資源**] 底下的 [**模組庫**]。
4. 搜尋 MSOnline。
5. 選取 PowerShell `MSOnline`模組，然後按一下 [匯**入**]，以資產形式匯入模組。
6. 重複步驟4和5，以找出`MSOnlineExt`並匯入模組。 

## <a name="creating-a-credential-asset-optional"></a>建立認證資產（選擇性）

針對具有執行腳本許可權的 Office 365 系統管理使用者，可以選擇是否要建立認證資產。 不過，它可以協助您在 PowerShell 腳本中避免公開使用者名稱和密碼。 如需指示，請參閱[建立認證資產](automation-use-azure-ad.md#creating-a-credential-asset)。

## <a name="creating-an-office-365-service-account"></a>建立 Office 365 服務帳戶

若要執行 Office 365 訂閱服務，您需要有許可權可執行所需動作的 Office 365 服務帳戶。 您可以使用一個全域系統管理員帳戶、每個服務一個帳戶，或有一個要執行的函式或腳本。 在任何情況下，服務帳戶都需要複雜且安全的密碼。 請參閱[設定商務用 Office 365](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)。 

## <a name="connecting-to-the-azure-ad-online-service"></a>連接到 Azure AD 線上服務

>[!NOTE]
>若要使用 MSOnline 模組 Cmdlet，您必須從 Windows PowerShell 執行它們。 PowerShell Core 不支援這些 Cmdlet。

您可以使用 MSOnline 模組，從 Office 365 訂閱連接到 Azure AD。 連接會使用 Office 365 使用者名稱和密碼，或使用多重要素驗證（MFA）。 您可以使用 Azure 入口網站或 Windows PowerShell 命令提示字元（不需要提高許可權）進行連接。

PowerShell 範例如下所示。 [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) Cmdlet 會提示您輸入認證，並將`Msolcred`它們儲存在變數中。 然後， [connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) Cmdlet 會使用認證來連線到 Azure 目錄線上服務。 如果您想要連接到特定的 Azure 環境，請使用`AzureEnvironment`參數。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果您未收到任何錯誤，則您已成功連線。 快速測試是執行 Office 365 Cmdlet （例如`Get-MsolUser`），並查看結果。 如果您收到錯誤，請注意，常見的問題是不正確的密碼。

>[!NOTE]
>您也可以使用 AzureRM 模組或 Az 模組，從 Office 365 訂閱連接到 Azure AD。 主要的連線 Cmdlet 是[Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 Cmdlet 支援特定`AzureEnvironmentName` Office 365 環境的參數。

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>從現有的腳本建立 PowerShell runbook

您可以從 PowerShell 腳本存取 Office 365 功能。 以下是名`Office-Credentials`為的認證的腳本範例，其使用者名稱為`admin@TenantOne.com`。 它會`Get-AutomationPSCredential`使用匯入 Office 365 認證。

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

## <a name="running-the-script-in-a-runbook"></a>在 runbook 中執行腳本

您可以在 Azure 自動化 runbook 中使用您的腳本。 例如，我們將使用 PowerShell runbook 類型。

1. 建立新的 PowerShell runbook。 請參閱[建立 Azure 自動化 runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)。
2. 從您的自動化帳戶，選取 [**進程自動化**] 底下的 [ **runbook** ]。
3. 選取新的 runbook，然後按一下 [**編輯**]。
4. 複製您的腳本，並將它貼到 runbook 的文字編輯器中。
5. 選取 [**資產**]，然後展開 [**認證**]，並確認 Office 365 認證是否存在。
6. 按一下 **[儲存]** 。
7. 選取 [**測試] 窗格**，然後按一下 [**啟動**] 開始測試您的 runbook。 請參閱[管理 Azure 自動化中的 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。
8. 測試完成時，請從 [測試] 窗格結束。

## <a name="publishing-and-scheduling-the-runbook"></a>發佈和排程 runbook

若要發佈並排程您的 runbook，請參閱[管理 Azure 自動化中的 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。

## <a name="next-steps"></a>後續步驟

* 您可以[在 Azure 自動化中的認證資產](shared-resources/credentials.md)中找到自動化認證資產的相關資訊。
* 請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)，以瞭解如何使用自動化模組。
* 如需 runbook 管理的總覽，請參閱[管理 Azure 自動化中的 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。
* 若要深入瞭解可在 Azure 自動化中用來啟動 runbook 的方法，請參閱[在 Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)。
* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。