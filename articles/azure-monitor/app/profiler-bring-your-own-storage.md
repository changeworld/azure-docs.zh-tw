---
title: 設定 BYOS （攜帶您自己的儲存體）以進行 Profiler & 快照偵錯工具
description: 設定 BYOS （攜帶您自己的儲存體）以進行 Profiler & 快照偵錯工具
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 04/14/2020
ms.reviewer: mbullwin
ms.openlocfilehash: d84010fd62d753fafd7edffab833b203657f74c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361933"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>針對 Application Insights Profiler 和快照偵錯工具設定攜帶您自己的儲存體（BYOS）

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>何謂攜帶您自己的儲存體（BYOS），以及為何需要它？ 
當您使用 Application Insights Profiler 或快照偵錯工具時，您的應用程式所產生的成品會透過公用網際網路上傳至 Azure 儲存體帳戶。 這些帳戶會由 Microsoft 付費並控制，以進行處理和分析。 Microsoft 會控制這些成品的待用加密和生命週期管理原則。

使用「攜帶您自己的儲存體」，這些成品會上傳至您所控制的儲存體帳戶。 這表示您可以控制待用加密原則、存留期管理原則和網路存取。 不過，您會負責與該儲存體帳戶相關聯的成本。

> [!NOTE]
> 如果您要啟用私用連結，則需要攜帶您自己的儲存體。 如需 Application Insights 之私用連結的詳細資訊，[請參閱檔集。](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)
>
> 如果您要啟用客戶管理的金鑰，則需要攜帶您自己的儲存體。 如需 Application Insights 之客戶管理金鑰的詳細資訊，[請參閱檔](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys)集。

## <a name="how-will-my-storage-account-be-accessed"></a>如何存取我的儲存體帳戶？
1. 在您的虛擬機器或 App Service 中執行的代理程式，會將成品（設定檔、快照集和符號）上傳至您帳戶中的 blob 容器。 此套裝程式括聯絡 Application Insights Profiler 或快照偵錯工具服務，以取得儲存體帳戶中新 blob 的 SAS （共用存取簽章）權杖。
1. Application Insights Profiler 或快照偵錯工具服務會分析傳入的 blob，並將分析結果和記錄檔寫回 blob 儲存體。 視可用的計算容量而定，此程式可能會在上傳後的任何時間發生。
1. 當您查看 profiler 追蹤或快照集偵錯工具分析時，服務會從 blob 儲存體提取分析結果。

## <a name="prerequisites"></a>必要條件
* 請務必在與您 Application Insights 資源相同的位置中建立您的儲存體帳戶。 例如： 如果您的 Application Insights 資源在美國西部2中，則您的儲存體帳戶也必須在美國西部2中。 
* 透過存取控制（IAM） UI，將「儲存體 Blob 資料參與者」角色授與儲存體帳戶中的 AAD 應用程式「診斷服務信任的儲存體存取」。
* 如果已啟用私用連結，請設定其他設定，以允許從您的虛擬網路連線到受信任的 Microsoft 服務。 

## <a name="how-to-enable-byos"></a>如何啟用 BYOS

### <a name="create-storage-account"></a>建立儲存體帳戶
在與 Application Insights 資源相同的位置上，建立全新的儲存體帳戶（如果您沒有的話）。
如果您的 Application Insights 資源已開啟 `West US 2` ，則您的儲存體帳戶必須在中 `West US 2` 。

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>將診斷服務的存取權授與您的儲存體帳戶
BYOS 儲存體帳戶將會連結到 Application Insights 資源。 每個 Application Insights 資源可能只有一個儲存體帳戶，而且兩者都必須位於相同的位置。 您可以使用具有多個 Application Insights 資源的相同儲存體帳戶。

首先，Application Insights Profiler 和快照偵錯工具服務必須被授與儲存體帳戶的存取權。 若要授與存取權，請透過 `Storage Blob Data Contributor` `Diagnostic Services Trusted Storage Access` 儲存體帳戶中的 [存取控制（IAM）] 頁面，將角色新增至名為的 AAD 應用程式，如圖1.0 所示。 

步驟： 
1. 按一下 [新增角色指派] 區段中的 [新增] 按鈕 
1. 選取「儲存體 Blob 資料參與者」角色 
1. 在 [指派存取權給] 區段中，選取 [Azure AD 使用者、群組或服務主體] 
1. 搜尋 & 選取 [診斷服務信任的儲存體存取] 應用程式 
1. 儲存變更

