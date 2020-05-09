---
title: 管理 Azure 自動化中的連接
description: Azure 自動化中的連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 這篇文章說明連接的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 1a50c5d3d6e068054cfc8381b220d38471a7eb6c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996581"
---
# <a name="manage-connections-in-azure-automation"></a>管理 Azure 自動化中的連接

Azure 自動化的連線資產包含下面所列的資訊。 從 runbook 或 DSC 設定連接到外部服務或應用程式時，需要此資訊。 

* 驗證所需的資訊，例如使用者名稱和密碼
* 連接資訊，例如 URL 或埠

連線資產會將連接到特定應用程式的所有屬性保持在一起，因此不需要建立多個變數。 您可以在一個位置編輯連線的值，也可以在單一參數中將連接的名稱傳遞至 runbook 或 DSC 設定。 Runbook 或設定會使用 internal `Get-AutomationConnection` Cmdlet 來存取連線的屬性。

當您建立連線時，您必須指定 [連線類型]。 連接類型是定義一組屬性的範本。 您可以使用包含中繼資料檔案的整合模組，將連線類型新增至 Azure 自動化。 如果整合模組包含連線類型，且已匯入您的自動化帳戶中，也可以使用[AZURE 自動化 API](/previous-versions/azure/reference/mt163818(v=azure.100))建立連線類型。 

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會使用針對每個自動化帳戶產生的唯一金鑰，加密並儲存在 Azure 自動化中。 Azure 自動化會將金鑰儲存在系統管理的 Key Vault 中。 儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後使用它來加密資產。 

## <a name="connection-types"></a>連線類型

Azure 自動化可提供下列內建連線類型：

* `Azure`-代表用來管理傳統資源的連接。
* `AzureServicePrincipal`-代表 Azure 執行身分帳戶所使用的連線。
* `AzureClassicCertificate`-代表傳統 Azure 執行身分帳戶所使用的連接。

在大部分情況下，您不需要建立連線資源，因為它是在您建立執行身分[帳戶](manage-runas-account.md)時建立的。

## <a name="powershell-cmdlets-to-access-connections"></a>存取連接的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 建立和管理自動化連線。 它們會隨附于[Az 模組](shared-resources/modules.md#az-modules)中。

