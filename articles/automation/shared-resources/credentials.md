---
title: 管理 Azure 自動化中的認證
description: 本文說明如何建立認證資產及在 Runbook 或 DSC 設定中加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 540ae25b22b2c134a47f91ad5b8b19089c7f2acb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745004"
---
# <a name="manage-credentials-in-azure-automation"></a>管理 Azure 自動化中的認證

自動化認證資產會保存物件，其中包含安全性認證，例如使用者名稱和密碼。 Runbook 和 DSC 組態會使用接受 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 物件進行驗證的 Cmdlet。 或者，其可以解壓縮 `PSCredential` 物件的使用者名稱和密碼，以提供給某些需要驗證的應用程式或服務。 

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰，儲存在 Azure 自動化中。 Azure 自動化會將金鑰儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後將其用來加密資產。 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>用來存取認證的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 來建立及管理自動化認證。 其會隨附於 [Az 模組](modules.md#az-modules)中。

| Cmdlet | 描述 |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |擷取包含認證相關中繼資料的 [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) 物件。 Cmdlet 不會擷取 `PSCredential` 物件本身。  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |建立新的自動化認證。 |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |移除自動化認證。 |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |設定現有自動化認證的屬性。 |

## <a name="other-cmdlets-used-to-access-credentials"></a>用來存取認證的其他 Cmdlet

下表中的 Cmdlet 用來存取您 Runbook 和 DSC 組態中的認證。 

| Cmdlet | 描述 |
|:--- |:--- |
| `Get-AutomationPSCredential` |取得要在 Runbook 或 DSC 組態中使用的 `PSCredential` 物件。 最常見的情況是，您應該使用此[內部 Cmdlet](modules.md#internal-cmdlets) 而不是 `Get-AzAutomationCredential` Cmdlet，因為後者只會擷取認證資訊。 這項資訊傳遞給另一個 Cmdlet 通常沒有幫助。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |取得具有使用者名稱和密碼提示的認證。 此 Cmdlet 是預設 Microsoft.PowerShell.Security 模組的一部分。 請參閱[預設模組](modules.md#default-modules)。|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 建立認證資產。 此 Cmdlet 是預設 Azure 模組的一部分。 請參閱[預設模組](modules.md#default-modules)。|

若要在程式碼中擷取 `PSCredential` 物件，您必須匯入 `Orchestrator.AssetManagement.Cmdlets` 模組。 如需詳細資訊，請參閱[在 Azure 自動化中管理模組](modules.md)。

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 您應該避免在 `Get-AutomationPSCredential` 的 `Name` 參數中使用變數。 其使用可能會在設計階段將 Runbook 或 DSC 組態與認證資產之間的相依性探索變得複雜。

## <a name="python-2-functions-that-access-credentials"></a>存取認證的 Python 2 函式

下表中的函式用於存取 Pytho 2 Runbook 中的認證。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_credential` | 擷取認證資產的相關資訊。 |

> [!NOTE]
> 在 Python Runbook 的頂端匯入 `automationassets` 模組，才能存取資產函式。

## <a name="create-a-new-credential-asset"></a>建立新的認證資產

您可以使用 Azure 入口網站或使用 Windows PowerShell 來建立新的認證資產。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 入口網站建立新的認證資產

1. 從您的自動化帳戶中，選取 [共用資源] 下的 [認證]。
1. 選取 [新增認證]。
2. 在 [新增認證] 窗格中，按照您的命名標準輸入適當的認證名稱。 
3. 在 [使用者名稱] 欄位中輸入您的存取識別碼。 
4. 在兩個 [密碼] 欄位中，輸入您的祕密存取金鑰。

    ![建立新認證](../media/credentials/credential-create.png)

5. 如果已核取 [多重要素驗證] 方塊，請將其取消選取。 
6. 按一下 [建立] 以儲存新的憑證資產。

> [!NOTE]
> 「Azure 自動化」不支援使用多重要素驗證的使用者帳戶。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立新的認證資產

下列範例示範如何建立新的自動化認證資產。 `PSCredential` 物件是先建立了名稱和密碼，然後用來建立認證資產。 相反地，您可以使用 `Get-Credential` Cmdlet 來提示使用者輸入名稱和密碼。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>取得認證資產

Runbook 或 DSC 組態會使用內部 `Get-AutomationPSCredential` Cmdlet 來擷取認證資產。 此 Cmdlet 會取得 `PSCredential` 物件，可供您搭配需要認證的 Cmdlet 使用。 您也可以擷取要個別使用的認證物件的屬性。 物件具有使用者名稱和安全密碼的屬性。 

> [!NOTE]
> `Get-AzAutomationCredential` Cmdlet 不會擷取可用於驗證的 `PSCredential` 物件。 其只提供有關認證的資訊。 如果您需要在 Runbook 中使用認證，則必須使用 `Get-AutomationPSCredential` 將其擷取做為 `PSCredential` 物件。

或者，您可以使用 [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) 方法來擷取代表不安全密碼版本的 [NetworkCredential](/dotnet/api/system.net.networkcredential) 物件。

### <a name="textual-runbook-example"></a>文字式 Runbook 範例

下列範例示範如何在 Runbook 中使用 PowerShell 認證。 其會擷取認證，並將其使用者名稱和密碼指派給變數。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您也可以使用認證，透過 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) 向 Azure 進行驗證。 在大部分的情況下，您應該使用[執行身分帳戶](../manage-runas-account.md)，並使用 [Get-AzAutomationConnection](../automation-connections.md) 來擷取連線。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的認證上按一下滑鼠右鍵，然後選取 [新增至畫布]，即可將內部 `Get-AutomationPSCredential` Cmdlet 的活動新增至圖形化 Runbook。

![加入認證至畫布](../media/credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。 在此情況下，認證會向 Azure 資源提供 Runbook 的驗證，如[在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證](../automation-use-azure-ad.md)中所述。 第一個活動會擷取可存取 Azure 訂用帳戶的認證。 接著，帳戶連線活動會使用這個認證為隨後的任何活動提供驗證。 在此處使用[管線連結](../automation-graphical-authoring-intro.md#use-links-for-workflow)，因為 `Get-AutomationPSCredential` 預期的是單一物件。  

![加入認證至畫布](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>在 DSC 組態中使用認證

雖然 Azure 自動化中的 DSC 組態可以使用 `Get-AutomationPSCredential` 來處理認證資產，但也可以透過參數傳遞認證資產。 如需詳細資訊，請參閱 [編譯 Azure Automation DSC 中的設定](../automation-dsc-compile.md#credential-assets)。

## <a name="use-credentials-in-a-python-2-runbook"></a>在 Python 2 Runbook 中使用認證

下列範例示範存取 Python 2 Runbook 中認證的範例。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解用來存取憑證的 Cmdlet，請參閱[管理 Azure 自動化中的模組](modules.md)。
* 如需 Runbook 的一般資訊，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。
* 如需 DSC 組態的詳細資訊，請參閱 [Azure 自動化狀態設定總覽](../automation-dsc-overview.md)。 