_ ![ 圖 1.0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _圖 1.0_ 

新增角色之後，它會出現在 [角色指派] 區段底下，如下圖1.1 所示。 
_ ![ 圖 1.1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _圖 1.1_ 

如果您也使用私人連結，則需要另外一項設定，以允許從您的虛擬網路連線到受信任的 Microsoft 服務。 請參閱[儲存體網路安全性檔案](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)。

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>將您的儲存體帳戶與您的 Application Insights 資源連結
若要設定程式碼層級診斷（Profiler/偵錯工具）的 BYOS，有兩個選項：

* 使用 Azure PowerShell Cmdlet
* 使用 Azure 命令列介面（CLI）
* 使用 Azure Resource Manager 範本

#### <a name="configure-using-azure-powershell-cmdlets"></a>使用 Azure PowerShell Cmdlet 進行設定

1. 請確定您已安裝 Az PowerShell 4.2.0 或更高版本。

    若要安裝 Azure PowerShell，請參閱[官方 Azure PowerShell 檔](https://docs.microsoft.com/powershell/azure/install-az-ps)。

1. 安裝 Application Insights PowerShell 擴充功能。
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. 使用您的 Azure 帳戶登入
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    如需如何登入的詳細資訊，請參閱[disconnect-azaccount 檔](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)。

1. 移除連結至您 Application Insights 資源的先前儲存體帳戶。

    模式：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    範例：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. 將您的儲存體帳戶與您的 Application Insights 資源連線。
    
    模式：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    範例：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>使用 Azure CLI 進行設定

1. 請確定您已安裝 Azure CLI。

    若要安裝 Azure CLI，請參閱[官方 Azure CLI 檔](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 安裝 Application Insights CLI 擴充功能。
    ```powershell
    az extension add -n application-insights
    ```

1. 將您的儲存體帳戶與您的 Application Insights 資源連線。

    模式：
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    範例：
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    預期輸出：
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > 如需在 Application Insights 資源的連結儲存體帳戶上執行更新，請參閱[APPLICATION INSIGHTS CLI 檔](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)。

#### <a name="configure-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本進行設定

1. 建立具有下列內容的 Azure Resource Manager 範本檔案（byos.template.js開啟）。
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 執行下列 PowerShell 命令來部署先前的範本（建立連結的儲存體帳戶）。

    模式：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    範例：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. 在 PowerShell 主控台中出現提示時，提供下列參數：
    
    |           參數           |                                說明                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | 要啟用 BYOS 的 Application Insights 資源名稱。            |
    | storage_account_name          | 您將用來做為 BYOS 的儲存體帳戶資源的名稱。 |
    
    預期輸出：
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. 透過 Azure 入口網站，在感對的工作負載上啟用程式碼層級診斷（Profiler/偵錯工具）。 （App Service > Application Insights）_ ![ 圖 2.0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _圖 2.0_

## <a name="troubleshooting"></a>疑難排解
### <a name="template-schema-schema_uri-isnt-supported"></a>不支援範本架構 ' {schema_uri} '。
* 請確定範本的 `$schema` 屬性有效。 它必須遵循下列模式：`https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* 請確定範本的 `schema_version` 值在有效值內： `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` 。
    錯誤訊息：
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>找不到位置 ' {location} ' 的已註冊資源提供者。
* 請確定資源的 `apiVersion` `microsoft.insights/components` 是 `2015-05-01` 。
* 請確定資源的 `apiVersion` `linkedStorageAccount` 是 `2020-03-01-preview` 。
    錯誤訊息：
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>儲存體帳戶位置應符合 AI 元件位置。
* 請確定 Application Insights 資源的位置與儲存體帳戶相同。
    錯誤訊息：
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

如需一般 Profiler 疑難排解，請參閱分析工具[疑難排解檔](profiler-troubleshooting.md)。

如需一般快照偵錯工具疑難排解，請參閱[快照偵錯工具疑難排解檔](snapshot-debugger-troubleshoot.md)。 

## <a name="faqs"></a>常見問題集
* 如果我已啟用 Profiler 或快照集，然後又啟用了 BYOS，我的資料會遷移到我的儲存體帳戶嗎？
    _否，不會。_

* BYOS 會使用待用加密和客戶管理的金鑰？
    _是的，若要精確起見，必須使用客戶管理員金鑰來啟用 profiler/偵錯工具，BYOS 是必要的。_

* 在與網際網路隔離的環境中，會 BYOS 工作嗎？
    _是的。事實上，BYOS 是隔離式網路案例的必要條件。_

* 當客戶管理的金鑰和私人連結都已啟用時，將會 BYOS 工作嗎？ 
    _可以，這是可行的。_

* 如果我已啟用 BYOS，我可以返回使用診斷服務儲存體帳戶來儲存收集的資料嗎？ 
    _沒錯，您可以，但是現在我們不支援從您的 BYOS 進行資料移轉。_

* 啟用 BYOS 之後，我會接管其所有相關的成本，也就是儲存體和網路功能嗎？ 
    _是_