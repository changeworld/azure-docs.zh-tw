---
title: 設定 BYOS (將您自己的儲存體) 用於 Profiler & 快照偵錯工具
description: 設定 BYOS (將您自己的儲存體) 用於 Profiler & 快照偵錯工具
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 04/14/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 719f0cfa0a1f80568acf3231ce3ffab441e5f6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117386"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>為 Application Insights Profiler 和快照偵錯工具設定自備儲存體 (BYOS) 

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>什麼是攜帶您自己的儲存體 (BYOS) 以及為什麼需要它？ 
當您使用 Application Insights Profiler 或快照偵錯工具時，您的應用程式所產生的成品會透過公用網際網路上傳至 Azure 儲存體帳戶。 這些帳戶由 Microsoft 付費及控制，以進行處理和分析。 Microsoft 會控制這些構件的靜態加密和存留期管理原則。

使用「攜帶您自己的儲存體」時，這些構件會上傳至您所控制的儲存體帳戶。 這表示您會控制待用加密原則、存留期管理原則和網路存取。 不過，您將負責處理與該儲存體帳戶相關聯的成本。

> [!NOTE]
> 如果您要啟用 Private Link，則需要攜帶您自己的儲存體。 如需 Application Insights Private Link 的詳細資訊， [請參閱檔。](../platform/private-link-security.md)
>
> 如果您要啟用 Customer-Managed 金鑰，則需要攜帶您自己的儲存體。 如需 Application Insights Customer-Managed 索引鍵的詳細資訊， [請參閱檔](../platform/customer-managed-keys.md)集。

## <a name="how-will-my-storage-account-be-accessed"></a>如何存取我的儲存體帳戶？
1. 在您的虛擬機器或 App Service 中執行的代理程式，會將成品 (設定檔、快照集和符號) 上傳至您帳戶中的 blob 容器。 此程式牽涉到聯繫 Application Insights Profiler 或快照偵錯工具服務，以取得 SAS (共用存取簽章) 權杖新增至儲存體帳戶中的新 blob。
1. Application Insights Profiler 或快照偵錯工具服務會分析傳入的 blob，並將分析結果和記錄檔回寫至 blob 儲存體。 根據可用的計算容量，此程式可能會在上傳之後隨時進行。
1. 當您查看分析工具追蹤或快照偵錯工具分析時，服務將會從 blob 儲存體提取分析結果。

## <a name="prerequisites"></a>Prerequisites
* 請務必在與 Application Insights 資源相同的位置中建立您的儲存體帳戶。 例如 如果您的 Application Insights 資源美國西部2，您的儲存體帳戶也必須在美國西部2中。 
* 透過存取控制 (IAM) UI，在您的儲存體帳戶中將「儲存體 Blob 資料參與者」角色授與 AAD 應用程式「診斷服務信任的儲存體存取」。
* 如果 Private Link 啟用，請設定其他設定，以允許從您的虛擬網路連線到我們信任的 Microsoft 服務。 

## <a name="how-to-enable-byos"></a>如何啟用 BYOS

