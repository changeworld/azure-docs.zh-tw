---
title: 使用 PowerShell 監視和管理 Azure 串流分析作業
description: 本文說明如何使用 Azure PowerShell 和 Cmdlet 來監視及管理 Azure 串流分析作業。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 4d41e8231f5bd186408ea511c0f096a6a5f26c83
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123637"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>透過 Azure PowerShell Cmdlet 監視和管理串流分析工作
了解如何透過執行基本串流分析工作的 Azure PowerShell Cmdlet 及 PowerShell 指令碼，來監視及管理串流分析的資源。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>執行 Azure PowerShell Cmdlet 進行串流分析的必要條件
* 在您的訂用帳戶中建立「Azure 資源群組」。 下列是 PowerShell 指令碼範例。 如需 Azure PowerShell 資訊，請參閱 [安裝並設定 Azure PowerShell](/powershell/azure/)。  

Azure PowerShell 0.9.8：  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0：

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> 以程式控制方式建立的串流分析工作預設不會啟用監視功能。  您可以在 Azure 入口網站中巡覽至工作的 [監視] 頁面，並按一下 [啟用] 按鈕，以手動啟用監視，也可以依照 [Azure 串流分析 - 以程式設計方式監視串流分析工作](stream-analytics-monitor-jobs.md)中的步驟，以程式設計方式啟用監視。
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>用於串流分析的 Azure PowerShell Cmdlet
下表列出可用來監視和管理 Azure 串流分析工作的 Azure PowerShell Cmdlet。 請注意，Azure PowerShell 有不同的版本。 
**下列範例中所列出的第一個命令是針對 Azure PowerShell 0.9.8，第二個命令是針對 Azure PowerShell 1.0。** Azure PowerShell 1.0 命令中一律有 "Az"。

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
列出 Azure 訂用帳戶或指定的資源群組中定義的所有「串流分析」工作，或取得與資源群組內特定工作相關的工作資訊。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob
```

此 PowerShell 命令會傳回 Azure 訂用帳戶中所有「串流分析」工作的相關資訊。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

此 PowerShell 命令會傳回資源群組 StreamAnalytics-Default-Central-US 中所有串流分析工作的相關資訊。

**範例 3**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

此 PowerShell 命令會傳回 StreamAnalytics-Default-Central-US 資源群組中 StreamingJob 串流分析工作的相關資訊。

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
列出指定串流分析工作中定義的所有輸入，或取得特定輸入的相關資訊。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義的所有輸入的相關資訊。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義，名為 EntryStream 之輸入的相關資訊。

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
列出指定串流分析工作中定義的所有輸出，或取得特定輸出的相關資訊。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義之輸出的相關資訊。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義，名為 Output 之輸出的相關資訊。

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
取得指定區域之串流處理單位配額的相關資訊。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

此 PowerShell 命令會傳回美國中部區域之串流單位的配額和使用量的相關資訊。

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
取得在串流分析工作中定義之特定轉換的相關資訊。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

此 PowerShell 命令會傳回 StreamingJob 工作中名為 StreamingJob 之轉換的相關資訊。

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
在串流分析工作內建立新的輸入，或更新現有的指定輸入。

您可以在 .json 檔案中或命令列上指定輸入的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸入，但沒有指定 –Force 參數，此 Cmdlet 會詢問是否要取代現有的輸入。

如果您指定 –Force 參數，並指定現有的輸入名稱，則未經確認就會取代輸入。

如需 JSON 檔案結構和內容的詳細資訊，請參閱[串流分析管理 REST API 參考文件庫][stream.analytics.rest.api.reference]的[建立輸入 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-input] 一節。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

此 PowerShell 命令會透過 Input.json 檔案建立新的輸入。 如果輸入定義檔中已經定義指定名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

此 PowerShell 命令會在名為 EntryStream 的工作中建立新的輸入。 如果已經定義此名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 3**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

此 PowerShell 命令會使用檔案的定義取代名為 EntryStream 的現有輸入來源的定義。

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
在 Microsoft Azure 中建立新的串流分析工作，或更新現有指定工作的定義。

您可以在 .json 檔案中或命令列上指定工作的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的工作名稱，但沒有指定 –Force 參數，此 Cmdlet 會詢問是否要取代現有的工作。

如果您指定 –Force 參數，並指定現有的工作名稱，則未經確認就會取代工作定義。

如需 JSON 檔案結構和內容的詳細資訊，請參閱[串流分析管理 REST API 參考文件庫][stream.analytics.rest.api.reference]的[建立串流分析作業][msdn-rest-api-create-stream-analytics-job]一節。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

此 PowerShell 命令會透過 JobDefinition.json 中的定義建立新的工作。 如果輸出定義檔中已經定義了指定名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

此 PowerShell 命令會取代 StreamingJob 的工作定義。

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
在串流分析工作內建立新的輸出，或更新現有的輸出。  

您可以在 .json 檔案中或命令列上指定輸出的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸出，但沒有指定 –Force 參數，此 Cmdlet 會詢問是否要取代現有的輸出。

如果您指定 –Force 參數，並指定現有的輸出名稱，則未經確認就會取代輸出。

如需 JSON 檔案結構和內容的詳細資訊，請參閱[串流分析管理 REST API 參考文件庫][stream.analytics.rest.api.reference]的[建立輸出 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-output] 一節。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

此 PowerShell 命令會在 StreamingJob 工作中建立名為「output」的新輸出。 如果已經定義此名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

此 PowerShell 命令會取代 StreamingJob 工作中「output」的定義。

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
在串流分析工作內建立新的轉換，或更新現有的轉換。

您可以在 .json 檔案中或命令列上指定轉換的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的轉換，但沒有指定 –Force 參數，此 Cmdlet 會詢問是否要取代現有的轉換。

如果您指定 –Force 參數，並指定現有的轉換名稱，則未經確認就會取代轉換。

如需 JSON 檔案結構和內容的詳細資訊，請參閱[串流分析管理 REST API 參考文件庫][stream.analytics.rest.api.reference]的[建立轉換 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-transformation] 一節。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

此 PowerShell 命令會在 StreamingJob 工作中建立名為 StreamingJobTransform 的新轉換。 如果已經使用此名稱定義現有的轉換，Cmdlet 會詢問是否要取代它。

**範例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 此 PowerShell 命令會取代 StreamingJob 工作中 StreamingJobTransform 的定義。

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸入。  
如果您指定 –Force 參數，則未經確認就會刪除輸入。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

此 PowerShell 命令會移除 StreamingJob 工作中的 EventStream 輸入。  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
以非同步方式從 Microsoft Azure 中刪除特定的 Stream Analytics 作業。  
如果您指定 –Force 參數，則未經確認就會刪除作業。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

此 PowerShell 命令會移除 StreamingJob 工作。  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸出。  
如果您指定 –Force 參數，則未經確認就會刪除輸出。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

此 PowerShell 命令會移除 StreamingJob 工作中的 Output 輸出。  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
以非同步方式部署和啟動 Microsoft Azure 中的 Stream Analytics 作業。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0：  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

此 PowerShell 命令會啟動自訂輸出開始時間設為 2012 年 12 月 12 日 12:12:12 UTC 的 StreamingJob 工作。

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
以非同步方式停止在 Microsoft Azure 中執行 Stream Analytics 作業，並取消配置正在使用的資源。 透過 Azure 入口網站和管理 API 仍可使用訂用帳戶內的該工作定義與中繼資料，也因此可以編輯和重新啟動工作。 您將不需要對處於已停止狀態的工作支付費用。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

此 PowerShell 命令會停止 StreamingJob 工作。  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
測試 Stream Analytics 連線到指定輸入的能力。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

此 PowerShell 命令會測試 StreamingJob 中 EntryStream 輸入的連接狀態。  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
測試 Stream Analytics 連線到指定輸出的能力。

**範例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

此 PowerShell 命令會測試 StreamingJob 中 Output 輸出的連接狀態。  

## <a name="get-support"></a>取得支援
如需進一步的協助，請嘗試 [Microsoft 的 Azure 串流分析問與答頁面](/answers/topics/azure-stream-analytics.html)。 

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/