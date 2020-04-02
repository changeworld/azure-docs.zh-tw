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
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546421"
---
# <a name="credential-assets-in-azure-automation"></a>Azure 自動化中的認證資產

自動化憑據資產包含包含安全認證(如使用者名和密碼)的物件。 Runbook 和 DSC 設定使用接受[PS 認證](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)的物件的 cmdlet 進行身份驗證。 或者,他們可以提取`PSCredential`物件的使用者名和密碼,以提供給需要身份驗證的某些應用程式或服務。 

Azure 自動化安全地存儲認證的屬性。 通過 Runbook 或 DSC 設定訪問屬性使用[獲取自動化PS認證的](#activities-used-to-access-credentials)活動。

> [!NOTE]
> Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產使用為每個自動化帳戶生成的唯一密鑰在 Azure 自動化中加密和存儲。 此金鑰會儲存在 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>用於認證資產的 Azure PowerShell Az cmdlet

作為 Azure PowerShell Az 模組的一部分,下表中的 cmdlet 用於使用 Windows PowerShell 創建和管理自動化認證資產。 它們在[Az.自動化模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)中發貨,可用於自動化手冊和 DSC 配置。 請參考[Azure 自動化中的 Az 模組支援](https://docs.microsoft.com/azure/automation/az-modules)。

| Cmdlet | 描述 |
|:--- |:--- |
| [取得阿茲自動化憑證](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |擷取認證資產的相關資訊。 此 cmdlet 不會`PSCredential`傳回物件 。  |
| [新自動化憑證](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |建立新的自動化認證。 |
| [刪除-阿茲自動化認證](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |移除自動化認證。 |
| [設定-阿茲自動化證書](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |設定現有自動化認證的屬性。 |

## <a name="activities-used-to-access-credentials"></a>存取認證的活動

下表中的活動用於存取 Runbook 和 DSC 設定中的認證。

| 活動 | 描述 |
|:--- |:--- |
| `Get-AutomationPSCredential` |取得要在 Runbook 或 DSC 設定中使用的認證。 認證以物件的形式出現`PSCredential`。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |獲取具有使用者名和密碼提示的認證。 |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 創建憑據資產。 |

對於使用 Azure 自動化創作工具套件的本地開發,cmdlet`Get-AutomationPSCredential`是程式集 Azure[自動化創作工具套件的](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)一部分。 對於使用自動化上下文的 Azure,cmdlet`Orchestrator.AssetManagement.Cmdlets`位於 中。 請參考[Azure 自動化中管理模組](modules.md)。

若要檢索`PSCredential`程式碼中的物件,可以[安裝 PowerShell ISE 的 Microsoft Azure 自動化 ISE 載入項](https://github.com/azureautomation/azure-automation-ise-addon)。

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

文本還可以根據需要導入所需的模組,如以下示例所示: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 應避免在`Name`的`Get-AutomationPSCredential`參數中使用變數。 它們的使用會使在設計時 Runbook 或 DSC 配置和憑據資產之間的依賴項發現複雜化。

## <a name="python2-functions-that-access-credentials"></a>存取認證的 Python2 函數

下表中的函式用於存取 Python2 Runbook 中的認證。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_credential` | 擷取認證資產的相關資訊。 |

> [!NOTE]
> 導入`automationassets`Python runbook 頂部的模組以存取資產函數。

## <a name="creating-a-new-credential-asset"></a>建立新的認證資產

您可以使用 Azure 門戶或使用 Windows PowerShell 創建新的認證資產。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 門戶建立新的認證資產

1. 從自動化帳戶中選擇 **「分享資源**」下的**認證**。
1. 選取 [新增認證]****。
2. 在"新建認證"窗格中,按照命名標準輸入適當的認證名稱。 
3. 在 **「使用者名稱」** 欄位中鍵入您的存取 ID。 
4. 對於這兩個密碼欄位,請輸入您的機密訪問密鑰。

    ![建立新認證](../media/credentials/credential-create.png)

5. 如果選中多重身份驗證框,則取消選中它。 
6. 按下 **「創建**」以保存新的憑據資產。

> [!NOTE]
> Azure 自動化不支援使用多重身份驗證的使用者帳戶。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立新的認證資產

下面的範例展示如何創建新的自動化憑據資產。 首先`PSCredential`使用名稱和密碼創建物件,然後用於創建憑據資產。 相反,`Get-Credential`您可以使用 cmdlet 提示使用者鍵入使用者名稱和密碼。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>使用 PowerShell 認證

Runbook 或 DSC 配置檢索`Get-AutomationPSCredential`具有活動的 認證資產。 此活動檢索可用於需要`PSCredential`憑據的活動或 cmdlet 的物件。 您也可以擷取要個別使用的認證物件的屬性。 該物件具有使用者名和安全密碼的屬性。 或者,您可以使用[GetNetwork認證取](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0)取方法檢索表示密碼不安全版本的[Network 認證](/dotnet/api/system.net.networkcredential)。

> [!NOTE]
> `Get-AzAutomationCredential`不檢索可用於身份驗證`PSCredential`的物件。 它僅提供有關憑據的資訊。 如果需要在 Runbook 中使用認證,則必須`PSCredential``Get-AutomationPSCredential`使用檢索該認證作為物件。

### <a name="textual-runbook-example"></a>文字執行簿範例

下面的示例演示如何在 Runbook 中使用 PowerShell 認證。 它檢索憑據並將其使用者名和密碼分配給變數。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您還可以使用認證對 Azure[連線-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)。 在大多數情況下,您應該使用[「運行即」帳戶](../manage-runas-account.md),並檢索與[Get-AzAutomationConnection](../automation-connections.md)的連接。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>圖像圖形執行簿範例

您可以通過右鍵按`Get-AutomationPSCredential`一下 圖形編輯器的「庫」窗格中的認證並選擇「**新增到畫布**」,將活動添加到圖形 Runbook。

![加入認證至畫布](../media/credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。 在這種情況下,憑據為 Runbook 向 Azure 資源提供身份驗證,如在[Azure 自動化中使用 Azure AD 對 Azure](../automation-use-azure-ad.md)進行身份驗證中所述。 第一個活動會擷取可存取 Azure 訂用帳戶的認證。 然後,帳戶連接活動使用此憑據為它之後的任何活動提供身份驗證。 此處使用[管道連結](../automation-graphical-authoring-intro.md#links-and-workflow)`Get-AutomationPSCredential`,因為需要單個物件。  

![加入認證至畫布](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>在 DSC 設定中使用認證

雖然 Azure 自動化中的 DSC`Get-AutomationPSCredential`配置可以使用 的認證資產使用,但它們還可以透過參數傳遞認證資產。 如需詳細資訊，請參閱 [編譯 Azure Automation DSC 中的設定](../automation-dsc-compile.md#credential-assets)。

## <a name="using-credentials-in-python2"></a>使用 Python2 中的認證

下面的範例顯示了在 Python2 Runbook 中存取認證的範例。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>後續步驟

* 要瞭解有關圖形創作中的連結的更多詳細資訊,請參閱[圖形創作中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。
* 要瞭解自動化的不同身份驗證方法,請參閱[Azure 自動化安全](../automation-security-overview.md)。
* 要開始使用圖形執行簿,請參閱[我的第一個圖形執行簿](../automation-first-runbook-graphical.md)。
* 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](../automation-first-runbook-textual.md)。
* 要開始使用 Python2 執行簿,請參考[我的第一個 Python2 執行簿](../automation-first-runbook-textual-python2.md)。 
