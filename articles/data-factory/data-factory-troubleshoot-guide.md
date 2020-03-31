---
title: 排除 Azure 資料工廠故障 |微軟文檔
description: 瞭解如何在 Azure 資料工廠中排除外部控制活動。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065385"
---
# <a name="troubleshoot-azure-data-factory"></a>排除 Azure 資料工廠故障

本文探討 Azure 資料工廠中外部控制活動的常見故障排除方法。

## <a name="connector-and-copy-activity"></a>連接器和複製活動

對於連接器問題（例如，使用複製活動遇到錯誤）請參閱對[Azure 資料工廠連接器進行故障排除](connector-troubleshoot-guide.md)。
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>錯誤代碼： 3200

- **消息**：錯誤 403。

- **原因**：`The Databricks access token has expired.`

- **建議**：預設情況下，Azure 資料磚塊訪問權杖的有效期為 90 天。 創建新權杖並更新連結的服務。


### <a name="error-code--3201"></a>錯誤代碼： 3201

- **消息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建議**：在資料磚塊活動中指定筆記本路徑。

<br/>  

- **消息**：`Cluster... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建議**：驗證資料磚塊群集是否存在。

<br/>  

- **消息**：`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建議**：為工作區定址方案或`dbfs:/folder/subfolder/foo.py`存儲在資料磚塊檔案系統中的檔指定絕對路徑。

<br/>  

