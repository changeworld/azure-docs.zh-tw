---
title: Azure 自動化中的認證資產
description: Azure 自動化中的認證資產包含可用來驗證由 Runbook 或 DSC 設定存取資源的安全性認證。 本文說明如何建立認證資產和在 Runbook 或 DSC 設定中使用它們。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252697"
---
# <a name="credential-assets-in-azure-automation"></a>Azure 自動化中的認證資產

自動化憑據資產包含一個物件，該物件包含安全憑據，如使用者名和密碼。 Runbook 和 DSC 設定可以使用可接受 PSCredential 物件進行驗證的 Cmdlet，否則可能會擷取 PSCredential 物件的使用者名稱和密碼，以對需要驗證的一些應用程式或服務提供。 認證的屬性會安全地儲存在 Azure 自動化中，並且可在 Runbook 或 DSC 設定中透過 [Get-AutomationPSCredential](#activities) 活動存取。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。

## <a name="azure-powershell-az-cmdlets"></a>Azure 電源殼 Az Cmdlet

對於 Azure PowerShell Az 模組，下表中的 Cmdlet 用於使用 Windows PowerShell 創建和管理自動化憑據資產。 它們作為[AzureAz.自動化模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)的一部分發貨，該模組可用於自動化運行簿和 DSC 配置。

| 指令程式 | 描述 |
|:--- |:--- |
| [獲取阿茲自動化證書](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |擷取認證資產的相關資訊。 這不會返回 PS憑據物件。  |
| [新自動化證書](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |建立新的自動化認證。 |
| [刪除-阿茲自動化憑據](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |移除自動化認證。 |
| [設置-阿茲自動化證書](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |設定現有自動化認證的屬性。 |

## <a name="activities"></a>活動

下表中的活動用來存取中 Runbook 和 DSC 設定的認證。

| 活動 | 描述 |
|:--- |:--- |
| Get-AutomationPSCredential |取得要在 Runbook 或 DSC 設定中使用的認證。 傳回 [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) 物件。 |

> [!NOTE]
> 您應該避免在 Get-AutomationPSCredential 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 或 DSC 設定與認證資產之間的相依性變得複雜。

## <a name="python2-functions"></a>Python2 函式

下表中的函式用於存取 Python2 Runbook 中的認證。

| 函式 | 描述 |
|:---|:---|
| automationassets.get_automation_credential | 擷取認證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入「automationassets」模組，才能存取資產函式。

## <a name="creating-a-new-credential-asset"></a>建立新的認證資產

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 入口網站建立新的認證資產

1. 從您的自動化帳戶中，選取 [共用資源]**** 下的 [認證]****。
1. 選取 [新增認證]****。
1. 完成表單並選擇 **"創建**"以保存新憑據。

> [!NOTE]
> 「Azure 自動化」中不支援使用多重要素驗證的使用者帳戶。

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立新的認證資產

下列範例命令顯示如何建立新的自動化認證。 PSCredential 物件是先建立了名稱和密碼，然後用來建立認證資產。 或者，您可以使用 **Get-Credential** Cmdlet 讓系統提示您輸入名稱和密碼。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>使用 PowerShell 認證

您會使用 **Get-AutomationPSCredential** 活動在 Runbook 或 DSC 設定中擷取認證資產。 這會傳回 [PSCredential 物件](/dotnet/api/system.management.automation.pscredential)，以便您搭配需要 PSCredential 參數的活動或 Cmdlet 使用。 您也可以擷取要個別使用的認證物件的屬性。 此物件具有使用者名稱和安全密碼的屬性，或您可使用 **GetNetworkCredential** 方法來傳回 [NetworkCredential](/dotnet/api/system.net.networkcredential) 物件，而該物件提供的密碼版本不安全。

> [!NOTE]
> **獲取阿茲自動化憑據**不會返回可用於身份驗證的**PS 憑據**。 它僅提供有關憑據的資訊。 如果需要在 Runbook 中使用憑據，則必須使用**獲取自動化PS憑據**來檢索**PS憑據**物件。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例命令顯示如何在 Runbook 中使用 PowerShell 認證。 在此範例中，會擷取認證及指派給變數的使用者名稱和密碼。

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您還可以使用憑據對 Azure 進行[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)。 在大多數情況下，您應該使用["運行即"帳戶](../manage-runas-account.md)，並使用[Get-AzAutomationConnection](../automation-connections.md)檢索它。

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的認證上按一下滑鼠右鍵，然後選取 [加入至畫布]****，即可將 **Get-AutomationPSCredential** 活動加入至圖形化 Runbook。

![加入認證至畫布](../media/credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。 在這種情況下，它用於為 Azure 資源提供 Runbook 的身份驗證，如使用 Azure [AD 使用者帳戶進行身份驗證 Runbook](../automation-create-aduser-account.md)中所述。 第一個活動會擷取可存取 Azure 訂用帳戶的認證。 然後 **，Connect-AzureRmAccount**活動使用此憑據為它之後的任何活動提供身份驗證。 因為 [Get-AutomationPSCredential](../automation-graphical-authoring-intro.md#links-and-workflow) 需要單一物件，因此推出了 **管線連結** 。  

![加入認證至畫布](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>在 DSC 中使用 PowerShell 認證

雖然 Azure 自動化中的 DSC 配置可以使用**Get-AutomationPSCredential**引用憑據資產，但憑據資產也可以通過參數（如果需要）傳入。 如需詳細資訊，請參閱 [編譯 Azure Automation DSC 中的設定](../automation-dsc-compile.md#credential-assets)。

## <a name="using-credentials-in-python2"></a>使用 Python2 中的認證

下列範例顯示存取 Python2 Runbook 中認證的範例。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化編寫中的連結，請參閱 [圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)
* 若要了解使用自動化的不同驗證方法，請參閱 [Azure 自動化安全性](../automation-security-overview.md)
* 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](../automation-first-runbook-graphical.md)
* 要開始使用 PowerShell 工作流運行簿，請參閱[我的第一個 PowerShell 工作流運行簿](../automation-first-runbook-textual.md)
* 若要開始使用 Python2 Runbook，請參閱[我的第一個 Python2 Runbook](../automation-first-runbook-textual-python2.md) 