### <a name="create-storage-account"></a>建立儲存體帳戶
如果您未在與 Application Insights 資源相同的位置上) ，請建立全新的儲存體帳戶 (。
如果您的 Application Insights 資源已開啟 `West US 2` ，則您的儲存體帳戶必須在中 `West US 2` 。

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>將診斷服務的存取權授與您的儲存體帳戶
BYOS 儲存體帳戶將會連結至 Application Insights 資源。 每個 Application Insights 資源可能只有一個儲存體帳戶，且兩者必須位於相同的位置。 您可以使用具有多個 Application Insights 資源的同一個儲存體帳戶。

首先，必須將儲存體帳戶的存取權授與 Application Insights Profiler 和快照偵錯工具服務。 若要授與存取權，請透過 `Storage Blob Data Contributor` `Diagnostic Services Trusted Storage Access` 儲存體帳戶中的存取控制 (IAM) 頁面，將角色新增至名為的 AAD 應用程式，如圖1.0 所示。 

步驟： 
1. 按一下 [新增角色指派] 區段中的 [新增] 按鈕 
1. 選取「儲存體 Blob 資料參與者」角色 
1. 在 [指派存取權] 區段中選取 [Azure AD 使用者、群組或服務主體] 
1. 搜尋 & 選取 [診斷服務受信任的儲存體存取] 應用程式 
1. 儲存變更

_ ![ 圖 1.0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _圖 1.0_ 

新增角色之後，它會出現在 [角色指派] 區段底下，如下圖1.1 所示。 
_ ![ 圖 1.1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _圖 1.1_ 

如果您也使用 Private Link，則需要一個額外的設定，以允許從您的虛擬網路連線到我們的受信任的 Microsoft 服務。 請參閱 [存放裝置網路安全性檔案](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>將儲存體帳戶連結到您的 Application Insights 資源
若要為程式碼層級診斷設定 BYOS (Profiler/偵錯工具) ，有三個選項：

* 使用 Azure PowerShell Cmdlet
* 使用 Azure 命令列介面 (CLI) 
* 使用 Azure Resource Manager 範本

#### <a name="configure-using-azure-powershell-cmdlets"></a>使用 Azure PowerShell Cmdlet 進行設定

1. 請確定您已安裝 Az PowerShell 4.2.0 或更新版本。

    若要安裝 Azure PowerShell，請參閱 [官方 Azure PowerShell 檔](/powershell/azure/install-az-ps)。

1. 安裝 Application Insights PowerShell 擴充功能。
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. 使用您的 Azure 帳戶登入
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    如需如何登入的詳細資訊，請參閱 [disconnect-azaccount 檔](/powershell/module/az.accounts/connect-azaccount)。

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

1. 將您的儲存體帳戶與您的 Application Insights 資源連接。
    
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

    若要安裝 Azure CLI，請參閱 [官方 Azure CLI 檔](/cli/azure/install-azure-cli)。

1. 安裝 Application Insights CLI 擴充功能。
    ```powershell
    az extension add -n application-insights
    ```

1. 將您的儲存體帳戶與您的 Application Insights 資源連接。

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
    > 若要在連結的儲存體帳戶上執行 Application Insights 資源的更新，請參閱 [APPLICATION INSIGHTS CLI 檔](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)。

#### <a name="configure-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本設定

1. 使用下列內容建立 Azure Resource Manager 範本檔案 ( # B0 在) 上。
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

1. 執行下列 PowerShell 命令，以部署先前的範本 (建立連結的儲存體帳戶) 。

    模式：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    範例：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. 當 PowerShell 主控台出現提示時，請提供下列參數：
    
    |           參數           |                                說明                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | 要啟用 BYOS 之 Application Insights 資源的名稱。            |
    | storage_account_name          | 您將用作 BYOS 的儲存體帳戶資源的名稱。 |
    
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

1. 透過 Azure 入口網站來啟用程式碼層級診斷 (分析工具/偵錯工具) 感興趣的工作負載。  (App Service > Application Insights) _ ![ 圖 2.0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _圖 2.0_

## <a name="troubleshooting"></a>疑難排解
### <a name="template-schema-schema_uri-isnt-supported"></a>不支援範本架構 ' {schema_uri} '。
* 請確定範本的 `$schema` 屬性是有效的。 它必須遵循下列模式： `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* 確定 `schema_version` 範本的是在有效的值內： `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` 。
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
### <a name="storage-account-location-should-match-ai-component-location"></a>儲存體帳戶位置應符合 AI 元件的位置。
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

如需一般分析工具的疑難排解，請參閱 [Profiler 疑難排解檔](profiler-troubleshooting.md)。

如需疑難排解的一般快照偵錯工具，請參閱 [快照偵錯工具的疑難排解檔](snapshot-debugger-troubleshoot.md)。 

## <a name="faqs"></a>常見問題集
* 如果我已啟用 Profiler 或快照集，然後再啟用 BYOS，我的資料將會遷移至我的儲存體帳戶嗎？
    _否，它不會。_

* BYOS 是否會使用待用加密和 Customer-Managed 金鑰？
    _沒錯，為了精確起見，BYOS 是使用 Customer-Manager 金鑰啟用分析工具/偵錯工具的必要項。_

* BYOS 是否會在與網際網路隔離的環境中工作？
    _是的。事實上，BYOS 是隔離網路案例的需求。_

* 當 Customer-Managed 索引鍵和 Private Link 都啟用時，BYOS 是否可運作？ 
    _是的，可能會發生這種情況。_

* 如果我已啟用 BYOS，是否可以使用診斷服務儲存體帳戶來儲存所收集的資料？ 
    _是的，您可以，但現在我們不支援從 BYOS 進行資料移轉。_

* 在啟用 BYOS 之後，我是否會接管 it 的所有相關成本，也就是儲存體和網路？ 
    _是_
