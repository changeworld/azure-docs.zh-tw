---
title: 使用 PowerShell 監視和管理 Azure 流分析作業
description: 本文說明如何使用 Azure PowerShell 和 Cmdlet 來監視及管理 Azure 串流分析作業。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 7781f35fe7c17e4a0f307f559945caf648b23f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431711"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>透過 Azure PowerShell Cmdlet 監視和管理串流分析工作
了解如何透過執行基本串流分析工作的 Azure PowerShell Cmdlet 及 PowerShell 指令碼，來監視及管理串流分析的資源。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>執行 Azure PowerShell Cmdlet 進行串流分析的必要條件
* 在您的訂用帳戶中建立「Azure 資源群組」。 下列是 PowerShell 指令碼範例。 如需 Azure PowerShell 資訊，請參閱 [安裝並設定 Azure PowerShell](/powershell/azure/overview)。  

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
Get-AzSubscription �SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> 以程式控制方式建立的串流分析工作預設不會啟用監視功能。  您可以通過導航到作業的"監視器"頁並按一下"啟用"按鈕來手動在 Azure 門戶中啟用監視，也可以按照[Azure 流分析 - 以程式設計方式監視流分析作業的步驟](stream-analytics-monitor-jobs.md)以程式設計方式執行此操作。
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>用於串流分析的 Azure PowerShell Cmdlet
下表列出可用來監視和管理 Azure 串流分析工作的 Azure PowerShell Cmdlet。 請注意，Azure PowerShell 有不同的版本。 
**下列範例中所列出的第一個命令是針對 Azure PowerShell 0.9.8，第二個命令是針對 Azure PowerShell 1.0。** Azure PowerShell 1.0 命令的命令中始終包含"Az"。

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>獲取 Azure 流分析作業 |獲取阿茲特線上分析工作
列出 Azure 訂用帳戶或指定的資源群組中定義的所有「串流分析」工作，或取得與資源群組內特定工作相關的工作資訊。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob
```

此 PowerShell 命令會傳回 Azure 訂用帳戶中所有「串流分析」工作的相關資訊。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

此 PowerShell 命令會傳回資源群組 StreamAnalytics-Default-Central-US 中所有串流分析工作的相關資訊。

**示例 3**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

此 PowerShell 命令會傳回 StreamAnalytics-Default-Central-US 資源群組中 StreamingJob 串流分析工作的相關資訊。

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>獲取 Azure 流分析輸入 |獲取-阿茲特流分析輸入
列出指定串流分析工作中定義的所有輸入，或取得特定輸入的相關資訊。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義的所有輸入的相關資訊。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義，名為 EntryStream 之輸入的相關資訊。

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>獲取 Azure 流分析輸出 |獲取阿茲特流分析輸出
列出指定串流分析工作中定義的所有輸出，或取得特定輸出的相關資訊。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義之輸出的相關資訊。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

此 PowerShell 命令會傳回在 StreamingJob 工作中定義，名為 Output 之輸出的相關資訊。

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>獲取 Azure 流分析配額 |獲取阿茲特線上分析配額
取得指定區域之串流處理單位配額的相關資訊。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsQuota �Location "Central US" 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsQuota �Location "Central US" 
```

此 PowerShell 命令會傳回美國中部區域之串流單位的配額和使用量的相關資訊。

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>獲取 Azure 流分析轉換 |獲取阿茲特流分析轉型
取得在串流分析工作中定義之特定轉換的相關資訊。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

此 PowerShell 命令會傳回 StreamingJob 工作中名為 StreamingJob 之轉換的相關資訊。

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>新 Azure 流分析輸入 |新-阿茲特流分析輸入
在串流分析工作內建立新的輸入，或更新現有的指定輸入。

您可以在 .json 檔案中或命令列上指定輸入的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果指定已存在的輸入，但不指定 Force 參數，Cmdlet 將詢問是否替換現有輸入。

如果指定 Force 參數並指定現有輸入名稱，則將在不確認的情況下替換輸入。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立輸入 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-input] 一節。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

此 PowerShell 命令會透過 Input.json 檔案建立新的輸入。 如果輸入定義檔中已經定義指定名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

此 PowerShell 命令會在名為 EntryStream 的工作中建立新的輸入。 如果已經定義此名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**示例 3**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

