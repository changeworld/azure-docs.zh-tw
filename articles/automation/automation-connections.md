---
title: Azure 自動化中的連接資產
description: Azure 自動化中的連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 這篇文章說明連接的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: d1ce59322b0cbfaf5a4f7b31371f0d0929a3a3ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457717"
---
# <a name="connection-assets-in-azure-automation"></a>Azure 自動化中的連接資產

自動化連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 除了連接資訊，例如 URL 或連接埠等，這可能包括驗證所需的資訊，例如使用者名稱和密碼。 連接的值會將連接到特定應用程式的所有屬性放在一個資產中，而不是建立多個變數。 使用者可以在一個地方編輯連接的值，而您可以將連接的名稱在單一參數中傳遞至 Runbook 或 DSC 設定。 可以在 Runbook 或 DSC`Get-AutomationConnection`配置中訪問與活動的連接屬性。

當您建立連線時，您必須指定 [連線類型]。 連接類型是定義一組屬性的範本。 連接會定義在其連接類型中定義的每一個屬性的值。 如果整合模組包含連線類型，且已匯入您的自動化帳戶，連線類型是在整合模組中加入 Azure 自動化或使用 [Azure 自動化 API](/previous-versions/azure/reference/mt163818(v=azure.100))建立。 否則,必須創建元數據檔以指定自動化連接類型。 有關此的詳細資訊,請參閱[整合模組](automation-integration-modules.md)。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產使用為每個自動化帳戶生成的唯一密鑰在 Azure 自動化中加密和存儲。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序由 Azure 自動化所管理。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="connection-types"></a>連線類型

Azure 自動化中有三種類型的內建連線:

* **Azure** - 此連線可用來管理傳統資源。
* **AzureClassicCertificate** - 此連線由 **AzureClassicRunAs** 帳戶所使用。
* **AzureServicePrincipal** - 此連線由 **AzureRunAs** 帳戶所使用。

在大多數情況下,您不需要建立連接資源,因為它是在創建[「運行方式」帳戶](manage-runas-account.md)時創建的。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來使用 Windows PowerShell 建立和管理自動化連接。 它們作為[Azure PowerShell 模組](/powershell/azure/overview)的一部分發貨,可用於自動化運行簿和 DSC 配置。

|Cmdlet|描述|
|---|---|
|[取得自動化連線](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|擷取連接。 包括具有連接欄位值的哈希表。|
|[新-阿茲自動化連線](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|建立新連接。|
|[刪除-阿茲自動化連線](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|刪除現有連接。|
|[設定-阿茲自動化連接場值](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|設定現有連接的特定欄位的值。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 或 DSC 設定中的連接。

|活動|描述|
|---|---|
|`Get-AutomationConnection` | 取得要使用的連接。 傳回具有連線屬性的雜湊表。|

>[!NOTE]
>避免將變數與`Name`參數一`Get-AutomationConnection`起使用。 使用此參數會使 Runbook 或 DSC 配置與設計時的連接資產之間的依賴項發現複雜化。

## <a name="python-2-functions"></a>Python 2 函數

下表中的函數用於造訪 Python 2 執行簿中的連接。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_connection` | 擷取連接。 傳回具有連線屬性的字典。 |

> [!NOTE]
> 您必須在`automationassets`Python runbook 頂部導入模組才能存取資產函數。

## <a name="creating-a-new-connection"></a>建立新連線

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>使用 Azure 入口網站建立新的連接

1. 從自動化帳戶中,按一下 **「資產**」部分以打開 **「資產**」邊欄選項卡。
2. 按一下 [連接]**** 部分，以開啟 [連接]**** 刀鋒視窗。
3. 在分頁的頂端按一下 [ **加入連接** ]。
4. 在 [ **類型** ] 下拉式清單中，選取您想要建立的連接類型。 表單會顯示該特定類型的屬性。
5. 完成表單，然後按一下 [ **建立** ] 以儲存新連接。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 建立新連接

使用`New-AzAutomationConnection`cmdlet 與 Windows PowerShell 創建新連接。 此 cmdlet`ConnectionFieldValues`具有一 個名為的參數,該參數需要一個[可哈希值](https://technet.microsoft.com/library/hh847780.aspx),用於定義由連接類型定義的每個屬性的值。

可以使用以下範例命令作為從門戶創建" 執行方式「 帳戶以創建新連接資產的替代方法。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

您可以使用該文本創建連接資產,因為當您創建自動化帳戶時,預設情況下它會自動包含多個全域模組以及創建`AzureServicePrincipal``AzureRunAsConnection`連接資產的連接類型。 這很重要要牢記在心，因為如果您嘗試建立新的連接資產來連接到採用不同驗證方法的服務或應用程式，它會失敗，因為在您的自動化帳戶未定義該連接類型。 有關如何從[PowerShell 函式庫](https://www.powershellgallery.com)為自訂或模組建立自己的連線類型的詳細資訊,請參考[整合模組](automation-integration-modules.md)。

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定中使用連接

使用 cmdlet 檢索`Get-AutomationConnection`Runbook 或 DSC 設定中的連接。 無法使用作用`Get-AzAutomationConnection`。 此活動檢索連接中不同欄位的值,並將它們作為[可哈希值](https://go.microsoft.com/fwlink/?LinkID=324844)返回。 然後,此哈希表可用於 Runbook 或 DSC 配置中的相應命令。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例命令示範如何使用先前提及的執行身分帳戶，向您的 Runbook 中的 Azure Resource Manager 資源驗證。 它會使用代表執行身分帳戶的連接資產，亦即會參考憑證型服務主體而非認證型。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> 對於非圖形 PowerShell`Add-AzAccount`執行`Add-AzureRMAccount`簿, 並且是[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可以使用這些 cmdlet,也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶,您也可能需要更新模組。

### <a name="graphical-runbook-samples"></a>圖形化 Runbook 範例

以右鍵`Get-AutomationConnection`按一下圖形編輯器的「庫」窗格中的連線並選擇「**新增到畫布**」,將活動添加到圖形 Runbook。

![加入至畫布](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用連接的範例。 這與上面所示使用帶有文本 Runbook 的「運行作為」帳戶進行身份驗證的範例相同。 本示例使用`Constant value`使用連接物件進行身份`Get RunAs Connection`驗證 的活動的數據集。 此處使用[管道連結](automation-graphical-authoring-intro.md#links-and-workflow),`ServicePrincipalCertificate`因為參數集需要單個物件。

![取得連線](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 執行簿範例

下面的範例展示如何在 Python 2 runbook 中使用「運行作為」連接進行身份驗證。

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
* 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 若要進一步了解 Azure 自動化如何使用 PowerShell 模組，以及建立自有 PowerShell 模組來做為 Azure 自動化內整合模組的最佳做法，請參閱[整合模組](automation-integration-modules.md)。