- **消息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建議**：驗證[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/>  

- **消息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建議**：驗證[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/>  

- **消息**：`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建議**：請參閱錯誤訊息。

<br/>

### <a name="error-code--3202"></a>錯誤代碼： 3202

- **消息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建議**：檢查使用此資料塊工作區的所有管道的作業創建率。  如果啟動的管道在聚合中運行了過多的 Databricks，則將某些管道遷移到新的工作區。

<br/>  

- **消息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建議**：檢查管道 JSON 並確保基本參數筆記本中的所有參數指定非空值。

<br/>  

- **消息** `User: `： 簡單使用者上下文[使用者 Id]...user@company.com` is not   authorized to access cluster.`

- **原因**：不允許生成訪問權杖的使用者訪問連結服務中指定的 Databricks 群集。

- **建議**：確保使用者在工作區中具有所需的許可權。


### <a name="error-code--3203"></a>錯誤代碼： 3203

- **消息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**：群集已終止。 對於互動式群集，這可能是一個競爭條件。

- **建議**：避免此錯誤的最佳方法是使用作業群集。


### <a name="error-code--3204"></a>錯誤代碼： 3204

- **消息**：`Job execution failed.`

- **原因**：錯誤訊息指示各種問題，如意外的群集狀態或特定活動。 通常根本不顯示錯誤訊息。

- **建議**： 不適用
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表適用于 U-SQL。
      
### <a name="error-code--2709"></a>錯誤代碼： 2709

- **消息**：`The access token is from the wrong tenant.`

- **原因**： Azure 活動目錄 （Azure AD） 租戶不正確。

- **建議**： 不正確的 Azure 活動目錄 （Azure AD） 租戶。

<br/>

- **消息**：`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **原因**：此錯誤是由資料湖分析限制引起的。

- **建議**：通過更改資料工廠觸發器和活動併發設置，減少向資料湖分析提交的作業數。 或增加資料湖分析的限制。

<br/>  

- **消息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**：此錯誤是由資料湖分析限制引起的。

- **建議**：通過更改資料工廠觸發器和活動併發設置，減少向資料湖分析提交的作業數。 或增加資料湖分析的限制。


### <a name="error-code--2705"></a>錯誤代碼： 2705

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服務主體或證書無法訪問存儲中的檔。

- **建議**：確保使用者為數據湖分析作業提供的服務主體或證書可以訪問資料湖分析帳戶和來自根資料夾中的預設資料湖存儲實例。


### <a name="error-code--2711"></a>錯誤代碼： 2711

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服務主體或證書無法訪問存儲中的檔。

- **建議**：確保使用者為數據湖分析作業提供的服務主體或證書可以訪問資料湖分析帳戶和來自根資料夾中的預設資料湖存儲實例。

<br/>  

- **消息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**：U-SQL 檔的路徑錯誤，或者連結的服務憑據沒有存取權限。

- **建議**：驗證連結服務中提供的路徑和憑據。


### <a name="error-code--2704"></a>錯誤代碼： 2704

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服務主體或證書無法訪問存儲中的檔。

- **建議**：確保使用者為數據湖分析作業提供的服務主體或證書可以訪問資料湖分析帳戶和來自根資料夾中的預設資料湖存儲實例。


### <a name="error-code--2707"></a>錯誤代碼： 2707

- **消息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **原因**：連結服務中的資料湖分析帳戶錯誤。

- **建議**：驗證是否提供了正確的帳戶。


### <a name="error-code--2703"></a>錯誤代碼： 2703

- **消息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **原因**：錯誤來自資料湖分析。

- **建議**：類似示例的錯誤表示作業已提交到資料湖分析，並且那裡的腳本失敗。 在資料湖分析中調查。 在門戶中，轉到資料湖分析帳戶，並使用資料工廠活動運行 ID（而不是管道運行 ID）查找作業。 那裡的作業提供有關錯誤的詳細資訊，並將説明您疑難排解。 如果解決方案不明確，請聯繫資料湖分析支援小組並提供作業 URL，其中包括您的帳戶名稱和工作 ID。
          

## <a name="azure-functions"></a>Azure 函式

### <a name="error-code--3602"></a>錯誤代碼： 3602

- **消息**：`Invalid HttpMethod: '%method;'.`

- **原因**：Azure 函式活動不支援在活動負載中指定的 Http 方法。

- **建議**： 支援的 Http 方法包括 PUT、POST、GET、刪除、選項、頭和 TRACE。


### <a name="error-code--3603"></a>錯誤代碼： 3603

- **消息**：`Response Content is not a valid JObject.`

- **原因**：調用的 Azure 函數未在回應中返回 JSON 有效負載。 ADF Azure 函式活動僅支援 JSON 回應內容。

- **建議**：更新 Azure 函數以返回有效的 JSON 有效負載，例如 C# 函數可能會返回（操作結果）新的\"OkObjectResult（"*ID\"：123\"\"}"）;


### <a name="error-code--3606"></a>錯誤代碼： 3606

- **消息**：Azure 函式活動缺少函數鍵。

- **原因**：Azure 函式活動定義不完整。

- **建議**：請檢查輸入 Azure 函式活動 JSON 定義具有名為"函數鍵"的屬性。


### <a name="error-code--3607"></a>錯誤代碼： 3607

- **消息**：`Azure function activity missing function name.`

- **原因**：Azure 函式活動定義不完整。

- **建議**：請檢查輸入 Azure 功能活動 JSON 定義具有名為"函數名稱"的屬性。


### <a name="error-code--3608"></a>錯誤代碼： 3608

- **消息**：`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**：活動定義中的 Azure 函數詳細資訊可能不正確。

- **建議**：修復 azure 函數詳細資訊，然後重試。


### <a name="error-code--3609"></a>錯誤代碼： 3609

- **消息**：`Azure function activity missing functionAppUrl.`

- **原因**：Azure 函式活動定義不完整。

- **建議**：請檢查輸入 Azure 函式活動 JSON 定義具有名為"函數 AppUrl"的屬性。


### <a name="error-code--3610"></a>錯誤代碼： 3610

- **消息**：`There was an error while calling endpoint.`

- **原因**：函數 URL 可能不正確。

- **建議**：請確保活動 JSON 中的"函數 AppUrl"的值正確，然後重試。


### <a name="error-code--3611"></a>錯誤代碼： 3611

- **消息**：`Azure function activity missing Method in JSON.`

- **原因**：Azure 函式活動定義不完整。

- **建議**：請檢查輸入 Azure 函式活動 JSON 定義具有名為"方法"的屬性。


### <a name="error-code--3612"></a>錯誤代碼： 3612

- **消息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**：Azure 函式活動定義不完整。

- **建議**：請檢查輸入 Azure 功能活動 JSON 定義具有連結的服務詳細資訊。



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>錯誤代碼： 4101

- **消息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**：格式錯誤或缺少屬性"%屬性名稱"的定義。

- **建議**：請檢查活動"%活動名稱"是否具有屬性"%屬性名稱"，定義與正確資料。


### <a name="error-code--4110"></a>錯誤代碼： 4110

- **消息**：`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**：AzureML 執行管道活動定義不完整。

- **建議**：請檢查輸入 AzureML 執行管道活動 JSON 定義是否連結了服務詳細資訊。


### <a name="error-code--4111"></a>錯誤代碼： 4111

- **消息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**：活動定義不正確。

- **建議**：請檢查輸入 AzureML 執行管道活動 JSON 定義是否具有正確的連結服務詳細資訊。


### <a name="error-code--4112"></a>錯誤代碼： 4112

- **消息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**：格式錯誤或缺少屬性"%屬性名稱"的定義。

- **建議**：請檢查連結的服務是否具有屬性"%屬性名稱"，定義與正確的資料。


### <a name="error-code--4121"></a>錯誤代碼： 4121

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：用於訪問 Azure 機器學習的憑據已過期。

- **建議**：請驗證憑據是否有效並重試


### <a name="error-code--4122"></a>錯誤代碼： 4122

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：Azure 機器學習連結服務中提供的憑據無效或沒有操作許可權。

- **建議**：請在連結服務中驗證憑據是否有效，並有權訪問 Azure 機器學習。


### <a name="error-code--4123"></a>錯誤代碼： 4123

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：活動的屬性（如管道參數）對於 Azure ML 管道無效。

- **建議**：請檢查活動屬性的值，以匹配連結服務中指定的已發佈的 Azure ML 管道的預期負載。


### <a name="error-code--4124"></a>錯誤代碼： 4124

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：已發佈的 Azure ML 管道終結點不存在。

- **建議**：請驗證在"連結服務"中指定的已發佈的 Azure 機器學習管道終結點存在於 Azure 機器學習中。


### <a name="error-code--4125"></a>錯誤代碼： 4125

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：Azure 機器學習上的伺服器錯誤。

- **建議**：請稍後重試。 如果問題仍然存在，請與 Azure 機器學習團隊聯繫尋求説明。


### <a name="error-code--4126"></a>錯誤代碼： 4126

- **消息**：`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **原因**：Azure ML 管道運行失敗。

- **建議**：請在 Azure 機器學習中查看更多錯誤日誌並修復 ML 管道。



## <a name="common"></a>通用

### <a name="error-code--2103"></a>錯誤代碼： 2103

- **消息**：`Please provide value for the required property '%propertyName;'.`

- **原因**：屬性的值尚未提供，但在方案中需要該值。

- **建議**：從郵件中提供值，然後重試。


### <a name="error-code--2104"></a>錯誤代碼： 2104

- **消息**：`The type of the property '%propertyName;' is incorrect.`

- **原因**：提供的屬性的類型不符合預期。

- **建議**：請修復屬性的類型，然後重試。


### <a name="error-code--2105"></a>錯誤代碼： 2105

- **消息**：`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**：屬性的值無效或沒有預期的格式。

- **建議**：請查找屬性的文檔，並確保提供的值具有預期的格式和類型。


### <a name="error-code--2106"></a>錯誤代碼： 2106

- **消息**：`The storage connection string is invalid. %errorMessage;`

- **原因**：存儲的連接字串無效或格式不正確。

- **建議**：請轉到 Azure 門戶，查找存儲，複製連接字串並粘貼到連結的服務中，然後重試。


### <a name="error-code--2108"></a>錯誤代碼： 2108

- **消息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：由於基礎問題（如網路連接、DNS 失敗、伺服器憑證驗證或超時），請求失敗。

- **建議**：使用 Fiddler/Postman 驗證請求。


### <a name="error-code--2110"></a>錯誤代碼： 2110

- **消息**：`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**：活動中指定的連結服務錯誤。

- **建議**：請確保連結的服務類型是活動支援的類型之一。 例如，對於 HDI 活動，連結的服務類型可以是 HDInsight 或 HDInsightOnDemand。


### <a name="error-code--2111"></a>錯誤代碼： 2111

- **消息**：`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**：提供的屬性的類型不符合預期。

- **建議**：請修復屬性的類型，然後重試。


### <a name="error-code--2112"></a>錯誤代碼： 2112

- **消息**：`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**：雲類型不受支援或無法確定從終結點Suffix進行存儲。

- **建議**：請使用另一個雲中的存儲，然後重試。


### <a name="error-code--2128"></a>錯誤代碼： 2128

- **消息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：網路連接、DNS 故障、伺服器憑證驗證或超時。

- **建議**：驗證您嘗試命中的終結點是否回應請求。 您可以使用像菲德爾/波斯特曼這樣的工具。



## <a name="custom"></a>Custom

下表適用于 Azure 批次處理。
      
### <a name="error-code--2500"></a>錯誤代碼： 2500

- **消息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建議**：確保可執行檔存在。 如果程式啟動，請確保*stdout.txt*和*stderr.txt*已上載到存儲帳戶。 最好在代碼中發出大量日誌以進行調試。


### <a name="error-code--2501"></a>錯誤代碼： 2501

- **消息**：`Cannot access user batch account; please check batch account settings.`

- **原因**：批次處理訪問金鑰或池名稱不正確。

- **建議**：驗證連結服務中的池名稱和批次處理訪問金鑰。


### <a name="error-code--2502"></a>錯誤代碼： 2502

- **消息**：`Cannot access user storage account; please check storage account settings.`

- **原因**：存儲帳戶名稱或訪問金鑰不正確。

- **建議**：驗證連結服務中的存儲帳戶名稱和訪問金鑰。


### <a name="error-code--2504"></a>錯誤代碼： 2504

- **消息**：`Operation returned an invalid status code 'BadRequest'.`

- **原因**：自訂活動的資料夾 Path 中的檔太多。 資源檔的總大小不能超過 32，768 個字元。

- **建議**：刪除不必要的檔。 或者壓縮它們並添加解壓縮命令以提取它們。 例如，使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>錯誤代碼： 2505

- **消息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**：自訂活動僅支援使用訪問金鑰的存儲帳戶。

- **建議**：請參閱錯誤說明。


### <a name="error-code--2507"></a>錯誤代碼： 2507

- **消息**：`The folder path does not exist or is empty: ...`

- **原因**：指定路徑的存儲帳戶中沒有檔。

- **建議**： 資料夾路徑必須包含要運行的可執行檔。


### <a name="error-code--2508"></a>錯誤代碼： 2508

- **消息**：`There are duplicate files in the resource folder.`

- **原因**：同一名稱的多個檔位於資料夾 Path 的不同子資料夾中。

- **建議**：自訂活動在資料夾路徑下平展資料夾結構。  如果需要保留資料夾結構，請使用解壓縮命令壓縮檔並將其提取到 Azure Batch 中。 例如，使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>錯誤代碼： 2509

- **消息**：`Batch   url ... is invalid; it must be in Uri format.`

- **原因**： 批次處理 URL 必須類似于`https://mybatchaccount.eastus.batch.azure.com`

- **建議**：請參閱錯誤說明。


### <a name="error-code--2510"></a>錯誤代碼： 2510

- **消息**：`An   error occurred while sending the request.`

- **原因**：批次處理 URL 無效。

- **建議**：驗證批次處理 URL。
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>錯誤代碼： 200

- **消息**：`Unexpected error happened: '%error;'.`

- **原因**：存在內部服務問題。

- **建議**：請聯繫 ADF 支援，以獲得進一步説明。


### <a name="error-code--201"></a>錯誤代碼： 201

- **消息**：`JobType %jobType; is not found.`

- **原因**：ADF 不支援新的作業類型。

- **建議**：請聯繫 ADF 支援小組以獲得進一步説明。


### <a name="error-code--202"></a>錯誤代碼： 202

- **消息**：`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息應顯示出錯的詳細資訊。

- **建議**：錯誤訊息應有助於解決問題。 如果沒有足夠的資訊，請聯繫 ADF 支援人員尋求進一步説明。


### <a name="error-code--203"></a>錯誤代碼： 203

- **消息**：`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息應顯示出錯的詳細資訊。

- **建議**：錯誤訊息應有助於解決問題。 如果沒有足夠的資訊，請聯繫 ADF 支援人員尋求進一步説明。


### <a name="error-code--204"></a>錯誤代碼： 204

- **消息**：`The resumption token is missing for runId '%runId;'.`

- **原因**：存在內部服務問題。

- **建議**：請聯繫 ADF 支援，以獲得進一步説明。


### <a name="error-code--205"></a>錯誤代碼： 205

- **消息**：`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **原因**：在按需創建 HDI 群集時出錯。

- **建議**：請聯繫 ADF 支援，以獲得進一步説明。


### <a name="error-code--206"></a>錯誤代碼： 206

- **消息**：`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **原因**：導致此問題的服務存在內部問題。

- **建議**：這可能是一個暫時性的問題。 請重試您的工作，如果問題仍然存在，請聯繫 ADF 支援人員以獲得進一步説明。


### <a name="error-code--207"></a>錯誤代碼： 207

- **消息**：`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **原因**：嘗試從主存儲帳戶確定區域時出現內部錯誤。

- **建議**：嘗試其他存儲。 如果這不是可接受的解決方案，請聯繫 ADF 支援小組以獲得進一步説明。


### <a name="error-code--208"></a>錯誤代碼： 208

- **消息**：`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **原因**：嘗試讀取服務主體或具現化 MSI 身份驗證時出現內部錯誤。

- **建議**：請考慮提供具有在提供的訂閱中創建 HDInsight 群集的許可權的服務主體，然後重試。 如果這不是可接受的解決方案，請聯繫 ADF 支援小組以獲得進一步説明。


### <a name="error-code--2300"></a>錯誤代碼： 2300

- **消息**：`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **原因**：當錯誤訊息包含類似于"無法解析遠端名稱"的消息時，這可能意味著提供的群集 URI 無效。


- **建議**：確保群集尚未刪除，並且提供的 URI 正確。 在瀏覽器中打開 URI 時，應看到 Ambari UI。 如果群集位於虛擬網路中，則 URI 應為專用 URI。 要打開它，請使用屬於同一虛擬網路的 VM。 如需詳細資訊，請參閱 [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)。
                  

<br>

- **原因**：當錯誤訊息包含類似于"任務已取消"的消息時，這意味著作業提交超時。

- **建議**： 問題可能是一般 HDInsight 連接或網路連接。 首先確認 HDInsight Ambari UI 可從任何瀏覽器獲得。 確認您的憑據仍然有效。 如果使用自託管集成運行時 （IR），請確保在安裝自託管 IR 的 VM 或電腦中執行此操作。 然後嘗試再次嘗試從資料工廠提交作業。 如果仍然失敗，請與資料工廠團隊聯繫以尋求支援。

<br>

- **原因**：當錯誤訊息包含類似于"使用者管理員鎖定在 Ambari 中"或"未授權：Ambari 使用者名或密碼不正確"的消息時，這意味著 HDInsight 的憑據不正確或已過期。

- **建議**：更正憑據並重新部署連結的服務。 首先，通過在任何瀏覽器上打開群集 URI 並嘗試登錄，確保憑據在 HDInsight 上正常工作。 如果憑據不起作用，可以從 Azure 門戶重置它們。

<br>

- **原因**：當錯誤訊息包含類似于"502 - Web 服務器在充當閘道或代理伺服器時收到無效回應"的消息時，HDInsight 服務將返回此錯誤。


- **建議**： 查看 Azure HDInsight 故障排除文檔，https://hdinsight.github.io/ambari/ambari-ui-502-error.html例如https://hdinsight.github.io/spark/spark-thriftserver-errors.html https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502， 。
                  

<br>

- **原因**：當錯誤訊息包含類似于"無法為提交作業請求提供服務"或"由於 templeton 服務忙於提交作業請求太多"或"佇列 root.joblauncher 已包含 500 個申請，無法接受提交申請"的消息時，這意味著同時向 HDInsight 提交的作業太多。

- **建議**：考慮限制提交到 HDInsight 的併發作業數。 如果同一活動正在提交作業，請參閱資料工廠活動併發。 更改觸發器，以便併發管道運行隨時間而分散。 請參閱 HDInsight 文檔來調整坦普頓.parallellism.job.提交，如錯誤所示。


### <a name="error-code--2301"></a>錯誤代碼： 2301

- **消息**：`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**：HDInsight 群集或服務有問題。


- **建議**：當 ADF 在嘗試獲取正在運行的作業的狀態時未從 HDInsight 群集獲得回應時，將發生此錯誤。 這可能是群集本身出現問題的原因，或者 HDInsight 服務可能有中斷。 請參閱 HDInsight 故障排除文檔，https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide或聯繫其支援以獲得進一步説明。
                


### <a name="error-code--2302"></a>錯誤代碼： 2302

- **消息**：`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**：作業已提交到 HDI 群集，但在那裡失敗。

- **建議**：按照活動運行輸出中的 Yarn 日誌連結查找 HDI 輸出中的錯誤。 如果需要，請聯繫 HDInsight 團隊尋求支援。


### <a name="error-code--2303"></a>錯誤代碼： 2303

- **消息**：`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**：作業已提交到 HDI 群集，但在那裡失敗。

- **建議**：按照活動運行輸出中的 Yarn 日誌連結查找 HDI 輸出中的錯誤。 請重試，或在需要時聯繫 HDInsight 團隊尋求支援。


### <a name="error-code--2304"></a>錯誤代碼： 2304

- **消息**：`MSI authentication is not supported on storages for HDI activities.`

- **原因**：HDI 連結服務或 HDI 活動中使用的存儲連結服務配置了不支援的 MSI 身份驗證。

- **建議**：請為 HDI 連結服務或 HDI 活動中使用的存儲帳戶提供完整的連接字串。


### <a name="error-code--2305"></a>錯誤代碼： 2305

- **消息**：`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**：HDI 群集的連接資訊不正確，提供的使用者沒有執行所需操作的許可權，或者 HDInsight 服務在回應來自 ADF 的請求時出現問題。

- **建議**：請確保使用者資訊正確。 還要驗證 HDI 群集的 Ambari UI 是否可以在安裝在自承載 IR 的情況下安裝 IR 的 VM 瀏覽器中打開，或者可以在 Azure IR 的情況下從任何電腦打開。


### <a name="error-code--2306"></a>錯誤代碼： 2306

- **消息**：`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**：為腳本操作提供的 json 無效。


- **建議**：錯誤訊息應有助於識別問題。 請修復 json 配置，然後重試。 有關詳細資訊https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service，請查看。
                


### <a name="error-code--2310"></a>錯誤代碼： 2310

- **消息**：`Failed to submit Spark job. Error: '%message;'`

- **原因**：ADF 嘗試使用 Livy API（livy/batch）在 Spark 群集上創建批次處理，但收到錯誤。

- **建議**：請按照錯誤訊息來解決此問題。 如果沒有足夠的資訊來解決問題，請與 HDI 團隊聯繫，並為他們提供可在活動運行 ADF 監視頁中的輸出中找到的批次處理 ID 和作業 ID。


### <a name="error-code--2312"></a>錯誤代碼： 2312

- **消息**：`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**：HDInsight Spark 群集上的作業失敗。

- **建議**：請按照活動運行輸出中的連結在 ADF 監視頁中對 HDInsight Spark 群集上的運行進行故障排除。 請聯繫 HDInsight 支援小組以獲得進一步説明。


### <a name="error-code--2313"></a>錯誤代碼： 2313

- **消息**：`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**：該批已刪除 HDInsight Spark 群集。

- **建議**：對 HDInsight Spark 群集上的批次處理進行故障排除。 請聯繫 HDInsight 支援，以獲得進一步説明。 


### <a name="error-code--2328"></a>錯誤代碼： 2328

- **消息**：`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：`The error message should show the details of what went wrong.`

- **建議**：錯誤訊息應有助於解決問題。


### <a name="error-code--2329"></a>錯誤代碼： 2329

- **消息**：`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：錯誤訊息應顯示出錯的詳細資訊。

- **建議**：錯誤訊息應有助於解決問題。


### <a name="error-code--2331"></a>錯誤代碼： 2331

- **消息**：`The file path should not be null or empty.`

- **原因**：提供的檔路徑為空。

- **建議**：請為存在的檔提供路徑。


### <a name="error-code--2340"></a>錯誤代碼： 2340

- **消息**：`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**：HDInsightOnDemand 連結服務不支援通過自託管 IR 執行。

- **建議**：請選擇 Azure IR，然後重試。


### <a name="error-code--2341"></a>錯誤代碼： 2341

- **消息**：`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**：提供的 URL 格式不正確。

- **建議**：請修復群集 URL，然後重試。


### <a name="error-code--2342"></a>錯誤代碼： 2342

- **消息**：`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**：提供的憑據對群集是錯誤的，或者存在網路設定或連接問題，或者 IR 連接到群集時出現問題。

- **建議**：  
      1. 通過在瀏覽器中打開 HDInsight 群集的 Ambari UI，驗證憑據是否正確。
      2. 如果群集位於 VNet 中，並且正在使用自承載 IR，則 HDI URL 應為 VNet 中的專用 URL，這意味著它應該在群集名稱之後具有"int"。 例如""https://mycluster.azurehdinsight.net/應更改為""。https://mycluster-int.azurehdinsight.net/
      2. 如果群集在 VNet 中，則使用自承載的 IR，並且使用了專用 URL，並且連接仍然失敗，則安裝 IR 的 VM 連接到 HDI 時出現問題。 連接到安裝 IR 的 VM，並在瀏覽器中打開 Ambari UI。 使用群集的專用 URL。 此連接應在瀏覽器中工作。 如果沒有，請聯繫 HDInsight 支援小組以獲得進一步説明。
      3. 如果未使用自承載 IR，則應公開訪問 HDI 群集。 在瀏覽器中打開 Ambari UI 並確保它打開。 如果群集或其上的服務存在任何問題，請聯繫 HDInsight 支援小組尋求説明。
      因此，通常，ADF 連結服務中使用的 HDI 群集 URL 必須可供 ADF IR（自託管或 Azure）訪問，以便測試連接通過，並且運行才能正常工作。 通過從 VM 或任何公共電腦從瀏覽器打開該 URL，可以輕鬆驗證這一點。
    


### <a name="error-code--2343"></a>錯誤代碼： 2343

- **消息**：`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**：使用者名或密碼為空。

- **建議**：提供正確的憑據以連接到 HDI，然後重試。


### <a name="error-code--2345"></a>錯誤代碼： 2345

- **消息**：`Failed to read the content of the hive script. Error: '%message;'`

- **原因**：指令檔不存在或 ADF 無法連接到腳本的位置。

- **建議**：請驗證腳本是否存在，並且關聯的連結服務具有正確的連接憑據。


### <a name="error-code--2346"></a>錯誤代碼： 2346

- **消息**：`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**：ADF 嘗試建立與 HDI 群集的 ODBC 連接，但失敗且出錯。

- **建議**：錯誤訊息和錯誤代碼應有助於解決 ODBC 連接錯誤。 如果它們不足以解決問題，請聯繫 Azure HDInsight 團隊尋求支援。


### <a name="error-code--2347"></a>錯誤代碼： 2347

- **消息**：`Hive execution through ODBC failed with error message '%message;'.`

- **原因**：ADF 通過 ODBC 連接向 HDI 群集提交了用於執行的配置單元腳本，並且該腳本在 HDI 上出現故障。

- **建議**：在 HDI 群集上執行 hive 腳本失敗，錯誤訊息和錯誤代碼應有助於故障排除。 如果它們不足以解決問題，請聯繫 Azure HDInsight 團隊尋求支援。


### <a name="error-code--2348"></a>錯誤代碼： 2348

- **消息**：`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**：存儲連結的服務屬性設置不正確。

- **建議**：HDI 活動的主存儲連結服務中僅支援完整連接字串。 請確保您沒有使用 MSI auth 或應用程式。


### <a name="error-code--2350"></a>錯誤代碼： 2350

- **消息**：`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**：為連接到應位於檔的存儲提供的憑據不正確，或者檔不存在。

- **建議**：當 ADF 為 HDI 活動執行準備步驟時，將發生此錯誤。 在將作業提交到 HDI 之前，它嘗試將檔案複製到主存儲。 確保檔存在於提供的位置，存儲連接正確。 ADF HDI 活動不支援與 HDI 活動相關的存儲帳戶上的 MSI 身份驗證，因此請確保這些連結的服務具有完整金鑰或使用 Azure 金鑰保存庫。


### <a name="error-code--2351"></a>錯誤代碼： 2351

- **消息**：`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**：檔在指定的路徑上不存在。

- **建議**：請檢查該檔是否確實存在，並且指向此檔的連接資訊的連結服務具有正確的憑據。


### <a name="error-code--2352"></a>錯誤代碼： 2352

- **消息**：`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**：檔存儲連結的服務屬性設置不正確。

- **建議**：請確保正確配置了檔存儲連結服務的屬性。


### <a name="error-code--2353"></a>錯誤代碼： 2353

- **消息**：`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**：腳本存儲連結的服務屬性設置不正確。

- **建議**：請確保正確配置了腳本存儲連結服務的屬性。


### <a name="error-code--2354"></a>錯誤代碼： 2354

- **消息**：`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**：活動不支援存儲連結的服務類型。

- **建議**：請確保所選連結的服務具有活動支援的類型之一。 HDI 活動支援 Azure Blob 存儲和 Azure BlobFS 存儲連結的服務。


### <a name="error-code--2355"></a>錯誤代碼： 2355

- **消息**：`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**：為命令環境提供不正確。

- **建議**：  
      \"請確保提供的值類似于： 命令環境\"： [\"變數Name_變數值\"] 每個變數只出現在清單中一次。
    


### <a name="error-code--2356"></a>錯誤代碼： 2356

- **消息**：`The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**：變數在命令環境中提供了兩次。

- **建議**：  
      \"請確保提供的值類似于： 命令環境\"： [\"變數Name_變數值\"] 每個變數只出現在清單中一次。
    


### <a name="error-code--2357"></a>錯誤代碼： 2357

- **消息**：`The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**：提供的憑據不正確。

- **建議**：請驗證 ADLS 第 1 代連結服務中的連接資訊，並確保測試連接成功。


### <a name="error-code--2358"></a>錯誤代碼： 2358

- **消息**：`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**：所需屬性"TimetoLive"提供的值格式無效。 

- **建議**：請更新值以在建議的範圍內，然後重試。


### <a name="error-code--2359"></a>錯誤代碼： 2359

- **消息**：`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**：屬性"角色"的提供值無效。

- **建議**：請更新該值作為建議之一，然後重試。


### <a name="error-code--2360"></a>錯誤代碼： 2360

- **消息**：`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**：為 HCatalog連結服務提供的連接字串無效。

- **建議**：請將該值更新為正確的 Azure SQL 連接字串，然後重試。


### <a name="error-code--2361"></a>錯誤代碼： 2361

- **消息**：`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**：群集創建失敗，ADF 未從 HDInsight 服務返回錯誤。

- **建議**：打開 Azure 門戶，並嘗試查找具有提供名稱的 HDI 資源，並檢查預配狀態。 請聯繫 HDInsight 支援小組以獲得進一步説明。


### <a name="error-code--2362"></a>錯誤代碼： 2362

- **消息**：`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**：提供的額外存儲不是 Azure Blob 存儲。

- **建議**：提供 Azure Blob 存儲帳戶作為 HDInsight 按需連結服務的額外存儲。



## <a name="web-activity"></a>網路活動

### <a name="error-code--2128"></a>錯誤代碼： 2128

- **消息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：網路連接、DNS 故障、伺服器憑證驗證或超時。

- **建議**：驗證您嘗試命中的終結點是否回應請求。 您可以使用像菲德爾/波斯特曼這樣的工具。


### <a name="error-code--2108"></a>錯誤代碼： 2108

- **消息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：由於基礎問題（如網路連接、DNS 失敗、伺服器憑證驗證或超時），請求失敗。

- **建議**：使用 Fiddler/Postman 驗證請求。
<br>


#### <a name="more-details"></a>其他詳細資訊
要使用 Fiddler 創建受監視 Web 應用程式的 HTTP 會話，請執行以下操作：

1. 下載、安裝和打開[Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果您的 Web 應用程式使用 HTTPS，請轉到**工具** > **Fiddler 選項** > **HTTPS**。 選擇 **"捕獲 HTTPS CONNECT"** 和 **"解密 HTTPS 流量**"。

   ![菲德爾選項](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果您的應用程式使用 TLS/SSL 憑證，請將 Fiddler 證書添加到您的設備。 轉到**工具** > **Fiddler 選項** > **HTTPS** > **操作** > **將根憑證匯出到桌面**。

1. 通過訪問**檔** > **捕獲流量**關閉捕獲。 或按**F12**。

1. 清除瀏覽器的緩存，以便刪除所有緩存的專案，並且必須再次下載。

1. 創建請求：

   1. 選擇 **"作曲家"** 選項卡。

   1. 設置 HTTP 方法和 URL。
   
   1. 如果需要，請添加標頭和請求正文。

   1. 選取 [執行] ****。

1. 再次打開流量捕獲，並在頁面上完成有問題的事務。

1. 轉到**檔** > **保存** > **所有會話**。

有關詳細資訊，請參閱[開始使用 Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)。

## <a name="next-steps"></a>後續步驟

有關更多故障排除説明，請嘗試以下資源：

*  [資料工廠博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [資料工廠功能請求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 視頻](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [資料工廠的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有關資料工廠的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)


            
