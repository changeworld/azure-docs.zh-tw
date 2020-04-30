---
title: 管理 Azure 自動化中的連接
description: Azure 自動化中的連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 這篇文章說明連接的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097110"
---
# <a name="manage-connections-in-azure-automation"></a>管理 Azure 自動化中的連接

自動化連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 除了連接資訊，例如 URL 或連接埠等，這可能包括驗證所需的資訊，例如使用者名稱和密碼。 連接的值會將連接到特定應用程式的所有屬性放在一個資產中，而不是建立多個變數。 使用者可以在一個地方編輯連接的值，而您可以將連接的名稱在單一參數中傳遞至 Runbook 或 DSC 設定。 您可以在 runbook 或 DSC 設定中使用`Get-AutomationConnection`活動來存取連線的屬性。

當您建立連線時，您必須指定 [連線類型]。 連接類型是定義一組屬性的範本。 連接會定義在其連接類型中定義的每一個屬性的值。 如果整合模組包含連線類型，且已匯入您的自動化帳戶，連線類型是在整合模組中加入 Azure 自動化或使用 [Azure 自動化 API](/previous-versions/azure/reference/mt163818(v=azure.100))建立。 否則，您必須建立中繼資料檔案來指定自動化連線類型。 如需這方面的詳細資訊，請參閱[整合模組](automation-integration-modules.md)。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會使用針對每個自動化帳戶產生的唯一金鑰，加密並儲存在 Azure 自動化中。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序由 Azure 自動化所管理。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="connection-types"></a>連線類型

Azure 自動化中提供三種類型的內建連接：

* **Azure** - 此連線可用來管理傳統資源。
* **AzureClassicCertificate** - 此連線由 **AzureClassicRunAs** 帳戶所使用。
* **AzureServicePrincipal** - 此連線由 **AzureRunAs** 帳戶所使用。

在大部分情況下，您不需要建立連線資源，因為它是在您建立執行身分[帳戶](manage-runas-account.md)時建立的。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來使用 Windows PowerShell 建立和管理自動化連接。 它們隨附于[Azure PowerShell 模組](/powershell/azure/overview)中，可在自動化 RUNBOOK 和 DSC 設定中使用。

|Cmdlet|描述|
|---|---|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|擷取連接。 包含具有連接欄位值的 hashtable。|
|[新增-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|建立新連接。|
|[移除-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|移除現有的連接。|
|[設定-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|設定現有連接的特定欄位的值。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 或 DSC 設定中的連接。

|活動|描述|
|---|---|
|`Get-AutomationConnection` | 取得要使用的連接。 傳回具有連線屬性的雜湊表。|

>[!NOTE]
>避免將`Name`變數與的參數搭配`Get-AutomationConnection`使用。 使用此參數可能會使 runbook 或 DSC 設定之間的相依性變複雜，以及設計階段的連接資產。

## <a name="python-2-functions"></a>Python 2 函式

下表中的函式是用來存取 Python 2 runbook 中的連接。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_connection` | 擷取連接。 傳回具有連線屬性的字典。 |

> [!NOTE]
> 您必須在 Python `automationassets` runbook 的頂端匯入模組，才能存取資產功能。

## <a name="creating-a-new-connection"></a>建立新的連接

### <a name="create-a-new-connection-with-the-azure-portal"></a>使用 Azure 入口網站建立新的連接

1. 從您的自動化帳戶，按一下 [**資產**] 部分，以開啟 [**資產**] 分頁。
2. 按一下 [連接]**** 部分，以開啟 [連接]**** 刀鋒視窗。
3. 在分頁的頂端按一下 [ **加入連接** ]。
4. 在 [ **類型** ] 下拉式清單中，選取您想要建立的連接類型。 表單會顯示該特定類型的屬性。
5. 完成表單，然後按一下 [ **建立** ] 以儲存新連接。

### <a name="create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 建立新的連接

使用`New-AzAutomationConnection` Cmdlet 建立與 Windows PowerShell 的新連線。 此 Cmdlet 具有名為`ConnectionFieldValues`的參數，它需要定義連線類型所定義的每個屬性值的[hashtable](https://technet.microsoft.com/library/hh847780.aspx) 。

您可以使用下列範例命令做為從入口網站建立執行身分帳戶的替代方法，以建立新的連接資產。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

當您建立自動化帳戶時，它預設會包含數個全域模組，以及用來建立`AzureServicePrincipal` `AzureRunAsConnection`連接資產的連線類型。 如果您嘗試使用不同的驗證方法來建立新的連線資產以連線至服務或應用程式，則作業會失敗，因為您的自動化帳戶中尚未定義連線類型。 如需有關為自訂[PowerShell 資源庫](https://www.powershellgallery.com)模組建立自己的連線類型的詳細資訊，請參閱[整合模組](automation-integration-modules.md)。

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定中使用連接

使用`Get-AutomationConnection` Cmdlet 取出 RUNBOOK 或 DSC 設定中的連線。 您無法使用`Get-AzAutomationConnection`活動。 此活動會抓取連接中不同欄位的值，並以[hashtable](https://go.microsoft.com/fwlink/?LinkID=324844)的形式傳回。 然後，此 hashtable 可以與 runbook 或 DSC 設定中的適當命令搭配使用。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例命令示範如何使用先前提及的執行身分帳戶，向您的 Runbook 中的 Azure Resource Manager 資源驗證。 它會使用代表執行身分帳戶的連接資產，亦即會參考憑證型服務主體而非認證型。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> 針對非圖形化 PowerShell runbook， `Add-AzAccount`和`Add-AzureRMAccount`是[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

### <a name="graphical-runbook-samples"></a>圖形化 Runbook 範例

在圖形化`Get-AutomationConnection`編輯器的 [程式庫] 窗格中，以滑鼠右鍵按一下連接，然後選取 [**新增至畫布**]，即可將活動新增至圖形化 runbook。

![加入至畫布](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用連接的範例。 這是使用執行身分帳戶搭配文字 runbook 來進行驗證的相同範例，如下所示。 這個範例會針對`Constant value`使用連線物件進行`Get RunAs Connection`驗證的活動使用資料集。 在這裡使用[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)，因為`ServicePrincipalCertificate`參數集需要單一物件。

![取得連線](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 runbook 範例

下列範例示範如何使用 Python 2 runbook 中的「執行身分」連線來進行驗證。

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>後續步驟

- 閱讀[圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)，以了解如何引導和控制您的 Runbook 中的邏輯流程。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 若要進一步了解 Azure 自動化如何使用 PowerShell 模組，以及建立自有 PowerShell 模組來做為 Azure 自動化內整合模組的最佳做法，請參閱[整合模組](automation-integration-modules.md)。