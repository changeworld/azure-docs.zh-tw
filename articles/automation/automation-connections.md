---
title: 管理 Azure 自動化中的連線
description: 本文說明如何管理 Azure 自動化與外部服務或應用程式的連線，以及如何在 Runbook 中加以使用。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 0a3cff616f814b8e5209b15f9d3f7439533452ca
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071756"
---
# <a name="manage-connections-in-azure-automation"></a>管理 Azure 自動化中的連線

Azure 自動化連線資產包含下面所列的資訊。 從 Runbook 或 DSC 組態連線到外部服務或應用程式需要此資訊。 

* 驗證時所需的資訊，例如使用者名稱和密碼
* 連線資訊，例如 URL 或連接埠

連線資產會將連線到特定應用程式的所有屬性放在一起，因此不需要建立多個變數。 您可以在一個地方編輯連線的值，而您可以將連線的名稱在單一參數中傳遞至 Runbook 或 DSC 組態。 Runbook 或組態會使用外部 `Get-AutomationConnection` Cmdlet 來存取連線的屬性。

建立連線時，您必須指定連線類型。 連接類型是定義一組屬性的範本。 您可以使用整合模組搭配中繼資料檔案，將連線類型新增至 Azure 自動化。 如果整合模組包含連線類型，且已匯入至您的自動化帳戶，則也可以使用 [Azure 自動化 API](/previous-versions/azure/reference/mt163818(v=azure.100)) 建立連線類型。 

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰，儲存在 Azure 自動化中。 Azure 自動化會將金鑰儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後將其用來加密資產。 

## <a name="connection-types"></a>連線類型

Azure 自動化可提供下列內建連線類型：

* `Azure` - 代表用來管理傳統資源的連線。
* `AzureServicePrincipal` - 代表 Azure 執行身分帳戶所使用的連線。
* `AzureClassicCertificate` - 代表傳統 Azure 執行身分帳戶所使用的連線。

在大部分情況下，您並不需要建立連線資源，因為這項資源會在您建立[執行身分帳戶](manage-runas-account.md)時建立。

## <a name="powershell-cmdlets-to-access-connections"></a>存取連線的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 來建立及管理自動化連線。 其會隨附於 [Az 模組](shared-resources/modules.md#az-modules)中。

|Cmdlet|描述|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|擷取連線的相關資訊。|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|建立新連接。|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|移除現有的連線。|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|設定現有連接的特定欄位的值。|

## <a name="internal-cmdlets-to-access-connections"></a>存取連線的內部 Cmdlet

下表中的內部 Cmdlet 是用來存取 Runbook 和 DSC 組態中的連線。 此 Cmdlet 隨附於全域模組 `Orchestrator.AssetManagement.Cmdlets`。 如需詳細資訊，請參閱[內部 Cmdlet](shared-resources/modules.md#internal-cmdlets)。

|內部 Cmdlet|描述|
|---|---|
|`Get-AutomationConnection` | 擷取連線中不同欄位的值，並將其當做[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)傳回。 然後，您可以在 Runbook 或 DSC 組態中使用此雜湊表搭配適當的命令。|

>[!NOTE]
>您應該避免搭配 `Get-AutomationConnection` 的 `Name` 參數使用變數。 在此情況下使用變數，可能會在設計階段將 Runbook 或 DSC 組態與連線資產之間的相依性探索變得複雜。

## <a name="python-2-functions-to-access-connections"></a>用來存取連線的 Python 2 函式

下表中的函式用於存取 Python 2 Runbook 中的連線。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_connection` | 擷取連接。 傳回具有連線屬性的字典。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入 `automationassets` 模組，才能存取資產函式。

## <a name="create-a-new-connection"></a>建立新的連線

### <a name="create-a-new-connection-with-the-azure-portal"></a>使用 Azure 入口網站建立新的連線

在 Azure 入口網站中建立新的連線：

1. 從您的自動化帳戶中，按一下 [共用資源] 下的 [連線]。
2. 在 [連線] 頁面上，按一下 [+ 新增連線]。
4. 在 [新增連線] 窗格的 [類型] 欄位中，選取要建立的連線類型。 您的選擇為 `Azure`、`AzureServicePrincipal` 和 `AzureClassicCertificate`。 
5. 表單會呈現所選擇連線類型的屬性。 完成表單，然後按一下 [ **建立** ] 以儲存新連接。

### <a name="create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 建立新連線

使用 `New-AzAutomationConnection` Cmdlet 搭配 Windows PowerShell 建立新連線。 此 Cmdlet 具有名為 `ConnectionFieldValues` 的參數，對每個連接類型定義之屬性，預期會有雜湊表定義值。

您可以使用下列範例命令，做為從入口網站建立執行身分帳戶的替代方法，以建立新的連線資產。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

當您建立自動化帳戶時，其預設會包含數個全域模組，連同連線類型 `AzureServicePrincipal` 一起建立 `AzureRunAsConnection` 連線資產。 如果您嘗試建立新的連接資產來連接到採用不同驗證方法的服務或應用程式，作業會失敗，因為在您的自動化帳戶未定義該連接類型。 如需為自訂模組建立專屬連線類型的詳細資訊，請參閱[新增連線類型](#add-a-connection-type)。

## <a name="add-a-connection-type"></a>新增連線類型

如果您的 Runbook 或 DSC 組態連線到外部服務，您必須在稱為整合模組的[自訂模組](shared-resources/modules.md#custom-modules)中定義連線類型。 此模組包含可指定連線類型屬性的中繼資料檔案，並命名為 **&lt;ModuleName&gt;-Automation. json**，位於 **.zip** 壓縮檔案的模組資料夾中。 此檔案包含連線的欄位，連線至模組代表的系統或服務時需要這個項目。 使用此檔案，您可以設定連線類型的欄位名稱、資料類型、加密狀態和選擇性狀態。 

下列範例是 **json** 檔案格式中的範本，可為名為 `MyModuleConnection` 的自訂連線類型定義使用者名稱和密碼屬性：

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 組態中取得連線

使用 `Get-AutomationConnection` Cmdlet 擷取 Runbook 或 DSC 組態中的連線。 此 Cmdlet 優先於 `Get-AzAutomationConnection` Cmdlet，因為其會擷取連線值，而不是連線的相關資訊。 

### <a name="textual-runbook-example"></a>文字式 Runbook 範例

下列範例示範如何使用執行身分帳戶，搭配您 Runbook 中的 Azure Resource Manager 資源進行驗證。 其會使用代表執行身分帳戶的連接資產，亦即會參考憑證型服務主體。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>圖形化 Runbook 範例

您可以將內部 Cmdlet `Get-AutomationConnection` 的活動新增至圖形化 Runbook。 在圖形化編輯器的 [程式庫] 窗格中，以滑鼠右鍵按一下連線，然後選取 [加入至畫布]。

![加入至畫布](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用連線物件的範例。 這個範例會對使用連接物件進行驗證的 `Get RunAs Connection` 活動使用 `Constant value` 資料集。 這裡會使用[管道連結](automation-graphical-authoring-intro.md#use-links-for-workflow)，因為 `ServicePrincipalCertificate` 參數集需要單一物件。

![取得連線](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Python 2 Runbook 範例

下列範例會示範如何使用 Python 2 Runbook 中的執行身分連線進行驗證。

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

* 若要深入了解用來存取連線的 Cmdlet，請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)。
* 如需 Runbook 的一般資訊，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。
* 如需 DSC 組態的詳細資訊，請參閱 [State Configuration 概觀](automation-dsc-overview.md)。