此 PowerShell 命令會使用檔案的定義取代名為 EntryStream 的現有輸入來源的定義。

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>新 Azure 流分析作業 |新-阿茲特線上分析工作
在 Microsoft Azure 中建立新的串流分析工作，或更新現有指定工作的定義。

您可以在 .json 檔案中或命令列上指定工作的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果指定已存在的作業名稱，但不指定 Force 參數，Cmdlet 將詢問是否替換現有作業。

如果指定 Force 參數並指定現有作業名稱，則將在不確認的情況下替換作業定義。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立串流分析作業][msdn-rest-api-create-stream-analytics-job]一節。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

此 PowerShell 命令會透過 JobDefinition.json 中的定義建立新的工作。 如果輸出定義檔中已經定義了指定名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

此 PowerShell 命令會取代 StreamingJob 的工作定義。

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>新 Azure 流分析輸出 |新-阿茲特流分析輸出
在串流分析工作內建立新的輸出，或更新現有的輸出。  

您可以在 .json 檔案中或命令列上指定輸出的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果指定已存在的輸出，但不指定 Force 參數，Cmdlet 將詢問是否替換現有輸出。

如果指定 Force 參數並指定現有輸出名稱，則輸出將在未確認的情況下替換。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立輸出 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-output] 一節。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

此 PowerShell 命令會在 StreamingJob 工作中建立名為「output」的新輸出。 如果已經定義此名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

此 PowerShell 命令會取代 StreamingJob 工作中「output」的定義。

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>新 Azure 流分析轉換 |新-阿茲特流分析轉型
在串流分析工作內建立新的轉換，或更新現有的轉換。

您可以在 .json 檔案中或命令列上指定轉換的名稱。 若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果指定已存在的轉換，但不指定 Force 參數，Cmdlet 將詢問是否替換現有轉換。

如果指定 Force 參數並指定現有轉換名稱，則轉換將在未確認的情況下替換。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立轉換 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-transformation] 一節。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

此 PowerShell 命令會在 StreamingJob 工作中建立名為 StreamingJobTransform 的新轉換。 如果已經使用此名稱定義現有的轉換，Cmdlet 會詢問是否要取代它。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

 此 PowerShell 命令會取代 StreamingJob 工作中 StreamingJobTransform 的定義。

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>刪除 Azure 流分析輸入 |刪除-阿茲特流分析輸入
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸入。  
如果指定 Force 參數，則輸入將在未確認的情況下被刪除。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

此 PowerShell 命令會移除 StreamingJob 工作中的 EventStream 輸入。  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>刪除 Azure 流分析作業 |刪除-阿茲特線上分析作業
以非同步方式從 Microsoft Azure 中刪除特定的 Stream Analytics 作業。  
如果指定 Force 參數，則作業將在未確認的情況下刪除。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

此 PowerShell 命令會移除 StreamingJob 工作。  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>刪除 Azure 流分析輸出 |刪除-阿茲特流分析輸出
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸出。  
如果指定 Force 參數，輸出將被刪除，無需確認。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

此 PowerShell 命令會移除 StreamingJob 工作中的 Output 輸出。  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>啟動-AzureStream分析作業 |啟動-阿茲特線上分析工作
以非同步方式部署和啟動 Microsoft Azure 中的 Stream Analytics 作業。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0：  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

此 PowerShell 命令會啟動自訂輸出開始時間設為 2012 年 12 月 12 日 12:12:12 UTC 的 StreamingJob 工作。

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>停止-Azure 流分析作業 |停止-阿茲特槽分析工作
以非同步方式停止在 Microsoft Azure 中執行 Stream Analytics 作業，並取消配置正在使用的資源。 透過 Azure 入口網站和管理 API 仍可使用訂用帳戶內的該工作定義與中繼資料，也因此可以編輯和重新啟動工作。 您將不需要對處於已停止狀態的工作支付費用。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

此 PowerShell 命令會停止 StreamingJob 工作。  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>測試-Azure 流分析輸入 |測試-阿茲特流分析輸入
測試 Stream Analytics 連線到指定輸入的能力。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

此 PowerShell 命令會測試 StreamingJob 中 EntryStream 輸入的連接狀態。  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>測試-Azure 流分析輸出 |測試-阿茲特流分析輸出
測試 Stream Analytics 連線到指定輸出的能力。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

此 PowerShell 命令會測試 StreamingJob 中 Output 輸出的連接狀態。  

## <a name="get-support"></a>取得支援
有關進一步説明，請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。 

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

