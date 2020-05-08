---
title: 針對 Azure Data Factory 進行疑難排解 |Microsoft Docs
description: 瞭解如何在 Azure Data Factory 中針對外部控制活動進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: f07cc109b21010df89b105576cb9afcf93df774a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744777"
---
# <a name="troubleshoot-azure-data-factory"></a>疑難排解 Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中外部控制活動的常見疑難排解方法。

## <a name="connector-and-copy-activity"></a>連接器和複製活動

如需連接器問題，例如使用複製活動時遇到錯誤，請參閱[疑難排解 Azure Data Factory 連接器](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>錯誤碼：3200

- **訊息**：錯誤403。

- **原因**：`The Databricks access token has expired.`

- **建議**：根據預設，Azure Databricks 存取權杖的有效時間為90天。 建立新的權杖，並更新連結的服務。

### <a name="error-code-3201"></a>錯誤碼：3201

- **訊息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建議**：在 Databricks 活動中指定筆記本路徑。

<br/> 

- **訊息**：`Cluster... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建議**：請確認 Databricks 叢集是否存在。

<br/> 

- **訊息**：`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建議**：針對工作區定址配置或`dbfs:/folder/subfolder/foo.py`儲存在 DATABRICKS 檔案系統（DFS）中的檔案，指定絕對路徑。

<br/> 

- **訊息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建議**：確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **訊息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建議**：確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **訊息**：`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建議**：請參閱錯誤訊息。

<br/>

### <a name="error-code-3202"></a>錯誤碼：3202

- **訊息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建議**：檢查使用此 Databricks 工作區的所有管線，以取得其作業建立速率。 如果管線啟動太多 Databricks 在匯總中執行，請將一些管線遷移至新的工作區。

<br/> 

- **訊息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建議**：檢查管線 JSON，並確保 baseParameters 筆記本中的所有參數都指定非空白值。

<br/> 

- **Message**： `User: `SimpleUserCoNtext {userId = ...，name =user@company.com，orgId = ...}` is not authorized to access cluster.`

- **原因**：產生存取權杖的使用者不能存取連結服務中指定的 Databricks 叢集。

- **建議**：請確認使用者具有工作區中的必要許可權。

### <a name="error-code-3203"></a>錯誤碼：3203

- **訊息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**：叢集已終止。 若為互動式叢集，此問題可能是競爭情形。

- **建議**：若要避免此錯誤，請使用作業叢集。

### <a name="error-code-3204"></a>錯誤碼：3204

- **訊息**：`Job execution failed.`

- **原因**：錯誤訊息指出各種問題，例如未預期的叢集狀態或特定的活動。 通常不會出現任何錯誤訊息。

- **建議**： N/A
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表適用于 U-SQL。
 
### <a name="error-code-2709"></a>錯誤碼：2709

- **訊息**：`The access token is from the wrong tenant.`

- **原因**：不正確的 Azure Active Directory （Azure AD）租使用者。

- **建議**：不正確的 Azure Active Directory （Azure AD）租使用者。

<br/>

- **訊息**：`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **原因**： Data Lake Analytics 上的節流導致此錯誤。

- **建議**：將已提交的工作數目減少為 Data Lake Analytics。 請變更活動 Data Factory 觸發程式和並行設定，或增加 Data Lake Analytics 的限制。

<br/> 

- **訊息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**： Data Lake Analytics 上的節流導致此錯誤。

- **建議**：將已提交的工作數目減少為 Data Lake Analytics。 請變更活動 Data Factory 觸發程式和並行設定，或增加 Data Lake Analytics 的限制。

### <a name="error-code-2705"></a>錯誤碼：2705

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。

### <a name="error-code-2711"></a>錯誤碼：2711

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。

<br/> 

- **訊息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**： U-SQL 檔案的路徑錯誤，或連結的服務認證沒有存取權。

- **建議**：確認連結服務中提供的路徑和認證。

### <a name="error-code-2704"></a>錯誤碼：2704

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。

### <a name="error-code-2707"></a>錯誤碼：2707

- **訊息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **原因**：連結服務中的 Data Lake Analytics 帳戶錯誤。

- **建議**：確認已提供正確的帳戶。

### <a name="error-code-2703"></a>錯誤碼：2703

- **訊息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **原因**：錯誤來自 Data Lake Analytics。

- **建議**：已將作業提交給 Data Lake Analytics，而在其中的腳本中，兩者皆失敗。 在 Data Lake Analytics 中進行調查。 在入口網站中，移至 [Data Lake Analytics] 帳戶，然後使用 [Data Factory 活動執行識別碼] 來尋找作業（請勿使用管線執行識別碼）。 作業會提供有關錯誤的詳細資訊，並協助您進行疑難排解。

   如果解決方法不清楚，請洽詢 Data Lake Analytics 支援小組，並提供工作通用資源定位器（URL），其中包含您的帳戶名稱和作業識別碼。
 
## <a name="azure-functions"></a>Azure 函式

### <a name="error-code-3602"></a>錯誤碼：3602

- **訊息**：`Invalid HttpMethod: '%method;'.`

- **原因**： Azure Function 活動不支援活動裝載中指定的 Httpmethod。

- **建議**：支援的由動作 HTTPmethods 為： PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。

### <a name="error-code-3603"></a>錯誤碼：3603

- **訊息**：`Response Content is not a valid JObject.`

- **原因**：呼叫的 Azure 函式未傳迴響應中的 JSON 承載。 Azure Data Factory （ADF） Azure function 活動僅支援 JSON 回應內容。

- **建議**：更新 Azure 函式以傳回有效的 JSON 承載，例如 c # 函數可能會傳回`(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>錯誤碼：3606

- **訊息**： Azure 函式活動缺少功能金鑰。

- **原因**： Azure 函式活動定義不完整。

- **建議**：請確認輸入 Azure FUNCTION 活動 JSON 定義具有名為`functionKey`的屬性。

### <a name="error-code-3607"></a>錯誤碼：3607

- **訊息**：`Azure function activity missing function name.`

- **原因**： Azure 函式活動定義不完整。

- **建議**：請確認輸入 Azure FUNCTION 活動 JSON 定義具有名為`functionName`的屬性。

### <a name="error-code-3608"></a>錯誤碼：3608

- **訊息**：`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**：活動定義中的 Azure 函數詳細資料可能不正確。

- **建議**：修正 Azure function 詳細資料，然後再試一次。

### <a name="error-code-3609"></a>錯誤碼：3609

- **訊息**：`Azure function activity missing functionAppUrl.`

- **原因**： Azure 函式活動定義不完整。

- **建議**：請確認輸入 Azure FUNCTION 活動 JSON 定義具有名為`functionAppUrl`的屬性。

### <a name="error-code-3610"></a>錯誤碼：3610

- **訊息**：`There was an error while calling endpoint.`

- **原因**：函數 URL 可能不正確。

- **建議**：請確認活動 JSON `functionAppUrl`中的值正確無誤，然後再試一次。

### <a name="error-code-3611"></a>錯誤碼：3611

- **訊息**：`Azure function activity missing Method in JSON.`

- **原因**： Azure 函式活動定義不完整。

- **建議**：請確認輸入 Azure FUNCTION 活動 JSON 定義具有名為`method`的屬性。

### <a name="error-code-3612"></a>錯誤碼：3612

- **訊息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**： Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動 JSON 定義是否有已連結的服務詳細資料。

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>錯誤碼：4101

- **訊息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**：屬性`%propertyName;`的格式不正確或遺失。

- **建議**：檢查活動`%activityName;`是否已使用正確的`%propertyName;`資料定義屬性。

### <a name="error-code-4110"></a>錯誤碼：4110

- **訊息**：`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**： AzureMLExecutePipeline 活動定義不完整。

- **建議**：檢查輸入 AZUREMLEXECUTEPIPELINE 活動 JSON 定義是否已正確連結服務詳細資料。

### <a name="error-code-4111"></a>錯誤碼：4111

- **訊息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**：活動定義不正確。

- **建議**：檢查輸入 AZUREMLEXECUTEPIPELINE 活動 JSON 定義是否已正確連結服務詳細資料。

### <a name="error-code-4112"></a>錯誤碼：4112

- **訊息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**：屬性 '% propertyName; ' 的格式不正確或遺失。

- **建議**：檢查已連結的服務是否已使用`%propertyName;`正確的資料定義屬性。

### <a name="error-code-4121"></a>錯誤碼：4121

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：用來存取 Azure Machine Learning 的認證已過期。

- **建議**：請確認認證有效，然後再試一次。

### <a name="error-code-4122"></a>錯誤碼：4122

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**： Azure Machine Learning 連結服務中提供的認證無效，或沒有操作的許可權。

- **建議**：請確認已連結服務中的認證有效，而且具有存取 Azure Machine Learning 的許可權。

### <a name="error-code-4123"></a>錯誤碼：4123

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：活動的屬性（ `pipelineParameters`例如）對 Azure Machine Learning （ML）管線而言無效。

- **建議**：檢查活動屬性的值是否符合連結服務中所指定已發佈 Azure ML 管線的預期裝載。

### <a name="error-code-4124"></a>錯誤碼：4124

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：發佈的 Azure ML 管線端點不存在。

- **建議**：確認連結服務中指定的已發佈 Azure Machine Learning 管線端點存在於 Azure Machine Learning 中。

### <a name="error-code-4125"></a>錯誤碼：4125

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**： Azure Machine Learning 上發生伺服器錯誤。

- **建議**：請稍後重試。 如果問題持續發生，請洽詢 Azure Machine Learning 小組以取得協助。

### <a name="error-code-4126"></a>錯誤碼：4126

- **訊息**：`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **原因**： Azure ML 管線執行失敗。

- **建議**：請檢查 Azure Machine Learning 以取得更多錯誤記錄，然後修正 ML 管線。

## <a name="common"></a>通用

### <a name="error-code-2103"></a>錯誤碼：2103

- **訊息**：`Please provide value for the required property '%propertyName;'.`

- **原因**：尚未提供屬性所需的值。

- **建議**：提供來自訊息的值，然後再試一次。

### <a name="error-code-2104"></a>錯誤碼：2104

- **訊息**：`The type of the property '%propertyName;' is incorrect.`

- **原因**：提供的屬性類型不正確。

- **建議**：修正屬性的類型，然後再試一次。

### <a name="error-code-2105"></a>錯誤碼：2105

- **訊息**：`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**：屬性的值無效，或不是預期的格式。

- **建議**：請參閱屬性的檔，並確認提供的值包含正確的格式和類型。

### <a name="error-code-2106"></a>錯誤碼：2106

- **訊息**：`The storage connection string is invalid. %errorMessage;`

- **原因**：儲存體的連接字串無效或格式不正確。

- **建議**：請移至 Azure 入口網站並尋找您的儲存體，然後將連接字串複製並貼到您的連結服務中，然後再試一次。

### <a name="error-code-2108"></a>錯誤碼：2108

- **訊息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：要求已因基本問題（例如網路連線、DNS 失敗、伺服器憑證驗證或超時）而失敗。

- **建議**：使用 Fiddler/Postman 來驗證要求。

### <a name="error-code-2110"></a>錯誤碼：2110

- **訊息**：`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**：活動中指定的連結服務不正確。

- **建議**：請確認已連結的服務類型是活動的其中一個支援的類型。 例如，HDI 活動的連結服務類型可以是 HDInsight 或 HDInsightOnDemand。

### <a name="error-code-2111"></a>錯誤碼：2111

- **訊息**：`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**：提供的屬性類型不正確。

- **建議**：修正屬性類型，然後再試一次。

### <a name="error-code-2112"></a>錯誤碼：2112

- **訊息**：`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**：雲端類型不受支援，或無法從 EndpointSuffix 判斷存放裝置。

- **建議**：請使用另一個雲端中的儲存體，然後再試一次。

### <a name="error-code-2128"></a>錯誤碼：2128

- **訊息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：網路連線、DNS 失敗、伺服器憑證驗證或超時。

- **建議**：驗證您嘗試叫用的端點是否正在回應要求。 您可以使用 Fiddler/Postman 之類的工具。

## <a name="custom"></a>自訂

下表適用于 Azure Batch。
 
### <a name="error-code-2500"></a>錯誤碼：2500

- **訊息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建議**：請確認可執行檔是否存在。 如果程式已啟動，請確認*stdout*和*stderr*已上傳至儲存體帳戶。 在您的程式碼中包含記錄以進行調試是個很好的作法。

### <a name="error-code-2501"></a>錯誤碼：2501

- **訊息**：`Cannot access user batch account; please check batch account settings.`

- **原因**：不正確的批次存取金鑰或集區名稱。

- **建議**：確認連結服務中的集區名稱和批次存取金鑰。

### <a name="error-code-2502"></a>錯誤碼：2502

- **訊息**：`Cannot access user storage account; please check storage account settings.`

- **原因**：儲存體帳戶名稱或存取金鑰不正確。

- **建議**：驗證已連結服務中的儲存體帳戶名稱和存取金鑰。

### <a name="error-code-2504"></a>錯誤碼：2504

- **訊息**：`Operation returned an invalid status code 'BadRequest'.`

- **原因**：自訂活動的`folderPath`中有太多檔案。 的總大小`resourceFiles`不能超過32768個字元。

- **建議**：移除不必要的檔案，或將其壓縮，並新增解壓縮命令以將其解壓縮。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>錯誤碼：2505

- **訊息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**：自訂活動僅支援使用存取金鑰的儲存體帳戶。

- **建議**：請參閱錯誤描述。

### <a name="error-code-2507"></a>錯誤碼：2507

- **訊息**：`The folder path does not exist or is empty: ...`

- **原因**：位於指定路徑的儲存體帳戶中沒有任何檔案。

- **建議**：資料夾路徑必須包含您想要執行的可執行檔。

### <a name="error-code-2508"></a>錯誤碼：2508

- **訊息**：`There are duplicate files in the resource folder.`

- **原因**：相同名稱的多個檔案位於 folderPath 的不同子資料夾中。

- **建議**：自訂活動會在 folderPath 下壓平合併資料夾結構。 如果您需要保留資料夾結構，請壓縮檔案，並使用解壓縮命令在 Azure Batch 中將檔案解壓縮。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>錯誤碼：2509

- **訊息**：`Batch url ... is invalid; it must be in Uri format.`

- **原因**：批次 url 必須類似于`https://mybatchaccount.eastus.batch.azure.com`

- **建議**：請參閱錯誤描述。

### <a name="error-code-2510"></a>錯誤碼：2510

- **訊息**：`An error occurred while sending the request.`

- **原因**：批次 URL 無效。

- **建議**：驗證批次 URL。
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-200"></a>錯誤碼：200

- **訊息**：`Unexpected error happened: '%error;'.`

- **原因**：發生內部服務問題。

- **建議**：如需進一步協助，請聯絡 ADF 支援。

### <a name="error-code-201"></a>錯誤碼：201

- **訊息**：`JobType %jobType; is not found.`

- **原因**： ADF 不支援新的工作類型。

- **建議**：請洽詢 ADF 支援小組以取得進一步協助。

### <a name="error-code-202"></a>錯誤碼：202

- **訊息**：`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息包含發生錯誤的詳細資料。

- **建議**：錯誤訊息的詳細資料應協助您針對問題進行疑難排解。 如果沒有足夠的資訊，請聯絡 ADF 支援以取得進一步的協助。

### <a name="error-code-203"></a>錯誤碼：203

- **訊息**：`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息包含發生錯誤的詳細資料。

- **建議**：錯誤訊息的詳細資料應協助您針對問題進行疑難排解。 如果沒有足夠的資訊，請聯絡 ADF 支援以取得進一步的協助。

### <a name="error-code-204"></a>錯誤碼：204

- **訊息**：`The resumption token is missing for runId '%runId;'.`

- **原因**：發生內部服務問題。

- **建議**：如需進一步協助，請聯絡 ADF 支援。

### <a name="error-code-205"></a>錯誤碼：205

- **訊息**：`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **原因**：建立 HDI 隨選叢集時發生錯誤。

- **建議**：如需進一步協助，請聯絡 ADF 支援。

### <a name="error-code-206"></a>錯誤碼：206

- **訊息**：`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **原因**：造成此錯誤的服務發生內部問題。

- **建議**：此問題可能是暫時性的。 請重試您的作業，如果問題持續發生，請洽詢 ADF 支援以取得進一步的協助。

### <a name="error-code-207"></a>錯誤碼：207

- **訊息**：`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **原因**：嘗試從主要儲存體帳戶判斷區域時發生內部錯誤。

- **建議**：嘗試另一個儲存體。 如果此選項不是可接受的解決方案，請聯絡 ADF 支援小組以取得進一步協助。

### <a name="error-code-208"></a>錯誤碼：208

- **訊息**：`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **原因**：嘗試讀取服務主體或具現化 MSI 驗證時發生內部錯誤。

- **建議**：請考慮提供服務主體，其具有在所提供訂用帳戶中建立 HDInsight 叢集的許可權，然後再試一次。 確認[已正確設定 [管理](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities)身分識別]。

   如果此選項不是可接受的解決方案，請聯絡 ADF 支援小組以取得進一步協助。

### <a name="error-code-2300"></a>錯誤碼：2300

- **訊息**：`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **原因**：錯誤訊息包含類似的訊息`The remote name could not be resolved.`。 提供的叢集 URI 可能無效。

- **建議**：請確認叢集尚未刪除，而且提供的 URI 正確。 當您在瀏覽器中開啟 URI 時，您應該會看到 Ambari UI。 如果叢集位於虛擬網路中，則 URI 應該是私用 URI。 若要開啟它，請使用屬於相同虛擬網路的虛擬機器（VM）。

   如需詳細資訊，請參閱[直接連接到 Apache Hadoop 服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)。
 
 </br>

- **原因**：如果錯誤訊息包含類似的訊息`A task was canceled.`，表示作業提交超時。

- **建議**：問題可能是一般 HDInsight 連線能力或網路連線能力。 請先確認可以從任何瀏覽器取得 HDInsight Ambari UI。 然後檢查您的認證是否仍然有效。
   
   如果您使用自我裝載整合執行時間（IR），請從已安裝自我裝載 IR 的 VM 或電腦執行此步驟。 然後再次嘗試從 Data Factory 提交工作。 如果仍然失敗，請洽詢 Data Factory 小組以取得支援。

   如需詳細資訊，請參閱[Ambari WEB UI](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui)。

 </br>

- **原因**：當錯誤訊息包含類似`User admin is locked out in Ambari`或`Unauthorized: Ambari user name or password is incorrect`的訊息時，HDInsight 的認證不正確或已過期。

- **建議**：更正認證並重新部署已連結的服務。 首先，請在任何瀏覽器上開啟叢集 URI 並嘗試登入，以確認認證可在 HDInsight 上工作。 如果認證無法使用，您可以從 Azure 入口網站重設它們。

   針對 ESP 叢集，請透過[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password)重設密碼。

 </br>

- **原因**：當錯誤訊息包含類似的訊息時`502 - Web server received an invalid response while acting as a gateway or proxy server`，HDInsight 服務會傳回此錯誤。

- **建議**：當您的 Ambari 伺服器進程關閉時，通常會發生502錯誤。 您可以重新開機 Ambari 服務，方法是重新開機前端節點。

    1. 使用 SSH 連接到 HDInsight 上的其中一個節點。
    1. 執行`ping headnodehost`，以識別您的使用中前端節點主機。
    1. 使用 SSH，連線到作用中的前端節點，因為 Ambari 伺服器位於作用中的前端節點上。 
    1. 重新開機使用中的前端節點。

       如需詳細資訊，請參閱 Azure HDInsight 疑難排解檔。 例如：

       * [Ambari UI 502 錯誤](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [適用于 Apache Spark thrift 伺服器的 RpcTimeoutException](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception)
       * [疑難排解應用程式閘道中的閘道錯誤](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

 </br>

- **原因**：當錯誤訊息包含類似`Unable to service the submit job request as templeton service is busy with too many submit job requests`或`Queue root.joblauncher already has 500 applications, cannot accept submission of application`的訊息時，會同時將太多作業提交到 HDInsight。

- **建議**：限制提交至 HDInsight 的並行作業數目。 如果作業是由相同的活動所提交，請參閱 Data Factory 活動並行。 變更觸發程式，讓並行管線執行會隨著時間分散。

   如錯誤所建議，請`templeton.parallellism.job.submit`參閱[HDInsight 檔](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors)以進行調整。

### <a name="error-code-2301"></a>錯誤碼：2301

- **訊息**：`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**： HDInsight 叢集或服務有問題。

- **建議**：當 ADF 在嘗試要求執行中作業的狀態時，不會收到來自 HDInsight 叢集的回應時，就會發生此錯誤。 此問題可能是叢集本身，或 HDInsight 服務可能發生中斷。

   請參閱的 HDInsight 疑難排解檔https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide，或洽詢其支援以取得進一步的協助。

### <a name="error-code-2302"></a>錯誤碼：2302

- **訊息**：`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**：作業已提交至 HDI 叢集，並在該處失敗。

- **建議**： 

 1. 檢查 Ambari UI：
    1. 確定所有服務仍在執行中。
    1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
       1. 如需警示和警示解決方式的詳細資訊，請參閱[管理和監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)叢集。
    1. 檢查您的 YARN 記憶體。 如果您的 YARN 記憶體偏高，可能會延遲處理您的作業。 如果您的資源不足以容納 Spark 應用程式/作業，請相應增加叢集，以確保叢集具有足夠的記憶體和核心。 
 1. 執行範例測試作業。
    1. 如果您在 HDInsight 後端上執行相同的作業，請確認它是否成功。 如需範例執行的範例，請參閱[執行包含在 HDInsight 中的 MapReduce 範例](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. 如果 HDInsight 上的作業仍失敗，請檢查應用程式記錄檔和資訊，以提供支援：
    1. 檢查是否已將作業提交至 YARN。 如果作業未提交至 yarn，請使用`--master yarn`。
    1. 如果應用程式已完成執行，請收集 YARN 應用程式的開始時間和結束時間。 如果應用程式未完成執行，請收集開始時間/啟動時間。
    1. 使用`yarn logs -applicationId <Insert_Your_Application_ID>`檢查並收集應用程式記錄檔。
    1. 檢查並收集`/var/log/hadoop-yarn/yarn`目錄底下的 yarn Resource Manager 記錄檔。
    1. 如果這些步驟不足以解決問題，請聯絡 Azure HDInsight 小組以取得支援，並提供上述記錄和時間戳記。

### <a name="error-code-2303"></a>錯誤碼：2303

- **訊息**：`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**：作業已提交至 HDI 叢集，並在該處失敗。

- **建議**： 

 1. 檢查 Ambari UI：
    1. 確定所有服務仍在執行中。
    1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
       1. 如需警示和警示解決方式的詳細資訊，請參閱[管理和監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)叢集。
    1. 檢查您的 YARN 記憶體。 如果您的 YARN 記憶體偏高，可能會延遲處理您的作業。 如果您的資源不足以容納 Spark 應用程式/作業，請相應增加叢集，以確保叢集具有足夠的記憶體和核心。 
 1. 執行範例測試作業。
    1. 如果您在 HDInsight 後端上執行相同的作業，請確認它是否成功。 如需範例執行的範例，請參閱[執行包含在 HDInsight 中的 MapReduce 範例](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. 如果 HDInsight 上的作業仍失敗，請檢查應用程式記錄檔和資訊，以提供支援：
    1. 檢查是否已將作業提交至 YARN。 如果作業未提交至 yarn，請使用`--master yarn`。
    1. 如果應用程式已完成執行，請收集 YARN 應用程式的開始時間和結束時間。 如果應用程式未完成執行，請收集開始時間/啟動時間。
    1. 使用`yarn logs -applicationId <Insert_Your_Application_ID>`檢查並收集應用程式記錄檔。
    1. 檢查並收集`/var/log/hadoop-yarn/yarn`目錄底下的 yarn Resource Manager 記錄檔。
    1. 如果這些步驟不足以解決問題，請聯絡 Azure HDInsight 小組以取得支援，並提供上述記錄和時間戳記。

### <a name="error-code-2304"></a>錯誤碼：2304

- **訊息**：`MSI authentication is not supported on storages for HDI activities.`

- **原因**：在 HDINSIGHT （HDI）連結服務或 HDI 活動中使用的儲存體連結服務，是以不受支援的 MSI 驗證進行設定。

- **建議**：針對 HDI 連結服務或 HDI 活動中所使用的儲存體帳戶，提供完整的連接字串。

### <a name="error-code-2305"></a>錯誤碼：2305

- **訊息**：`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**： HDI 叢集的連線資訊不正確、提供的使用者沒有執行必要動作的許可權，或 HDInsight 服務在回應 ADF 的要求時發生問題。

- **建議**：請確認使用者資訊是否正確，以及是否可以從已安裝 IR 的 VM （適用于自我裝載 ir）的瀏覽器中開啟 HDI 叢集的 Ambari UI，或從任何電腦開啟（適用于 Azure IR）。

### <a name="error-code-2306"></a>錯誤碼：2306

- **訊息**：`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**：為腳本動作提供的 JSON 無效。

- **建議**：錯誤訊息應該有助於找出問題。 請修正 json 設定，然後再試一次。

   如需詳細資訊，請參閱[Azure HDInsight 隨選連結服務](https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service)。

### <a name="error-code-2310"></a>錯誤碼：2310

- **訊息**：`Failed to submit Spark job. Error: '%message;'`

- **原因**： ADF 嘗試使用 Livy API （Livy/batch）在 Spark 叢集上建立批次，但收到錯誤。

- **建議**：請遵循錯誤訊息來修正問題。 如果沒有足夠的資訊可讓它解析，請洽詢 HDI 小組，並為他們提供 batch 識別碼和作業識別碼，這可以在 [ADF 監視] 的 [活動執行輸出] 頁面中找到。 若要進一步進行疑難排解，請收集批次作業的完整記錄檔。

   如需如何收集完整記錄檔的詳細資訊，請參閱[取得批次作業的完整記錄](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2312"></a>錯誤碼：2312

- **訊息**：`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**： HDInsight Spark 叢集上的作業失敗。

- **建議**：遵循 [ADF 監視] 頁面中 [活動執行輸出] 中的連結，針對 HDInsight Spark 叢集上的執行進行疑難排解。 請洽詢 HDInsight 支援小組以取得進一步的協助。

   如需如何收集完整記錄檔的詳細資訊，請參閱[取得批次作業的完整記錄](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2313"></a>錯誤碼：2313

- **訊息**：`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**：已刪除 HDInsight Spark 叢集上的批次。

- **建議**：針對 HDInsight Spark 叢集上的批次進行疑難排解。 請聯絡 HDInsight 支援以取得進一步的協助。 

   如需有關如何收集完整記錄檔的詳細資訊，請參閱[取得批次作業的完整記錄](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)，並與 HDInsight 共用完整記錄檔支援以取得進一步的協助。

### <a name="error-code-2328"></a>錯誤碼：2328

- **訊息**：`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息應該會顯示發生錯誤的詳細資料。

- **建議**：錯誤訊息應該有助於疑難排解問題。

### <a name="error-code-2329"></a>錯誤碼：2329

- **訊息**：`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息應該會顯示發生錯誤的詳細資料。

- **建議**：錯誤訊息應該有助於疑難排解問題。

### <a name="error-code-2331"></a>錯誤碼：2331

- **訊息**：`The file path should not be null or empty.`

- **原因**：提供的檔案路徑是空的。

- **建議**：提供存在之檔案的路徑。

### <a name="error-code-2340"></a>錯誤碼：2340

- **訊息**：`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**： HDInsightOnDemand 連結服務不支援透過 SelfHosted IR 執行。

- **建議**：請選取 Azure IR，然後再試一次。

### <a name="error-code-2341"></a>錯誤碼：2341

- **訊息**：`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**：提供的 URL 格式不正確。

- **建議**：修正叢集 URL，然後再試一次。

### <a name="error-code-2342"></a>錯誤碼：2342

- **訊息**：`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**：提供的認證對叢集而言錯誤，或發生網路設定或連線問題，或 IR 連線到叢集時發生問題。

- **建議**： 
    1. 在瀏覽器中開啟 HDInsight 叢集的 Ambari UI，以確認認證是否正確。
    1. 如果叢集位於虛擬網路（VNet）中，而且正在使用自我裝載 IR，則 HDI URL 必須是 Vnet 中的私人 URL，且在叢集名稱後面應該會列出 '-int '。
    
       例如，將 `https://mycluster.azurehdinsight.net/` 變更為 `https://mycluster-int.azurehdinsight.net/`。 請注意`-int` after `mycluster`之後，但之前`.azurehdinsight.net`
    1. 如果叢集在 VNet 中，則會使用自我裝載 IR，並使用私人 URL，但連線仍會失敗，而安裝 IR 的 VM 就會發生連接到 HDI 的問題。 
    
       連接到已安裝 IR 的 VM，並在瀏覽器中開啟 Ambari UI。 使用叢集的私人 URL。 此連接應該可從瀏覽器執行。 如果沒有，請洽詢 HDInsight 支援小組以取得進一步的協助。
    1. 如果未使用自我裝載 IR，則 HDI 叢集應可公開存取。 在瀏覽器中開啟 Ambari UI，並檢查它是否已開啟。 如果叢集或其上的服務有任何問題，請洽詢 HDInsight 支援小組以取得協助。

       Adf IR （自我裝載或 Azure）必須能夠存取 adf 的已連結服務中所使用的 HDI 叢集 URL，才能讓測試連線通過，並讓執行作業正常運作。 您可以從 [VM] 或任何公用電腦的瀏覽器開啟 URL，以驗證此狀態。

### <a name="error-code-2343"></a>錯誤碼：2343

- **訊息**：`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**：使用者名稱或密碼是空的。

- **建議**：請提供正確的認證以連接到 HDI，然後再試一次。

### <a name="error-code-2345"></a>錯誤碼：2345

- **訊息**：`Failed to read the content of the hive script. Error: '%message;'`

- **原因**：腳本檔案不存在，或 ADF 無法連接到腳本的位置。

- **建議**：請確認腳本存在，而且相關聯的連結服務具有適當的連接認證。

### <a name="error-code-2346"></a>錯誤碼：2346

- **訊息**：`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**： ADF 嘗試建立與 HDI 叢集的開放式資料庫連接（ODBC）連線，但發生錯誤而失敗。

- **建議**： 

   1. 確認您已正確設定 ODBC/JAVA 資料庫連線（JDBC）連接。
      1. 針對 JDBC，如果您使用相同的虛擬網路，您可以從下列網站取得此連接：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要確定您已設定正確的 JDBC，請參閱[透過 HDInsight 中的 JDBC 驅動程式查詢 Apache Hive](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver)。
      1. 針對 Open Database （ODB），請參閱[教學課程：使用 ODBC 和 PowerShell 查詢 Apache Hive](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) ，以確保您擁有正確的設定。 
   1. 確認 [Hiveserver2]、[Hive 中繼存放區] 和 [Hiveserver2 Interactive] 是作用中且正常運作。 
   1. 檢查 Ambari 使用者介面（UI）：
      1. 確定所有服務仍在執行中。
      1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
         1. 如需警示和警示解決方式的詳細資訊，請參閱[管理和監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)叢集。
   1. 如果這些步驟不足以解決問題，請洽詢 Azure HDInsight 小組。

### <a name="error-code-2347"></a>錯誤碼：2347

- **訊息**：`Hive execution through ODBC failed with error message '%message;'.`

- **原因**： ADF 已透過 ODBC 連接提交 hive 腳本以執行至 HDI 叢集，且腳本在 HDI 上失敗。

- **建議**： 

   1. 確認您已正確設定 ODBC/JAVA 資料庫連線（JDBC）連接。
      1. 針對 JDBC，如果您使用相同的虛擬網路，您可以從下列網站取得此連接：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要確定您已設定正確的 JDBC，請參閱[透過 HDInsight 中的 JDBC 驅動程式查詢 Apache Hive](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver)。
      1. 針對 Open Database （ODB），請參閱[教學課程：使用 ODBC 和 PowerShell 查詢 Apache Hive](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) ，以確保您擁有正確的設定。 
   1. 確認 [Hiveserver2]、[Hive 中繼存放區] 和 [Hiveserver2 Interactive] 是作用中且正常運作。 
   1. 檢查 Ambari 使用者介面（UI）：
      1. 確定所有服務仍在執行中。
      1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
         1. 如需警示和警示解決方式的詳細資訊，請參閱[管理和監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)叢集。
   1. 如果這些步驟不足以解決問題，請洽詢 Azure HDInsight 小組。

### <a name="error-code-2348"></a>錯誤碼：2348

- **訊息**：`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**：儲存體連結服務屬性未正確設定。

- **建議**： HDI 活動的主要儲存體連結服務僅支援完整連接字串。 確認您未使用 MSI 授權或應用程式。

### <a name="error-code-2350"></a>錯誤碼：2350

- **訊息**：`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**：提供用來連接到檔案所在之儲存體的認證不正確，或檔案不存在於該處。

- **建議**：當 ADF 準備 HDI 活動時，會發生此錯誤，並嘗試在將作業提交至 HDI 之前，先將檔案複製到主要儲存體。 檢查檔案是否存在於提供的位置，以及儲存體連線是否正確。 由於 ADF HDI 活動不支援在與 HDI 活動相關的儲存體帳戶上進行 MSI 驗證，因此請確認那些連結服務具有完整金鑰，或使用 Azure Key Vault。

### <a name="error-code-2351"></a>錯誤碼：2351

- **訊息**：`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**：檔案不存在於指定的路徑。

- **建議**：檢查檔案是否確實存在，以及指向此檔案的連結服務是否有正確的認證。

### <a name="error-code-2352"></a>錯誤碼：2352

- **訊息**：`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**：檔案儲存體連結服務屬性未正確設定。

- **建議**：確認已正確設定檔案儲存體連結服務的屬性。

### <a name="error-code-2353"></a>錯誤碼：2353

- **訊息**：`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**：未正確設定腳本儲存體連結服務屬性。

- **建議**：確認已正確設定腳本儲存體連結服務的屬性。

### <a name="error-code-2354"></a>錯誤碼：2354

- **訊息**：`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**：活動不支援儲存體連結服務類型。

- **建議**：請確認選取的連結服務有其中一個支援的活動類型。 HDI 活動支援 AzureBlobStorage 和 AzureBlobFSStorage 連結服務。

   如需詳細資訊，請參閱[比較與 Azure HDInsight 叢集搭配使用的儲存體選項](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)

### <a name="error-code-2355"></a>錯誤碼：2355

- **訊息**：`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**：提供的值`commandEnvironment`不正確。

- **建議**：請確認提供的值類似于：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    也請確認每個變數只會出現在清單中一次。

### <a name="error-code-2356"></a>錯誤碼：2356

- **訊息**：`The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**：提供的值`commandEnvironment`不正確。

- **建議**：請確認提供的值類似于：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    也請確認每個變數只會出現在清單中一次。

### <a name="error-code-2357"></a>錯誤碼：2357

- **訊息**：`The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**：提供的認證不正確。

- **建議**：請確認 ADLS Gen 1 中的連接資訊已連結至服務，並確認測試連接是否成功。

### <a name="error-code-2358"></a>錯誤碼：2358

- **訊息**：`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**：為必要屬性`TimeToLive`提供的值具有不正確格式。 

- **建議**：將值更新為建議的範圍，然後再試一次。

### <a name="error-code-2359"></a>錯誤碼：2359

- **訊息**：`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**：提供的屬性`roles`值無效。

- **建議**：將值更新為其中一個建議，然後再試一次。

### <a name="error-code-2360"></a>錯誤碼：2360

- **訊息**：`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**：提供的連接字串`HCatalogLinkedService`無效。

- **建議**：將值更新為正確的 Azure SQL 連接字串，然後再試一次。

### <a name="error-code-2361"></a>錯誤碼：2361

- **訊息**：`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**：叢集建立失敗，且 ADF 未從 HDInsight 服務取得錯誤。

- **建議**：開啟 Azure 入口網站，並嘗試尋找具有所提供名稱的 HDI 資源，然後檢查布建狀態。 請洽詢 HDInsight 支援小組以取得進一步的協助。

### <a name="error-code-2362"></a>錯誤碼：2362

- **訊息**：`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**：提供的額外儲存體不是 Azure Blob 儲存體。

- **建議**：提供 Azure Blob 儲存體帳戶作為 HDInsight 隨選連結服務的額外儲存體。

## <a name="web-activity"></a>網路活動

### <a name="error-code-2128"></a>錯誤碼：2128

- **訊息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：此問題的原因是網路連線、DNS 失敗、伺服器憑證驗證或超時。

- **建議**：驗證您嘗試叫用的端點是否正在回應要求。 您可以使用**Fiddler/Postman**之類的工具。

### <a name="error-code-2108"></a>錯誤碼：2108

- **訊息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：要求已因基本問題（例如網路連線、DNS 失敗、伺服器憑證驗證或超時）而失敗。

- **建議**：使用 Fiddler/Postman 來驗證要求。

#### <a name="more-details"></a>其他詳細資訊
若要使用**Fiddler**來建立受監視 web 應用程式的 HTTP 會話：

1. 下載、安裝及開啟[Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果您的 web 應用程式使用 HTTPs，請前往 [**工具** > ] [**Fiddler 選項** > ] [**HTTPs**]。

   1. 在 [HTTPS] 索引標籤中，選取 [ **CAPTURE HTTPs** connect] 和 [**解密 HTTPs 流量**]。

      ![Fiddler 選項](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果您的應用程式使用 TLS/SSL 憑證，請將 Fiddler 憑證新增至您的裝置。

   移至：**工具** > **Fiddler 選項** > **HTTPS** > **動作** > **將根憑證匯出到桌面**。

1. 前往 [檔案] [Capture] [ > **流量** **] 來關閉**捕捉。 或按**F12**。

1. 清除瀏覽器的快取，以便移除所有快取的專案，而且必須重新下載。

1. 建立要求：

1. 選取 [**編輯器**] 索引標籤。

   1. 設定 HTTP 方法和 URL。
 
   1. 如有需要，請新增標頭和要求主體。

   1. 選取 [**執行**]。

1. 再次開啟流量捕捉，並在您的頁面上完成有問題的交易。

1. 移**至：** > [檔案] [**儲存** > **所有會話**]。

如需詳細資訊，請參閱[開始使用 Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：

* [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
* [Azure 影片](https://azure.microsoft.com/resources/videos/index/)
* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home)