|Cmdlet|描述|
|---|---|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|抓取連接的相關資訊。|
|[新增-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|建立新連接。|
|[移除-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|移除現有的連接。|
|[設定-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|設定現有連接的特定欄位的值。|

## <a name="internal-cmdlets-to-access-connections"></a>存取連接的內部 Cmdlet

下表中的內部 Cmdlet 是用來存取您的 runbook 和 DSC 設定中的連接。 此 Cmdlet 隨附于全域模組`Orchestrator.AssetManagement.Cmdlets`。 如需詳細資訊，請參閱[內部 Cmdlet](shared-resources/modules.md#internal-cmdlets)。

|Internal Cmdlet|描述|
|---|---|
|`Get-AutomationConnection` | 抓取連接中不同欄位的值，並將它們當做[雜湊表](https://go.microsoft.com/fwlink/?LinkID=324844)傳回。 然後，您可以在 runbook 或 DSC 設定中使用此 hashtable 搭配適當的命令。|

>[!NOTE]
>避免將`Name`變數與的參數搭配`Get-AutomationConnection`使用。 在此情況下使用變數，會在設計階段將 runbook 或 DSC 設定與連接資產之間的相依性探索變得複雜。

## <a name="python-2-functions-to-access-connections"></a>Python 2 函式以存取連接

下表中的函式是用來存取 Python 2 runbook 中的連接。

| 函式 | 說明 |
|:---|:---|
| `automationassets.get_automation_connection` | 擷取連接。 傳回具有連線屬性的字典。 |

> [!NOTE]
> 您必須在 Python `automationassets` runbook 的頂端匯入模組，才能存取資產功能。

## <a name="create-a-new-connection"></a>建立新的連線

### <a name="create-a-new-connection-with-the-azure-portal"></a>使用 Azure 入口網站建立新的連接

若要在 Azure 入口網站中建立新的連接：

1. 從您的自動化帳戶，按一下 [**共用資源**] 底下的 [**連接**]。
2. 按一下 [連線] 頁面上的 [ **+ 新增連接**]。
4. 在 [新增連接] 窗格的 [**類型**] 欄位中，選取要建立的連線類型。 您的選擇`Azure`是`AzureServicePrincipal`、和`AzureClassicCertificate`。 
5. 表單會顯示您所選擇之連線類型的屬性。 完成表單，然後按一下 [ **建立** ] 以儲存新連接。

### <a name="create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 建立新的連接

使用`New-AzAutomationConnection` Cmdlet 建立與 Windows PowerShell 的新連線。 此 Cmdlet 有一個`ConnectionFieldValues`參數，它需要為連線類型所定義的每個屬性定義值的 hashtable。

您可以使用下列範例命令做為從入口網站建立執行身分帳戶的替代方法，以建立新的連接資產。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

當您建立自動化帳戶時，它預設會包含數個全域模組，以及用來建立`AzureServicePrincipal` `AzureRunAsConnection`連接資產的連線類型。 如果您嘗試使用不同的驗證方法來建立新的連線資產以連線至服務或應用程式，則作業會失敗，因為您的自動化帳戶中尚未定義連線類型。 如需有關為自訂模組建立自己的連線類型的詳細資訊，請參閱[新增連線類型](#add-a-connection-type)。

## <a name="add-a-connection-type"></a>新增連線類型

如果您的 runbook 或 DSC 設定連接到外部服務，您必須在稱為整合模組的[自訂模組](shared-resources/modules.md#custom-modules)中定義連線類型。 此模組包含中繼資料檔案，該檔案會指定連線類型屬性，並將其命名為** &lt;ModuleName&gt;-Automation. json**，位於壓縮 **.zip**檔案的模組資料夾中。 此檔案包含連接到模組所代表的系統或服務所需的連線欄位。 使用此檔案，您可以設定連線類型的功能變數名稱、資料類型、加密狀態和選擇性狀態。 

下列範例是**json**檔案格式的範本，它會定義名`MyModuleConnection`為的自訂連線類型的使用者名稱和密碼屬性：

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>取得 runbook 或 DSC 設定中的連接

使用 internal `Get-AutomationConnection` Cmdlet，在 RUNBOOK 或 DSC 設定中抓取連接。 此 Cmdlet 優於`Get-AzAutomationConnection` Cmdlet，因為它會抓取連接值，而不是連線的相關資訊。 

### <a name="textual-runbook-example"></a>文字式 runbook 範例

下列範例示範如何使用執行身分帳戶，向 runbook 中的 Azure Resource Manager 資源進行驗證。 它會使用代表執行身分帳戶的連線資產，其會參考以憑證為基礎的服務主體。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>圖形化 runbook 範例

您可以將內部`Get-AutomationConnection` Cmdlet 的活動新增至圖形化 runbook。 在圖形化編輯器的 [程式庫] 窗格中，以滑鼠右鍵按一下連接，然後選取 [**新增至畫布**]。

![加入至畫布](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 runbook 中使用連線物件的範例。 這個範例會使用`Constant value` `Get RunAs Connection`活動的資料集，這會使用連線物件來進行驗證。 在這裡使用[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)，因為`ServicePrincipalCertificate`參數集需要單一物件。

![取得連線](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Python 2 runbook 範例

下列範例顯示如何使用 Python 2 runbook 中的執行身分連線進行驗證。

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

* 若要深入瞭解用來存取連接的 Cmdlet，請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)。
* 如需 runbook 的一般資訊，請參閱[Azure 自動化中的 runbook 執行](automation-runbook-execution.md)。
* 如需 DSC 設定的詳細資訊，請參閱[狀態設定總覽](automation-dsc-overview.md)。