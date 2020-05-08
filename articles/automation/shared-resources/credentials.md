---
title: 管理 Azure 自動化中的認證
description: Azure 自動化中的認證資產包含可用來驗證由 Runbook 或 DSC 設定存取資源的安全性認證。 本文說明如何建立認證資產和在 Runbook 或 DSC 設定中使用它們。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16b92108bcb4e5185a1990b0ed8f1278bfe44921
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652824"
---
# <a name="manage-credentials-in-azure-automation"></a>管理 Azure 自動化中的認證

自動化認證資產會保存一個物件，其中包含安全性認證，例如使用者名稱和密碼。 Runbook 和 DSC 設定會使用接受[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)物件進行驗證的 Cmdlet。 或者，它們可以解壓縮`PSCredential`物件的使用者名稱和密碼，以提供給某些需要驗證的應用程式或服務。 

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會使用針對每個自動化帳戶產生的唯一金鑰，加密並儲存在 Azure 自動化中。 Azure 自動化會將金鑰儲存在系統管理的 Key Vault 中。 儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後使用它來加密資產。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>用來存取認證的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 建立和管理自動化認證。 它們會隨附于[Az 模組](modules.md#az-modules)中。

| Cmdlet | 描述 |
|:--- |:--- |
| [AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |抓取包含認證相關中繼資料的[CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps)物件。 Cmdlet 不會捕獲`PSCredential`物件本身。  |
| [新增-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |建立新的自動化認證。 |
| [移除-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |移除自動化認證。 |
| [設定-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |設定現有自動化認證的屬性。 |

## <a name="other-cmdlets-used-to-access-credentials"></a>用來存取認證的其他 Cmdlet

下表中的 Cmdlet 是用來存取 runbook 和 DSC 設定中的認證。 

| Cmdlet | 描述 |
|:--- |:--- |
| `Get-AutomationPSCredential` |取得要`PSCredential`在 RUNBOOK 或 DSC 設定中使用的物件。 最常見的情況是，您應該使用此[內部 Cmdlet](modules.md#internal-cmdlets) ，而不是`Get-AzAutomationCredential` Cmdlet，因為後者只會抓取認證資訊。 這種資訊通常無法傳遞給另一個 Cmdlet。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |取得具有使用者名稱和密碼提示的認證。 此 Cmdlet 是預設的 Microsoft. PowerShell. Security 模組的一部分。 請參閱[預設模組](modules.md#default-modules)。|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 建立認證資產。 此 Cmdlet 是預設 Azure 模組的一部分。 請參閱[預設模組](modules.md#default-modules)。|

若要`PSCredential`在程式碼中取出物件，您必須`Orchestrator.AssetManagement.Cmdlets`匯入模組。 如需詳細資訊，請參閱[管理 Azure 自動化中的模組](modules.md)。

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 您應該避免在的`Name`參數中使用變數`Get-AutomationPSCredential`。 其使用可能會在設計階段將 runbook 或 DSC 設定與認證資產之間的相依性探索變得複雜。

## <a name="python-2-functions-that-access-credentials"></a>存取認證的 Python 2 函式

下表中的函式是用來存取 Python 2 runbook 中的認證。

| 函式 | 說明 |
|:---|:---|
| `automationassets.get_automation_credential` | 擷取認證資產的相關資訊。 |

> [!NOTE]
> 在 Python `automationassets` runbook 的頂端匯入模組，以存取資產功能。

## <a name="create-a-new-credential-asset"></a>建立新的認證資產

您可以使用 Azure 入口網站或使用 Windows PowerShell 來建立新的認證資產。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 入口網站建立新的認證資產

1. 從您的自動化帳戶中，選取 [**共用資源**] 底下的 [**認證**]。
1. 選取 [新增認證]****。
2. 在 [新增認證] 窗格中，按照您的命名標準輸入適當的認證名稱。 
3. 在 [**使用者名稱**] 欄位中輸入您的存取識別碼。 
4. 在 [密碼] 欄位中，輸入您的密碼存取金鑰。

    ![建立新認證](../media/credentials/credential-create.png)

5. 如果已核取 [多重要素驗證] 方塊，請將其取消選取。 
6. 按一下 [**建立**] 以儲存新的認證資產。

> [!NOTE]
> Azure 自動化不支援使用多重要素驗證的使用者帳戶。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立新的認證資產

下列範例顯示如何建立新的自動化認證資產。 首先`PSCredential`會使用名稱和密碼建立物件，然後用來建立認證資產。 相反地，您可以使用`Get-Credential` Cmdlet 來提示使用者輸入名稱和密碼。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>取得認證資產

Runbook 或 DSC 設定會使用 internal `Get-AutomationPSCredential` Cmdlet 來抓取認證資產。 此 Cmdlet 會取得`PSCredential`可與需要認證的 Cmdlet 搭配使用的物件。 您也可以擷取要個別使用的認證物件的屬性。 物件具有使用者名稱和安全密碼的屬性。 

> [!NOTE]
> 此`Get-AzAutomationCredential` Cmdlet 不會抓取可`PSCredential`用於驗證的物件。 它只提供認證的相關資訊。 如果您需要在 runbook 中使用認證，則必須使用`PSCredential` `Get-AutomationPSCredential`將它當做物件加以取出。

或者，您可以使用[GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0)方法來抓取代表不安全版本之密碼的[NetworkCredential](/dotnet/api/system.net.networkcredential)物件。

### <a name="textual-runbook-example"></a>文字式 runbook 範例

下列範例顯示如何在 runbook 中使用 PowerShell 認證。 它會抓取認證，並將其使用者名稱和密碼指派給變數。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您也可以使用認證，透過[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)向 Azure 進行驗證。 在大部分的情況下，您應該使用「執行身分」[帳戶](../manage-runas-account.md)，並透過[AzAutomationConnection](../automation-connections.md)抓取連接。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>圖形化 runbook 範例

在圖形化編輯器的 [程式庫`Get-AutomationPSCredential` ] 窗格中，以滑鼠右鍵按一下 [認證]，然後選取 [**新增至畫布**]，即可將內部 Cmdlet 的活動新增至圖形化 runbook。

![加入認證至畫布](../media/credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。 在此情況下，認證會向 Azure 資源提供 runbook 的驗證，如[使用 Azure 自動化中的 Azure AD 來向 azure 進行驗證](../automation-use-azure-ad.md)中所述。 第一個活動會擷取可存取 Azure 訂用帳戶的認證。 帳戶連線活動接著會使用此認證來為其之後的任何活動提供驗證。 在這裡使用[管線連結](../automation-graphical-authoring-intro.md#links-and-workflow)，因為`Get-AutomationPSCredential`需要單一物件。  

![加入認證至畫布](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>在 DSC 設定中使用認證

雖然 Azure 自動化中的 DSC 設定可以搭配使用`Get-AutomationPSCredential`認證資產，但也可以透過參數傳遞認證資產。 如需詳細資訊，請參閱 [編譯 Azure Automation DSC 中的設定](../automation-dsc-compile.md#credential-assets)。

## <a name="use-credentials-in-a-python-2-runbook"></a>在 Python 2 runbook 中使用認證

下列範例顯示在 Python 2 runbook 中存取認證的範例。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解用來存取認證的 Cmdlet，請參閱[管理 Azure 自動化中的模組](modules.md)。
* 如需 runbook 的一般資訊，請參閱[Azure 自動化中的 runbook 執行](../automation-runbook-execution.md)。
* 如需 DSC 設定的詳細資訊，請參閱[狀態設定總覽](../automation-dsc-overview.md)。