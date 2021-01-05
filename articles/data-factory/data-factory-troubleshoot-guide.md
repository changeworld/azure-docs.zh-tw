---
title: 針對 Azure Data Factory 進行疑難排解 | Microsoft Docs
description: 了解如何針對 Azure Data Factory 中的外部控制活動進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 922ec6c4b579a657e7ee5e872148f8126ce175e2
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822279"
---
# <a name="troubleshoot-azure-data-factory"></a>針對 Azure Data Factory 進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將探討 Azure Data Factory 中外部控制活動的常見疑難排解方法。

## <a name="connector-and-copy-activity"></a>連接器和複製活動

針對連接器問題 (例如使用複製活動時發生錯誤)，請參閱[針對 Azure Data Factory 連接器進行疑難排解](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>錯誤碼：3200

- **訊息**：錯誤 403。

- **原因**：`The Databricks access token has expired.`

- **建議**：根據預設，Azure Databricks 存取權杖的有效期限為 90天。 請建立新的權杖，並更新連結的服務。

### <a name="error-code-3201"></a>錯誤碼：3201

- **訊息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建議**：在 Databricks 活動中指定筆記本路徑。

<br/> 

- **訊息**：`Cluster... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建議**：確認 Databricks 叢集存在。

<br/> 

- **訊息**：`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建議**：針對工作區定址配置指定絕對路徑，或針對儲存在 Databricks 檔案系統 (DFS) 中的檔案指定 `dbfs:/folder/subfolder/foo.py`。

<br/> 

- **訊息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建議**：確認 [連結服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **訊息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建議**：確認 [連結服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **訊息**：`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建議**：請參閱錯誤訊息。

<br/>

### <a name="error-code-3202"></a>錯誤碼：3202

- **訊息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建議**：檢查使用此 Databricks 工作區的所有管線，以了解其作業建立比率。 如果管線啟動的 Databricks 執行總數太多，請將一些管線遷移至新的工作區。

<br/> 

- **訊息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建議**：檢查管線 JSON，並確保 baseParameters 筆記本中的所有參數都指定非空白值。

<br/> 

- **訊息**：`User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **原因**︰產生存取權杖的使用者不允許存取連結服務中指定的 Databricks 叢集。

- **建議**：請確定使用者具有工作區中的必要權限。

### <a name="error-code-3203"></a>錯誤碼：3203

- **訊息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**︰叢集已終止。 若為互動式叢集，此問題可能是因為競爭條件。

- **建議**：若要避免此錯誤，請使用作業叢集。

### <a name="error-code-3204"></a>錯誤碼：3204

- **訊息**：`Job execution failed.`

- **原因**︰錯誤訊息指出各種問題，例如未預期的叢集狀態或特定活動。 通常不會出現任何錯誤訊息。

- **建議**：N/A

### <a name="error-code-3208"></a>錯誤碼：3208

- **訊息**：`An error occurred while sending the request.`

- **原因**： Databricks 服務的網路連接已中斷。

- **建議**：如果您使用的是自我裝載的整合執行時間，請確定網路連線是由 integration runtime 節點可靠。 如果您使用的是 Azure integration runtime，重試通常可以運作。
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表適用於 U-SQL。
 
### <a name="error-code-2709"></a>錯誤碼：2709

- **訊息**：`The access token is from the wrong tenant.`

- **原因**︰不正確的 Azure Active Directory (Azure AD) 租用戶。

- **建議**：不正確的 Azure Active Directory (Azure AD) 租用戶。

<br/>

- **訊息**：`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **原因**︰此錯誤是因為 Data Lake Analytics 上的節流所造成。

- **建議**：將提交至 Data Lake Analytics 的工作數目減少。 請變更活動上的 Data Factory 觸發程序和並行設定，或放寬 Data Lake Analytics 的限制。

<br/> 

- **訊息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**︰此錯誤是因為 Data Lake Analytics 上的節流所造成。

- **建議**：將提交至 Data Lake Analytics 的工作數目減少。 請變更活動上的 Data Factory 觸發程序和並行設定，或放寬 Data Lake Analytics 的限制。

### <a name="error-code-2705"></a>錯誤碼：2705

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以存取 Data Lake Analytics 帳戶，以及根資料夾中的預設 Data Lake Storage 執行個體。

### <a name="error-code-2711"></a>錯誤碼：2711

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以存取 Data Lake Analytics 帳戶，以及根資料夾中的預設 Data Lake Storage 執行個體。

<br/> 

- **訊息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**︰U-SQL 檔案的路徑錯誤，或連結的服務認證沒有存取權。

- **建議**：請確認連結服務中提供的路徑和認證。

### <a name="error-code-2704"></a>錯誤碼：2704

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：確認使用者為 Data Lake Analytics 作業提供的服務主體或憑證可以存取 Data Lake Analytics 帳戶，以及根資料夾中的預設 Data Lake Storage 執行個體。

### <a name="error-code-2707"></a>錯誤碼：2707

- **訊息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **原因**︰連結服務中的 Data Lake Analytics 帳戶錯誤。

- **建議**：確認已提供正確的帳戶。

### <a name="error-code-2703"></a>錯誤碼：2703

- **訊息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **原因**︰錯誤源自於 Data Lake Analytics。

- **建議**：已提交給 Data Lake Analytics 的作業和其中的指令碼皆失敗。 在 Data Lake Analytics 中進行調查。 在入口網站中，移至 Data Lake Analytics 帳戶，然後使用 Data Factory 活動執行識別碼來尋找作業 (請勿使用管線執行識別碼)。 這些作業會提供有關錯誤的詳細資訊，並協助您進行疑難排解。

   如果解決方法不清楚，請洽詢 Data Lake Analytics 支援小組，並提供作業統一資源定位器 (URL)，其中包含您的帳戶名稱和作業識別碼。
 
## <a name="azure-functions"></a>Azure 函式

### <a name="error-code-3602"></a>錯誤碼：3602

- **訊息**：`Invalid HttpMethod: '%method;'.`

- **原因**︰Azure Function 活動不支援活動承載中指定的 Httpmethod。

- **建議**：支援的 Httpmethod 如下：PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。

### <a name="error-code-3603"></a>錯誤碼：3603

- **訊息**：`Response Content is not a valid JObject.`

- **原因**︰呼叫的 Azure 函式未在回應中傳回 JSON 承載。 Azure Data Factory (ADF) Azure 函式活動僅支援 JSON 回應內容。

- **建議**：更新 Azure 函式以傳回有效的 JSON 承載，例如 C# 函式可能會傳回 `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>錯誤碼：3606

- **訊息**：Azure 函式活動缺少函式金鑰。

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動的 JSON 定義是否具有名為 `functionKey` 的屬性。

### <a name="error-code-3607"></a>錯誤碼：3607

- **訊息**：`Azure function activity missing function name.`

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動的 JSON 定義是否具有名為 `functionName` 的屬性。

### <a name="error-code-3608"></a>錯誤碼：3608

- **訊息**：`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**︰活動定義中的 Azure 函式詳細資料可能不正確。

- **建議**：請修正 Azure 函式的詳細資料，然後再試一次。

### <a name="error-code-3609"></a>錯誤碼：3609

- **訊息**：`Azure function activity missing functionAppUrl.`

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動的 JSON 定義是否具有名為 `functionAppUrl` 的屬性。

### <a name="error-code-3610"></a>錯誤碼：3610

- **訊息**：`There was an error while calling endpoint.`

- **原因**︰函式 URL 可能不正確。

- **建議**：請確認活動 JSON 中的 `functionAppUrl` 值正確無誤，然後再試一次。

### <a name="error-code-3611"></a>錯誤碼：3611

- **訊息**：`Azure function activity missing Method in JSON.`

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動的 JSON 定義是否具有名為 `method` 的屬性。

### <a name="error-code-3612"></a>錯誤碼：3612

- **訊息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 Azure 函式活動的 JSON 定義是否有連結服務的詳細資料。

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>錯誤碼：4101

- **訊息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**︰`%propertyName;` 屬性的定義未使用正確格式或遺失。

- **建議**：檢查 `%activityName;` 活動的 `%propertyName;` 屬性是否已使用正確資料加以定義。

### <a name="error-code-4110"></a>錯誤碼：4110

- **訊息**：`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**︰AzureMLExecutePipeline 活動定義不完整。

- **建議**：檢查輸入 AzureMLExecutePipeline 活動的 JSON 定義是否已正確連結服務詳細資料。

### <a name="error-code-4111"></a>錯誤碼：4111

- **訊息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**︰活動定義不正確。

- **建議**：檢查輸入 AzureMLExecutePipeline 活動的 JSON 定義是否已正確連結服務詳細資料。

### <a name="error-code-4112"></a>錯誤碼：4112

- **訊息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**︰'%propertyName;' 屬性的定義未使用正確格式或遺失。

- **建議**：檢查連結服務的 `%propertyName;` 屬性是否已使用正確資料加以定義。

### <a name="error-code-4121"></a>錯誤碼：4121

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**︰用來存取 Azure Machine Learning 的認證已過期。

- **建議**：請確認認證有效，然後再試一次。

### <a name="error-code-4122"></a>錯誤碼：4122

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**︰Azure Machine Learning 連結服務中提供的認證無效，或沒有執行該作業的權限。

- **建議**：請確認連結服務中的認證有效，而且具有存取 Azure Machine Learning 的權限。

### <a name="error-code-4123"></a>錯誤碼：4123

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**︰活動屬性 (例如 `pipelineParameters`) 不適用於 Azure Machine Learning (ML) 管線。

- **建議**：檢查活動屬性值是否符合預期的承載 (連結服務中指定且已發佈的 Azure ML 管線承載)。

### <a name="error-code-4124"></a>錯誤碼：4124

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**︰已發佈的 Azure ML 管線端點不存在。

- **建議**：確認連結服務中指定且已發佈的 Azure Machine Learning 管線端點存在於 Azure Machine Learning 中。

### <a name="error-code-4125"></a>錯誤碼：4125

- **訊息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**︰Azure Machine Learning 上發生伺服器錯誤。

- **建議**：稍後重試。 如果問題持續發生，請洽詢 Azure Machine Learning 小組以取得協助。

### <a name="error-code-4126"></a>錯誤碼：4126

- **訊息**：`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **原因**︰Azure ML 管線執行失敗。

- **建議**：請檢查 Azure Machine Learning 以取得更多錯誤記錄，然後修正 ML 管線。

## <a name="common"></a>通用

### <a name="error-code-2103"></a>錯誤碼：2103

- **訊息**：`Please provide value for the required property '%propertyName;'.`

- **原因**︰未提供屬性所需的值。

- **建議**：提供訊息中的值，然後再試一次。

### <a name="error-code-2104"></a>錯誤碼：2104

- **訊息**：`The type of the property '%propertyName;' is incorrect.`

- **原因**︰提供的屬性類型不正確。

- **建議**：請修正屬性的類型，然後再試一次。

### <a name="error-code-2105"></a>錯誤碼：2105

- **訊息**：`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**︰屬性的值無效，或不是預期的格式。

- **建議**：請參閱屬性的文件，並確認提供的值包含正確的格式和類型。

### <a name="error-code-2106"></a>錯誤碼：2106

- **訊息**：`The storage connection string is invalid. %errorMessage;`

- **原因**︰儲存體的連接字串無效或格式不正確。

- **建議**：請移至 Azure 入口網站並尋找您的儲存體，然後將連接字串複製並貼到您的連結服務中，然後再試一次。

### <a name="error-code-2108"></a>錯誤碼：2108

- **訊息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**︰要求因為基本問題而失敗，例如網路連線、DNS 失敗、伺服器憑證驗證或逾時。

- **建議**：使用 Fiddler/Postman 來驗證要求。

### <a name="error-code-2110"></a>錯誤碼：2110

- **訊息**：`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**︰活動中指定的連結服務不正確。

- **建議**：確認連結服務類型是活動支援的其中一個類型。 例如，HDI 活動的連結服務類型可以是 HDInsight 或 HDInsightOnDemand。

### <a name="error-code-2111"></a>錯誤碼：2111

- **訊息**：`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**︰提供的屬性類型不正確。

- **建議**：請修正屬性類型，然後再試一次。

### <a name="error-code-2112"></a>錯誤碼：2112

- **訊息**：`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**︰EndpointSuffix 中儲存體的雲端類型不受支援或無法判斷。

- **建議**：請使用另一個雲端中的儲存體，然後再試一次。

### <a name="error-code-2128"></a>錯誤碼：2128

- **訊息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**︰網路連線、DNS 失敗、伺服器憑證驗證或逾時。

- **建議**：驗證您嘗試叫用的端點正在回應要求。 您可以使用 Fiddler/Postman 之類的工具。

## <a name="custom"></a>Custom

下表適用於 Azure Batch。
 
### <a name="error-code-2500"></a>錯誤碼：2500

- **訊息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建議**：請確認可執行檔存在。 如果程式已啟動，請確認 *stdout.txt* 和 *stderr.txt* 已上傳至儲存體帳戶。 在您程式碼中包含記錄是利於偵錯的好方法。

### <a name="error-code-2501"></a>錯誤碼：2501

- **訊息**：`Cannot access user batch account; please check batch account settings.`

- **原因**︰不正確的 Batch 存取金鑰或集區名稱。

- **建議**：請確認連結服務中的集區名稱和 Batch 存取金鑰。

### <a name="error-code-2502"></a>錯誤碼：2502

- **訊息**：`Cannot access user storage account; please check storage account settings.`

- **原因**︰儲存體帳戶名稱或存取金鑰不正確。

- **建議**：請確認連結服務中的儲存體帳戶名稱和存取金鑰。

### <a name="error-code-2504"></a>錯誤碼：2504

- **訊息**：`Operation returned an invalid status code 'BadRequest'.`

- **原因**︰自訂活動的 `folderPath` 中有太多檔案。 `resourceFiles` 的大小總計不能超過 32,768 個字元。

- **建議**：移除不必要的檔案或將其壓縮，並新增解壓縮命令來將其解壓縮。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>錯誤碼：2505

- **訊息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**︰自訂活動僅支援使用存取金鑰的儲存體帳戶。

- **建議**：請參閱錯誤描述。

### <a name="error-code-2507"></a>錯誤碼：2507

- **訊息**：`The folder path does not exist or is empty: ...`

- **原因**︰位於指定路徑的儲存體帳戶中沒有任何檔案。

- **建議**：資料夾路徑必須包含您想要執行的可執行檔。

### <a name="error-code-2508"></a>錯誤碼：2508

- **訊息**：`There are duplicate files in the resource folder.`

- **原因**：相同名稱的多個檔案位於 folderPath 的不同子資料夾中。

- **建議**：自訂活動會將 folderPath 下的資料夾結構壓平合併。 如果您需要保留資料夾結構，請壓縮檔案，並使用解壓縮命令在 Azure Batch 中將檔案解壓縮。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>錯誤碼：2509

- **訊息**：`Batch url ... is invalid; it must be in Uri format.`

- **原因**︰Batch URL 必須類似於 `https://mybatchaccount.eastus.batch.azure.com`

- **建議**：請參閱錯誤描述。

### <a name="error-code-2510"></a>錯誤碼：2510

- **訊息**：`An error occurred while sending the request.`

- **原因**︰Batch URL 無效。

- **建議**：確認 Batch URL。
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>錯誤碼：206

- **訊息**：`The batch ID for Spark job is invalid. Please retry your job.`

- **原因**︰造成此錯誤的服務發生內部問題。

- **建議**：此問題可能是暫時性的。 請稍後再重試您的作業。

### <a name="error-code-207"></a>錯誤碼：207

- **訊息**：`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **原因**︰嘗試從主要儲存體帳戶判斷區域時發生內部錯誤。

- **建議**：請嘗試另一個儲存體。 

### <a name="error-code-208"></a>錯誤碼：208

- **訊息**：`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **原因**︰嘗試讀取服務主體或具現化 MSI 驗證時發生內部錯誤。

- **建議**：請考慮提供有權在所提供訂用帳戶中建立 HDInsight 叢集的服務主體，然後再試一次。 確認[已正確設定「管理身分識別」](../hdinsight/hdinsight-managed-identities.md)。

### <a name="error-code-2300"></a>錯誤碼：2300

- **訊息**：`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **原因**︰錯誤訊息包含類似於 `The remote name could not be resolved.` 的訊息。 提供的叢集 URI 可能無效。

- **建議**：請確認叢集尚未刪除，而且提供的 URI 正確。 當您在瀏覽器中開啟 URI 時，您應該會看到 Ambari UI。 如果叢集位於虛擬網路中，則 URI 應該是私人 URI。 若要將其開啟，請使用屬於相同虛擬網路的虛擬機器 (VM)。

   如需詳細資訊，請參閱[直接連線到 Apache Hadoop 服務](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services)。
 
 </br>

- **原因**︰如果錯誤訊息包含類似於 `A task was canceled.` 的訊息，表示作業提交逾時。

- **建議**：此問題可能源自一般 HDInsight 連線能力或網路連線能力。 請先確認您可以從任何瀏覽器連線到 HDInsight Ambari UI。 然後檢查您的認證是否仍然有效。
   
   如果您使用自我裝載整合執行階段 (IR)，請從已安裝自我裝載 IR 的 VM 或機器中執行此步驟。 然後再次嘗試從 Data Factory 提交作業。

   如需詳細資訊，請參閱 [Ambari Web UI](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)。

 </br>

- **原因**︰如果錯誤訊息包含類似於 `User admin is locked out in Ambari` 或 `Unauthorized: Ambari user name or password is incorrect` 的訊息時，表示 HDInsight 的認證不正確或已過期。

- **建議**：請更正認證並重新部署連結服務。 首先，請在任何瀏覽器上開啟叢集 URI 並嘗試登入，以確認認證可在 HDInsight 上使用。 如果認證無法使用，您可以從 Azure 入口網站中進行重設。

   針對 ESP 叢集，請透過[自助式密碼重設](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)來重設密碼。

 </br>

- **原因**︰如果錯誤訊息包含類似於 `502 - Web server received an invalid response while acting as a gateway or proxy server` 的訊息，表示此錯誤是由 HDInsight 服務傳回。

- **建議**：502 錯誤通常會發生在 Ambari 伺服器程序關閉時。 您可以藉由重新啟動前端節點來重新啟動 Ambari 服務。

    1. 使用 SSH 連線到 HDInsight 上的其中一個節點。
    1. 執行 `ping headnodehost` 來識別使用中的前端節點主機。
    1. 使用 SSH 連線到作用中的前端節點，因為 Ambari 伺服器位於作用中的前端節點上。 
    1. 重新啟動使用中的前端節點。

       如需詳細資訊，請參閱 Azure HDInsight 疑難排解文件。 例如：

       * [Ambari UI 502 錯誤](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [Apache Spark Thrift 伺服器的 RpcTimeoutException](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [針對應用程式閘道中閘道不正確的錯誤進行疑難排解](../application-gateway/application-gateway-troubleshooting-502.md)。

 </br>

- **原因**︰當錯誤訊息包含類似 `Unable to service the submit job request as templeton service is busy with too many submit job requests` 或 `Queue root.joblauncher already has 500 applications, cannot accept submission of application` 的訊息時，表示有太多作業同時提交到 HDInsight。

- **建議**：限制提交到 HDInsight 的並行作業數目。 如果作業是由相同活動所提交，請參閱 Data Factory 活動的並行資訊。 變更觸發程序，讓並行的管線執行數目分散在一段時間內。

   請參閱 [HDInsight 文件](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md)，依據錯誤中的建議調整 `templeton.parallellism.job.submit`。

### <a name="error-code-2301"></a>錯誤碼：2301

- **訊息**：`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**︰HDInsight 叢集或服務有問題。

- **建議**：如果 ADF 嘗試要求執行中作業的狀態，卻未收到來自 HDInsight 叢集的回應，就會發生此錯誤。 此問題可能源自叢集本身，或 HDInsight 服務可能發生中斷。

   請參閱 https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide 上的 HDInsight 疑難排解文件，或連絡其支援服務以取得進一步的協助。

### <a name="error-code-2302"></a>錯誤碼：2302

- **訊息**：`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**︰已將作業提交至 HDI 叢集，但卻失敗。

- **建議**： 

 1. 檢查 Ambari UI：
    1. 確定所有服務仍在執行中。
    1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
       1. 如需有關警示和警示解決方式的詳細資訊，請參閱[管理和監視叢集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
    1. 檢閱您的 YARN 記憶體。 如果您的 YARN 記憶體偏高，處理作業的速度可能會有所延遲。 如果您的資源不足以容納 Spark 應用程式/作業，請擴大叢集，確保叢集具有足夠的記憶體和核心。 
 1. 執行測試作業範例。
    1. 如果您在 HDInsight 後端上執行相同的作業，請確認其是否已成功。 如需執行範例的例子，請參閱[執行包含在 HDInsight 中的 MapReduce 範例](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. 如果 HDInsight 上的作業仍然失敗，請檢查應用程式記錄檔和資訊，這些資訊可協助您：
    1. 檢查是否已將作業提交至 YARN。 如果作業未提交至 YARN，請使用 `--master yarn`。
    1. 如果應用程式已完成執行，請收集 YARN 應用程式的開始時間和結束時間。 如果應用程式未完成執行，請收集開始時間/啟動時間。
    1. 使用 `yarn logs -applicationId <Insert_Your_Application_ID>` 檢查並收集應用程式記錄檔。
    1. 檢查並收集 `/var/log/hadoop-yarn/yarn` 目錄下的 YARN Resource Manager 記錄檔。
    1. 如果這些步驟不足以解決問題，請連絡 Azure HDInsight 小組以取得支援，並提供上述記錄和時間戳記。

### <a name="error-code-2303"></a>錯誤碼：2303

- **訊息**：`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**︰已將作業提交至 HDI 叢集，但卻失敗。

- **建議**： 

 1. 檢查 Ambari UI：
    1. 確定所有服務仍在執行中。
    1. 從 Ambari UI 中，檢查儀表板中的 [警示] 區段。
       1. 如需有關警示和警示解決方式的詳細資訊，請參閱[管理和監視叢集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
    1. 檢閱您的 YARN 記憶體。 如果您的 YARN 記憶體偏高，處理作業的速度可能會有所延遲。 如果您的資源不足以容納 Spark 應用程式/作業，請擴大叢集，確保叢集具有足夠的記憶體和核心。 
 1. 執行測試作業範例。
    1. 如果您在 HDInsight 後端上執行相同的作業，請確認其是否已成功。 如需執行範例的例子，請參閱[執行包含在 HDInsight 中的 MapReduce 範例](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. 如果 HDInsight 上的作業仍然失敗，請檢查應用程式記錄檔和資訊，這些資訊可協助您：
    1. 檢查是否已將作業提交至 YARN。 如果作業未提交至 YARN，請使用 `--master yarn`。
    1. 如果應用程式已完成執行，請收集 YARN 應用程式的開始時間和結束時間。 如果應用程式未完成執行，請收集開始時間/啟動時間。
    1. 使用 `yarn logs -applicationId <Insert_Your_Application_ID>` 檢查並收集應用程式記錄檔。
    1. 檢查並收集 `/var/log/hadoop-yarn/yarn` 目錄下的 YARN Resource Manager 記錄檔。
    1. 如果這些步驟不足以解決問題，請連絡 Azure HDInsight 小組以取得支援，並提供上述記錄和時間戳記。

### <a name="error-code-2304"></a>錯誤碼：2304

- **訊息**：`MSI authentication is not supported on storages for HDI activities.`

- **原因**︰HDInsight (HDI) 連結服務或 HDI 活動中所用的儲存體連結服務上設定了不受支援的 MSI 驗證。

- **建議**：針對 HDI 連結服務或 HDI 活動中所使用的儲存體帳戶，提供完整連接字串。

### <a name="error-code-2305"></a>錯誤碼：2305

- **訊息**：`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**︰HDI 叢集的連線資訊不正確、提供的使用者沒有執行必要動作的權限，或 HDInsight 服務在回應 ADF 的要求時發生問題。

- **建議**：請確認使用者資訊是否正確，以及 HDI 叢集的 Ambari UI 是否可以使用瀏覽器從已安裝 IR 的 VM 中開啟 (適用於自我裝載 IR)，或是否可以從任何機器開啟 (適用於 Azure IR)。

### <a name="error-code-2306"></a>錯誤碼：2306

- **訊息**：`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**︰為指令碼動作提供的 JSON 無效。

- **建議**：錯誤訊息應有助於找出問題。 請修正 JSON 設定，然後再試一次。

   如需詳細資訊，請參閱 [Azure HDInsight 隨選連結服務](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。

### <a name="error-code-2310"></a>錯誤碼：2310

- **訊息**：`Failed to submit Spark job. Error: '%message;'`

- **原因**︰ADF 嘗試使用 Livy API (livy/batch) 在 Spark 叢集上建立批次，但收到錯誤。

- **建議**：請遵循錯誤訊息來修正問題。 如果沒有足夠的資訊可解決此問題，請連絡 HDI 小組，並將批次識別碼和作業識別碼提供給他們，您可以在 [ADF 監視] 頁面的活動執行輸出中找到這些資訊。 若要進一步進行疑難排解，請收集批次工作的完整記錄檔。

   如需如何收集完整記錄檔的詳細資訊，請參閱[取得批次工作的完整記錄](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2312"></a>錯誤碼：2312

- **訊息**：`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**︰HDInsight Spark 叢集上的作業失敗。

- **建議**：遵循 [ADF 監視] 頁面中活動執行輸出內的連結，針對 HDInsight Spark 叢集上的執行進行疑難排解。 請連絡 HDInsight 支援小組以取得進一步的協助。

   如需如何收集完整記錄檔的詳細資訊，請參閱[取得批次工作的完整記錄](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2313"></a>錯誤碼：2313

- **訊息**：`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**︰已刪除 HDInsight Spark 叢集上的批次。

- **建議**：針對 HDInsight Spark 叢集上的批次進行疑難排解。 請連絡 HDInsight 支援服務以取得進一步的協助。 

   如需如何收集完整記錄的詳細資訊，請參閱[取得批次作業的完整記錄](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)，並與 HDInsight 支援服務分享完整記錄，以取得進一步的協助。

### <a name="error-code-2328"></a>錯誤碼：2328

- **訊息**：`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**︰錯誤訊息應該會顯示錯誤的詳細資料。

- **建議**：錯誤訊息應有助於問題的疑難排解。

### <a name="error-code-2329"></a>錯誤碼：2329

- **訊息**：`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**︰錯誤訊息應該會顯示錯誤的詳細資料。

- **建議**：錯誤訊息應有助於問題的疑難排解。

### <a name="error-code-2331"></a>錯誤碼：2331

- **訊息**：`The file path should not be null or empty.`

- **原因**︰提供的檔案路徑是空的。

- **建議**：請提供現有檔案的路徑。

### <a name="error-code-2340"></a>錯誤碼：2340

- **訊息**：`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**︰HDInsightOnDemand 連結服務不支援透過 SelfHosted IR 執行。

- **建議**：請選取 Azure IR，然後再試一次。

### <a name="error-code-2341"></a>錯誤碼：2341

- **訊息**：`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**︰提供的 URL 格式不正確。

- **建議**：請修正叢集 URL，然後再試一次。

### <a name="error-code-2342"></a>錯誤碼：2342

- **訊息**：`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**︰為叢集提供的認證錯誤，或發生網路設定或連線問題，或 IR 連線到叢集時發生問題。

- **建議**： 
    1. 在瀏覽器中開啟 HDInsight 叢集的 Ambari UI，以確認認證是否正確。
    1. 如果叢集位於虛擬網路 (VNet) 中，而且正在使用自我裝載 IR，則 HDI URL 必須是 Vnet 中的私人 URL，且在叢集名稱後面應該列出 '-int'。
    
       例如，將 `https://mycluster.azurehdinsight.net/` 變更為 `https://mycluster-int.azurehdinsight.net/`。 請注意，`-int` 應位於 `mycluster` 之後，但在 `.azurehdinsight.net` 之前
    1. 如果叢集在 VNet 中，且使用自我裝載 IR，也使用了私人 URL，但連線仍然失敗，則表示安裝 IR 的 VM 連線到 HDI 時出現問題。 
    
       連線到已安裝 IR 的 VM，並在瀏覽器中開啟 Ambari UI。 使用叢集的私人 URL。 此連線應該可從瀏覽器執行。 如果不行，請連絡 HDInsight 支援小組以取得進一步的協助。
    1. 如果未使用自我裝載 IR，則 HDI 叢集應可公開存取。 在瀏覽器中開啟 Ambari UI，並檢查其是否已開啟。 如果叢集或其中的服務有任何問題，請連絡 HDInsight 支援小組以取得協助。

       ADF IR (自我裝載或 Azure) 必須能夠存取 ADF 連結服務中所使用的 HDI 叢集 URL，才能讓測試連線通過，並讓執行作業正常運作。 您可以從 VM 或任何公用機器的瀏覽器開啟 URL，以驗證此狀態。

### <a name="error-code-2343"></a>錯誤碼：2343

- **訊息**：`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**：使用者名稱或密碼是空的。

- **建議**：請提供正確的認證以連線到 HDI，然後再試一次。

### <a name="error-code-2345"></a>錯誤碼：2345

- **訊息**：`Failed to read the content of the hive script. Error: '%message;'`

- **原因**︰指令檔不存在，或 ADF 無法連線到指令碼的位置。

- **建議**：請確認指令碼存在，而且相關聯的連結服務具有適當的認證可進行連線。

### <a name="error-code-2346"></a>錯誤碼：2346

- **訊息**：`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**︰ADF 嘗試對 HDI 叢集建立開放式資料庫連接 (ODBC) 的連線，但發生錯誤而失敗。

- **建議**： 

   1. 請確認您已正確設定 ODBC/Java 資料庫連線 (JDBC) 來進行連線。
      1. 針對 JDBC，如果您使用相同的虛擬網路，您可以從下列網站取得此連線：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要確定您已設定正確的 JDBC，請參閱[透過 HDInsight 中的 JDBC 驅動程式查詢 Apache Hive](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)。
      1. 針對開方式資料庫 (ODB)，請參閱[教學課程：使用 ODBC 和 PowerShell 查詢 Apache Hive](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)，以確保您擁有正確的設定。 
   1. 確認 Hiveserver2、Hive 中繼存放區和 Hiveserver2 Interactive 正在作用中且正常運作。 
   1. 檢查 Ambari 使用者介面 (UI)：
      1. 確定所有服務仍在執行中。
      1. 從 Ambari UI 中，檢查儀表板中的警示區段。
         1. 如需有關警示和警示解決方式的詳細資訊，請參閱[管理和監視叢集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
   1. 如果這些步驟不足以解決問題，請連絡 Azure HDInsight 小組。

### <a name="error-code-2347"></a>錯誤碼：2347

- **訊息**：`Hive execution through ODBC failed with error message '%message;'.`

- **原因**︰ADF 已透過 ODBC 連線提交用於執行的 Hive 指令碼給 HDI 叢集，但指令碼在 HDI 上失敗。

- **建議**： 

   1. 請確認您已正確設定 ODBC/Java 資料庫連線 (JDBC) 來進行連線。
      1. 針對 JDBC，如果您使用相同的虛擬網路，您可以從下列網站取得此連線：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要確定您已設定正確的 JDBC，請參閱[透過 HDInsight 中的 JDBC 驅動程式查詢 Apache Hive](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)。
      1. 針對開方式資料庫 (ODB)，請參閱[教學課程：使用 ODBC 和 PowerShell 查詢 Apache Hive](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)，以確保您擁有正確的設定。 
   1. 確認 Hiveserver2、Hive 中繼存放區和 Hiveserver2 Interactive 正在作用中且正常運作。 
   1. 檢查 Ambari 使用者介面 (UI)：
      1. 確定所有服務仍在執行中。
      1. 從 Ambari UI 中，檢查儀表板中的警示區段。
         1. 如需有關警示和警示解決方式的詳細資訊，請參閱[管理和監視叢集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
   1. 如果這些步驟不足以解決問題，請連絡 Azure HDInsight 小組。

### <a name="error-code-2348"></a>錯誤碼：2348

- **訊息**：`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**︰儲存體連結服務屬性未正確設定。

- **建議**：在 HDI 活動的主要儲存體連結服務中，僅支援完整的連接字串。 確認您未使用 MSI 授權或應用程式。

### <a name="error-code-2350"></a>錯誤碼：2350

- **訊息**：`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**︰用來連線到檔案所在儲存體的認證不正確，或檔案不存在於該處。

- **建議**：此錯誤發生在 ADF 準備 HDI 活動，並嘗試在將作業提交至 HDI 之前，先將檔案複製到主要儲存體時。 請檢查檔案是否存在於提供的位置，以及儲存體連線是否正確。 由於 ADF HDI 活動不支援在與 HDI 活動相關的儲存體帳戶上進行 MSI 驗證，因此請確認那些連結服務是否具有完整金鑰，或使用 Azure Key Vault。

### <a name="error-code-2351"></a>錯誤碼：2351

- **訊息**：`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**︰檔案不存在於指定路徑。

- **建議**：請檢查檔案是否確實存在，以及指向此檔案且具有連線資訊的連結服務是否有正確的認證。

### <a name="error-code-2352"></a>錯誤碼：2352

- **訊息**：`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**︰檔案儲存體連結服務屬性未正確設定。

- **建議**：確認已正確設定檔案儲存體連結服務的屬性。

### <a name="error-code-2353"></a>錯誤碼：2353

- **訊息**：`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**︰未正確設定指令碼儲存體連結服務屬性。

- **建議**：確認已正確設定指令碼儲存體連結服務的屬性。

### <a name="error-code-2354"></a>錯誤碼：2354

- **訊息**：`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**︰活動不支援儲存體連結服務類型。

- **建議**：確認所選取的連結服務有其中一個支援的活動類型。 HDI 活動支援 AzureBlobStorage 和 AzureBlobFSStorage 連結服務。

   如需詳細資訊，請參閱[比較與 Azure HDInsight 叢集搭配使用的儲存體選項](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>錯誤碼：2355

- **訊息**：`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**︰為 `commandEnvironment` 提供的值不正確。

- **建議**：確認提供的值類似於：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    也請確認每個變數只會在清單中出現一次。

### <a name="error-code-2356"></a>錯誤碼：2356

- **訊息**：`The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**︰為 `commandEnvironment` 提供的值不正確。

- **建議**：確認提供的值類似於：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    也請確認每個變數只會在清單中出現一次。

### <a name="error-code-2357"></a>錯誤碼：2357

- **訊息**：`The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**︰提供的認證不正確

- **建議**：確認 ADLS Gen 1 中的連線資訊已連結至服務，並確認測試連線是否已成功。

### <a name="error-code-2358"></a>錯誤碼：2358

- **訊息**：`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**︰為必要屬性 `TimeToLive` 所提供的值具有無效格式。 

- **建議**：將值更新為建議的範圍，然後再試一次。

### <a name="error-code-2359"></a>錯誤碼：2359

- **訊息**：`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**︰為屬性 `roles` 提供的值無效。

- **建議**：將值更新為其中一個建議，然後再試一次。

### <a name="error-code-2360"></a>錯誤碼：2360

- **訊息**：`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**︰為 `HCatalogLinkedService` 提供的連接字串無效。

- **建議**：請將值更新為正確的 Azure SQL 連接字串，然後再試一次。

### <a name="error-code-2361"></a>錯誤碼：2361

- **訊息**：`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**︰叢集建立失敗，且 ADF 未從 HDInsight 服務取得錯誤。

- **建議**：開啟 Azure 入口網站，並嘗試尋找具有所提供名稱的 HDI 資源，然後檢查佈建狀態。 請連絡 HDInsight 支援小組以取得進一步的協助。

### <a name="error-code-2362"></a>錯誤碼：2362

- **訊息**：`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**︰提供的額外儲存體不是 Azure Blob 儲存體。

- **建議**：提供 Azure Blob 儲存體帳戶作為 HDInsight 隨選連結服務的額外儲存體。

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>使用 HDInsight ESP 叢集的 ADF 連結服務時發生 SSL 錯誤

- **訊息**：`Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **原因**：此問題最可能與系統信任存放區相關。

- **解決** 方式：您可以流覽至 Path **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** 並開啟 DriverConfiguration64.exe 來變更設定。

    ![取消核取 [使用系統信任存放區]](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>網路活動

### <a name="error-code-2128"></a>錯誤碼：2128

- **訊息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**︰發生此問題的原因是網路連線、DNS 失敗、伺服器憑證驗證或逾時。

- **建議**：驗證您嘗試叫用的端點正在回應要求。 您可以使用 **Fiddler/Postman** 之類的工具。

### <a name="error-code-2108"></a>錯誤碼：2108

- **訊息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**︰要求因為基本問題而失敗，例如網路連線、DNS 失敗、伺服器憑證驗證或逾時。

- **建議**：使用 Fiddler/Postman 來驗證要求。

#### <a name="more-details"></a>其他詳細資訊
若要使用 **Fiddler** 來為受監視的 Web 應用程式建立 HTTP 工作階段：

1. 下載、安裝和開啟 [Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果您的 Web 應用程式使用 HTTPS，請移至 [工具] > [Fiddler 選項] > [HTTPS]。

   1. 在 [HTTPS] 索引標籤中，選取 [取得 HTTPS 連線] 和 [將 HTTPS 流量解密]。

      ![Fiddler 選項](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果您的應用程式使用 TLS/SSL 憑證，請將 Fiddler 憑證新增至您的裝置。

   請移至：[工具] > [Fiddler 選項] > [HTTPS] > [動作] > [將根憑證匯出到桌面]。

1. 藉由前往 [檔案] > [擷取流量] 來關閉擷取作業。 或是按下 **F12**。

1. 清除瀏覽器的快取後，所有快取的項目都會移除，因此必須重新下載。

1. 建立要求：

1. 選取 [編輯器] 索引標籤。

   1. 設定 HTTP 方法和 URL。
 
   1. 如有需要，請新增標頭和要求本文。

   1. 選取 [執行] 。

1. 再次開啟流量擷取，並在您的頁面上完成有問題的交易。

1. 請移至：[檔案] > [儲存] > [所有工作階段]。

如需詳細資訊，請參閱[開始使用 Azure Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="general"></a>一般

### <a name="activity-stuck-issue"></a>活動停滯問題

當您觀察到活動所執行的時間比平常沒有進度的一般回合還長時，可能會停滯。 您可以嘗試取消它，然後再試一次，看看它是否有説明。 如果是複製活動，您可以從 [疑難排解複製活動效能](copy-activity-performance-troubleshooting.md)，瞭解效能監視和疑難排解。如果是資料流程，請從 [對應資料流程的效能](concepts-data-flow-performance.md) 和微調指南中學習。

### <a name="payload-is-too-large"></a>承載太大

**錯誤訊息：**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**原因：** 每個活動執行的承載包括活動設定、相關聯的資料集 (s) 和連結的服務 (s) 設定（如果有的話），以及每個活動類型所產生的一小部分系統屬性。 這類承載大小的限制為 896 KB，如 [Data Factory 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) 一節中所述。

**建議：** 您達到此限制的原因很可能是因為您傳入了上游活動輸出或外部的一或多個大型參數值，特別是當您在控制流程的活動之間傳遞實際資料時。 檢查您是否可以減少大型參數值的大小，或調整管線邏輯以避免跨活動傳遞這類值，並改為在活動內部處理。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

* [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
* [Azure 影片](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
