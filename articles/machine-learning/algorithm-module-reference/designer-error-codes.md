---
title: 排除模組錯誤
titleSuffix: Azure Machine Learning
description: 使用錯誤代碼在 Azure 機器學習設計器中排除模組異常
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/03/2019
ms.openlocfilehash: cda499b81a61a5b78ca86a96372640e368f90357
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364191"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>設計器的異常和錯誤代碼（預覽）

本文介紹了 Azure 機器學習設計器（預覽）中的錯誤訊息和異常代碼，以説明您解決機器學習管道的故障。

有兩種方法可以在設計器中獲取錯誤訊息的全文：  

- 按一下右側窗格中的"**查看輸出日誌"** 連結，然後滾動到底部。 詳細的錯誤訊息顯示在視窗的最後兩行中。  
  
- 選擇有錯誤的模組，然後按一下紅色 X。僅顯示相應的錯誤文本。

## <a name="error-0001"></a>錯誤 0001  
 如果找不到資料集的一或多個指定的資料行，就會發生例外狀況。  

 如果為模組進行了列選擇，但輸入資料集中不存在所選列，則您將收到此錯誤。 如果手動鍵入列名稱，或者欄選取器提供了運行管道時資料集中不存在的建議列，則可能發生此錯誤。  

**解析度：** 重新訪問引發此異常的模組，並驗證列名稱或名稱是否正確，以及所有引用的列是否都存在。  

|例外狀況訊息|
|------------------------|
|找不到一個或多個指定的列。|
|找不到名稱或索引"{column_id}"的列。|
|名稱或索引"{column_id}"的列不存在於"{arg_name_missing_column}"。|
|名稱或索引"{column_id}"的列不存在於"{arg_name_missing_column}"中，但存在於"{arg_name_has_column}中。|
|找不到名稱或索引"{column_names}"的列。|
|名稱或索引"{column_names}"的列在"{arg_name_missing_column}"中不存在。|
|名稱或索引"{column_names}"的列不存在於"{arg_name_missing_column}"中，但存在於"{arg_name_has_column}中。|


## <a name="error-0002"></a>錯誤 0002  
 如果有一或多個參數無法剖析或從指定的類型轉換成目標方法所需的類型，就會發生例外狀況。  

 將參數指定為輸入時，在 Azure 機器學習中發生此錯誤，並且數值型別與預期類型不同，並且無法執行隱式轉換。  

**解析度：** 檢查模組要求並確定需要哪種數值型別（字串、整數、雙精度值等）  

|例外狀況訊息|
|------------------------|
|解析參數失敗。|
|無法分析"{arg_name_or_column}"參數。|
|無法將"{arg_name_or_column}"參數轉換為"{to_type}"。|
|無法將"{arg_name_or_column}"參數從"{from_type}"轉換為"{to_type}"。|
|無法將"{arg_name_or_column}"參數值"{arg_value}"從"{from_type}"轉換為"{to_type}"。|
|無法將"{arg_name_or_column}"一欄中的值"{arg_value arg_name_or_column}"from_type 轉換為"{to_type}"，並提供格式"{fmt}"。|


## <a name="error-0003"></a>錯誤 0003  
 如果一或多個輸入是 Null 或空白，就會發生例外狀況。  

 如果模組的任何輸入或參數為空，則將在 Azure 機器學習中收到此錯誤。  例如，當您未鍵入參數的任何值時，可能會發生此錯誤。 如果選擇了缺少值的資料集或空資料集，也可能發生這種情況。  

**解決方案：**

+ 打開生成異常的模組，並驗證是否指定了所有輸入。 確保指定所有必需的輸入。 
+ 確保可從 Azure 存儲載入的資料可訪問，並且帳戶名稱或金鑰未更改。  
+ 檢查輸入資料中缺少值或 null。
+ 如果在資料來源上使用查詢，請驗證資料是否以預期格式返回。 
+ 檢查資料規範中是否拼寫錯誤或其他更改。
  
|例外狀況訊息|
|------------------------|
|一個或多個輸入為空或空。|
|輸入"{name}"為空或空。|


## <a name="error-0004"></a>錯誤 0004  
 如果參數小於或等於特定值，就會發生例外狀況。  

 如果消息中的參數低於模組處理資料所需的邊界值，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 重新訪問引發異常的模組並將參數修改為大於指定值。  

|例外狀況訊息|
|------------------------|
|參數應該大於界限值。|
|參數"{arg_name}"值應大於 {lower_boundary}。|
|參數"{arg_name}"的值"{actual_value}"應大於 {lower_boundary}。|


## <a name="error-0005"></a>錯誤 0005  
 如果參數小於特定值，就會發生例外狀況。  

 如果消息中的參數低於或等於模組處理資料所需的邊界值，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 重新訪問引發異常的模組，並將參數修改為大於或等於指定值。  

|例外狀況訊息|
|------------------------|
|參數應該大於或等於界限值。|
|參數"{arg_name}"值應大於或等於 {lower_boundary}。|
|參數"{arg_name}"的值應大於或等於 {lower_boundary}。|


## <a name="error-0006"></a>錯誤 0006  
 如果參數大於或等於指定值，就會發生例外狀況。  

 如果消息中的參數大於或等於模組處理資料所需的邊界值，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 重新訪問引發異常的模組並將參數修改為小於指定值。  

|例外狀況訊息|
|------------------------|
|參數不相符。 其中一個參數應該小於另一個。|
|參數"{arg_name}"值應小於參數"{upper_boundary_parameter_name}"值。|
|參數"{arg_name}"的值應小於 {upper_boundary_parameter_name}。|


## <a name="error-0007"></a>錯誤 0007  
 如果參數大於特定值，就會發生例外狀況。  

 如果在模組的屬性中指定的值大於允許的值，則將在 Azure 機器學習中收到此錯誤。 例如，您可以指定超出受支援日期範圍的資料，或者可能指示在只有三列可用時使用五列。 

 如果要指定需要以某種方式匹配的兩組資料，則可能還會看到此錯誤。 例如，如果要重命名列，並按索引指定列，則提供的名稱數必須與列索引數匹配。 另一個示例可能是使用兩列的數學運算，其中列必須具有相同的行數。 

**解決方案：**

 + 打開有問題的模組並查看任何數值屬性設置。
 + 確保任何參數值都屬於該屬性的受支援值範圍。
 + 如果模組採用多個輸入，請確保輸入的大小相同。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 檢查資料集或資料來源是否已更改。 有時，在列數、列資料類型或資料大小更改後，處理早期版本資料的值將失敗。  

|例外狀況訊息|
|------------------------|
|參數不相符。 其中一個參數應該要小於或等於另一個。|
|參數"{arg_name}"值應小於或等於參數"{upper_boundary_parameter_name}"值。|
|參數"{arg_name}"的值"{actual_value}"應小於或等於 {upper_boundary}。|
|參數"arg_name"值 {actual_value}應小於或等於參數"upper_boundary_parameter_name"值 [upper_boundary]。|


## <a name="error-0008"></a>錯誤 0008  
 如果參數不是在範圍內，就會發生例外狀況。  

 如果消息中的參數超出模組處理資料所需的邊界，則將在 Azure 機器學習中收到此錯誤。  

 例如，如果嘗試使用["添加行"](add-rows.md)合併具有不同列數的兩個資料集，則顯示此錯誤。  

**解析度：** 重新訪問引發異常的模組，並將參數修改為在指定範圍內。  

|例外狀況訊息|
|------------------------|
|參數值不在指定的範圍內。|
|參數"{arg_name}"值不在範圍內。|
|參數"{arg_name}"值應位於 [lower_boundary][upper_boundary]]的範圍內。|
|參數"{arg_name}"值不在範圍內。 [原因]|


## <a name="error-0009"></a>錯誤 0009  
 當 Azure 存儲帳戶名稱或容器名稱指定不正確時，將發生異常。  

指定 Azure 存儲帳戶的參數時，Azure 機器學習設計器中會發生此錯誤，但無法解析名稱或密碼。 密碼或帳戶名稱上的錯誤可能由於多種原因發生：

 + 帳戶的類型不正確。 不支援某些新帳戶類型用於機器學習設計器。 有關詳細資訊，請參閱[導入資料](import-data.md)。
 + 您輸入的帳戶名稱不正確
 + 帳戶不再存在
 + 存儲帳戶的密碼錯誤或已更改
 + 您沒有指定容器名稱，或者容器不存在
 + 您沒有完全指定檔路徑（Blob 路徑）
   

**解決方案：**

當您嘗試手動輸入帳戶名稱、密碼或容器路徑時，通常會出現此類問題。 我們建議您對["導入資料"](import-data.md)模組使用新嚮導，這有助於查找和檢查名稱。

還要檢查帳戶、容器或 Blob 是否已刪除。 使用另一個 Azure 存儲實用程式驗證帳戶名稱和密碼輸入正確，以及容器是否存在。 

Azure 機器學習不支援某些較新的帳戶類型。 例如，新的"熱"或"冷"存儲類型不能用於機器學習。 作為"通用用途"創建的經典存儲帳戶和存儲帳戶都工作正常。

如果指定了 Blob 的完整路徑，請驗證該路徑是否指定為**容器/blobname，** 以及容器和 Blob 都存在於帳戶中。  

 路徑不應包含前導斜杠。 例如 **/容器/blob**不正確，應作為**容器/blob**輸入。  


|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶名稱或容器名稱不正確。|
|Azure 存儲帳戶名稱"{account_name}"或容器名稱"{container_name}"不正確;否則，請執行 "account_name}"或容器名稱"{container_name}"。"""""container_name}"。""""container_name"，否則，請執行"account_name}"或"container_name}"的容器應使用格式容器/blob 的容器名稱。|


## <a name="error-0010"></a>錯誤 0010  
 如果輸入資料集有資料行名稱應該符合但卻不符，就會發生例外狀況。  

 如果消息中的列索引在兩個輸入資料集中具有不同的列名稱，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 使用["編輯中繼資料"](edit-metadata.md)或修改原始資料集，以便對指定的列索引具有相同的列名稱。  

|例外狀況訊息|
|------------------------|
|在輸入資料集中有相對應索引的資料行具有不同的名稱。|
|對於輸入資料集（[資料集1]和[dataset2]）的列 [col_index]（基於零），列名稱不同。|


## <a name="error-0011"></a>錯誤 0011  
 如果，傳遞的資料行集引數不適用於任何資料集資料行，就會發生例外狀況。  

 如果指定的列選擇與給定資料集中的任何列不匹配，則將在 Azure 機器學習中收到此錯誤。  

 如果尚未選擇列，並且模組至少需要一列才能工作，則還可以收到此錯誤。  

**解析度：** 修改模組中的列選擇，以便應用於資料集中的列。  

 如果模組要求您選擇特定列（如標籤列），請驗證是否選擇了正確的列。  

 如果選擇了不適當的列，請刪除它們並重新運行管道。  

|例外狀況訊息|
|------------------------|
|指定的資料行集不適用於任何資料集資料行。|
|指定的列集"{column_set}"不適用於任何資料集列。|


## <a name="error-0012"></a>錯誤 0012  
 如果無法以傳遞的引數集合建立類別的執行個體，就會發生例外狀況。  

**解析度：** 此錯誤由使用者不可操作，並且將在將來的版本中棄用。  

|例外狀況訊息|
|------------------------|
|未定型的模型，請先定型模型。|
|未經訓練的模型 （{arg_name}），使用經過訓練的模型。|


## <a name="error-0013"></a>錯誤 0013  
 如果傳遞給模組的學員的類型無效，則會出現異常。  

 每當經過訓練的模型與連接的評分模組不相容時，就會發生此錯誤。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**解決方案：**

確定培訓模組生成的學員類型，並確定適合學員的評分模組。 

如果模型使用任何專門培訓模組進行訓練，則僅將訓練的模型連接到相應的專用評分模組。 


|模型類型|培訓模組| 評分模組|
|----|----|----|
|任何分類器|[火車模型](train-model.md) |[計分模型](score-model.md)|
|任何回歸模型|[火車模型](train-model.md) |[計分模型](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|例外狀況訊息|
|------------------------|
|傳遞的學習類型無效。|
|學員"{arg_name}"的類型無效。|
|學員"{arg_name}"的類型無效，類型為"{learner_type}"。|


## <a name="error-0014"></a>錯誤 0014  
 如果列唯一值的計數大於允許值，則會出現異常。  

 當列包含太多唯一值時，將發生此錯誤。  例如，如果指定將列作為分類資料處理，但列中的唯一值太多，無法完成處理，則可能會看到此錯誤。 如果兩個輸入中的唯一值數不匹配，則可能還會看到此錯誤。   

**解決方案：**

打開建置錯誤的模組，並標識用作輸入的列。 對於某些模組，您可以按右鍵資料集輸入並選擇 **"視覺化"** 以獲取有關各個列的統計資訊，包括唯一值的數量及其分佈。

對於要用於分組或分類的列，請採取措施減少列中唯一值的數量。 您可以根據列的資料類型以不同的方式減少。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到與您的方案匹配的解析度？ 您可以提供有關本主題的回饋，其中包括建置錯誤的模組的名稱以及列的資料類型和基數。 我們將使用這些資訊為常見方案提供更有針對性的故障排除步驟。   

|例外狀況訊息|
|------------------------|
|資料行的唯一值數量大於允許的數量。|
|列中的唯一值數："{column_name}"大於允許值。|
|列中的唯一值數："{column_name}"超過 [限制]的元組計數。|


## <a name="error-0015"></a>錯誤 0015  
 如果資料庫連接失敗，就會發生例外狀況。  

 如果輸入不正確的 SQL 帳戶名稱、密碼、資料庫伺服器或資料庫名稱，或者由於資料庫或伺服器問題而無法與資料庫建立連接，您將收到此錯誤。  

**解析度：** 驗證帳戶名稱、密碼、資料庫伺服器和資料庫是否輸入正確，以及指定的帳號是否具有正確的權限等級。 驗證資料庫當前是否可訪問。  

|例外狀況訊息|
|------------------------|
|建立資料庫連接時發生錯誤。|
|創建資料庫連接的錯誤：{connection_str}。|


## <a name="error-0016"></a>錯誤 0016  
 如果傳遞到模組的輸入資料集應該有相容的資料行類型，但卻沒有，就會發生例外狀況。  

 如果在兩個或多個資料集中傳遞的列類型彼此不相容，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 使用[編輯中繼資料](edit-metadata.md)或修改原始輸入資料集<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 以確保列的類型是相容的。  

|例外狀況訊息|
|------------------------|
|輸入資料集中具有對應索引的資料行是不相容的類型。|
|列"{first_col_names}"在訓練和測試資料之間不相容。|
|列"{first_col_names}"和"{second_col_names}"不相容。|
|列元素類型與輸入資料集的列"first_col_names}"（基於零）（{first_dataset_names}和{second_dataset_names}）不相容。|


## <a name="error-0017"></a>錯誤 0017  
 如果所選列使用當前模組不支援的資料類型，則會發生異常。  

 例如，如果列選擇包含資料類型由模組處理的列（如數學運算的字串列）或分類要素列所在的分數列，則可能在 Azure 機器學習中收到此錯誤必填。  

**解決方案：**
 1. 標識有問題的列。
 2. 查看模組的要求。
 3. 修改列以使其符合要求。 您可能需要使用以下幾個模組進行更改，具體取決於您嘗試的列和轉換：
    + 使用["編輯中繼資料"](edit-metadata.md)更改列的資料類型，或將列用法從要素更改為數字、分類更改為非分類，依此類推。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 作為最後的手段，您可能需要修改原始輸入資料集。

> [!TIP]
> 找不到與您的方案匹配的解析度？ 您可以提供有關本主題的回饋，其中包括建置錯誤的模組的名稱以及列的資料類型和基數。 我們將使用這些資訊為常見方案提供更有針對性的故障排除步驟。 

|例外狀況訊息|
|------------------------|
|無法處理目前類型的資料行。 模組不支援此類型。|
|無法處理類型 [col_type]的列。 模組不支援此類型。|
|無法處理類型 [col_type] 的列"{col_name}"。 模組不支援此類型。|
|無法處理類型 [col_type] 的列"{col_name}"。 模組不支援此類型。 參數名稱： [arg_name]。|


## <a name="error-0018"></a>錯誤 0018  
 如果輸入資料集無效，就會發生例外狀況。  

**解析度：** Azure 機器學習中的此錯誤可能出現在許多上下文中，因此沒有一個解析度。 通常，該錯誤指示作為輸入模組提供的資料列數錯誤，或者資料類型不符合模組的要求。 例如：  

-   模組需要標籤列，但沒有列被標記為標籤，或者您尚未選擇標籤列。  
  
-   該模組要求資料分類，但資料是數位資料。  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   資料採用錯誤的格式。  
  
-   導入的資料包含無效字元、錯誤值或範圍外值。  
-   該列為空或包含太多缺失值。  

 要確定要求和資料可能的方式，請查看將使用資料集作為輸入的模組的說明主題。  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|例外狀況訊息|
|------------------------|
|資料集無效。|
|[dataset1] 包含無效資料。|
|[資料集1]和[資料集2]應按列排列一致。|
|[dataset1] 包含無效資料[原因]。|
|[資料集1] 包含 [invalid_data_category]。 [troubleshoot_hint]|
|[資料集1] 無效，[原因]。 [troubleshoot_hint]|


## <a name="error-0019"></a>錯誤 0019  
 如果資料行必須要包含已排序的值，但卻沒有，就會發生例外狀況。  

 如果指定的列值順序錯誤，將在 Azure 機器學習中收到此錯誤。  

**解析度：** 通過手動修改輸入資料集重新運行模組，對列值進行排序。  

|例外狀況訊息|
|------------------------|
|資料行中的值未排序。|
|列"{col_index}"中的值未排序。|
|資料集"[資料集]的列"{col_index}"中的值未排序。|


## <a name="error-0020"></a>錯誤 0002  
 如果傳遞給模組的某些資料集的資料行數目太少，就會發生例外狀況。  

 如果為模組選擇了足夠的列，您將在 Azure 機器學習中收到此錯誤。  

**解析度：** 重新訪問模組並確保欄選取器選擇正確的列數。  

|例外狀況訊息|
|------------------------|
|輸入資料集的資料行數目小於允許的最小值。|
|輸入資料集"{arg_name}中的列數小於允許的最小值。|
|輸入資料集中的列數小於允許的最小 [required_columns_count] 列。|
|輸入資料集"{arg_name}"中的列數小於允許的最小 [required_columns_count] 列。|


## <a name="error-0021"></a>錯誤 0021  
 如果傳遞給模組的某些資料集的資料列數目太少，就會發生例外狀況。  

 當資料集中沒有足夠的行來執行指定的操作時，在 Azure 機器學習中看到的此錯誤。 例如，如果輸入資料集為空，或者嘗試執行需要某些最小行數才能有效的操作，則可能會看到此錯誤。 此類操作可以包括（但不限於）基於統計方法的分組或分類、某些類型的分箱以及使用計數的學習。  

**解決方案：**

 + 打開返回錯誤的模組，並檢查輸入資料集和模組屬性。 
 + 驗證輸入資料集是否不為空，以及是否有足夠的資料行來滿足模組説明中描述的要求。  
 + 如果資料是從外部源載入的，請確保資料來源可用，並且資料定義中沒有錯誤或更改，這將導致導入過程獲取較少的行。
 + 如果要對模組上游的資料執行可能影響資料類型或值數（如清理、拆分或聯接操作）的操作，請檢查這些操作的輸出以確定返回的行數。  

|例外狀況訊息|
|------------------------|
|輸入資料集的資料列數目小於允許的最小值。|
|輸入資料集中的行數小於允許的最小 {required_rows_count} 行。|
|輸入資料集中的行數小於允許的最小 {required_rows_count} 行。 [原因]|
|輸入資料集"{arg_name}中的行數小於允許的 [required_rows_count] 行的最小值。|
|輸入資料集"{arg_name}"中的行數為 {actual_rows_count}，小於允許的 [required_rows_count] 行的最小值。|
|輸入資料集"{arg_name}"中的"{row_type}"行數為 {actual_rows_count}，小於允許的 {required_rows_count} 行的最小值。|


## <a name="error-0022"></a>錯誤 0022  
 如果輸入資料集中選取的資料行數目不等於預期的數目，就會發生例外狀況。  

 當下游模組或操作需要特定數量的列或輸入，並且提供的列或輸入太少或太多時，Azure 機器學習中可能會出現此錯誤。 例如：  

-   指定單個標籤列或鍵列，並意外選擇多個列。  
  
-   您正在重命名列，但提供的名稱多於或少於列。  
  
-   源或目標中的列數已更改或與模組使用的列數不匹配。  
  
-   您提供了輸入的逗號分隔值清單，但值數不匹配，或者不支援多個輸入。  

**解析度：** 重新訪問模組並檢查列選擇，以確保選擇了正確的列數。 驗證上游模組的輸出和下游操作的要求。  

 如果使用可以選擇多個列（列索引、所有要素、所有數位等）的列選擇選項之一，請驗證所選內容返回的列的確切數量。  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 驗證上游列的數量或類型未更改。  

 如果使用建議資料集來訓練模型，請記住，建議者需要數量有限的列，對應于使用者項對或使用者專案排名。 在訓練模型或拆分建議資料集之前刪除其他列。 有關詳細資訊，請參閱[拆分資料](split-data.md)。  

|例外狀況訊息|
|------------------------|
|輸入資料集中選取的資料行數目不等於預期的數目。|
|輸入資料集中的選定列數不等於 [expected_col_count]。|
|列選擇模式"{selection_pattern_friendly_name}"提供輸入資料集中的選定列數，不等於 {expected_col_count}。|
|列選擇模式"{selection_pattern_friendly_name}"應提供輸入資料集中選擇的 {expected_col_count} 列，但實際提供 {selected_col_count} 列。|


## <a name="error-0023"></a>錯誤 0023  

如果輸入資料集的目標資料行對目前的訓練者模組而言無效，就會發生例外狀況。  

如果目標列（如模組參數中選擇）不是有效的資料類型，包含所有缺失的值，或者未按預期分類，則會出現此錯誤。  

**解析度：** 重新訪問模組輸入以檢查標籤/目標列的內容。 確保它沒有所有缺失的值。 如果模組期望目標列是分類列，請確保目標列中有多個不同的值。  

|例外狀況訊息|
|------------------------|
|輸入資料集具有不支援的目標資料行。|
|輸入資料集具有不支援的目標列"{column_index}"。|
|對於類型 [learner_type] 的學員，輸入資料集具有不支援的目標列"{column_index}"。|


## <a name="error-0024"></a>錯誤 0024  
如果資料集未包含標籤資料行，就會發生例外狀況。  

 當模組需要標籤列並且資料集沒有標籤列時，Azure 機器學習中會發生此錯誤。 例如，對評分資料集的評估通常需要存在標籤列以計算準確性指標。  

資料集中也可能存在標籤列，但 Azure 機器學習未正確檢測到。

**解決方案：**

+ 打開建置錯誤的模組，並確定是否存在標籤列。 只要列包含您嘗試預測的單個結果（或相關變數），列的名稱或資料類型並不重要。 如果不確定哪個列具有標籤，則查找泛型名稱，如*類*或*Target*。 
+  如果資料集不包含標籤列，則標籤列可能是顯式或意外刪除的上游。 資料集也可能不是上游評分模組的輸出。
+ 要顯式將列標記為標籤列，請添加["編輯中繼資料"](edit-metadata.md)模組並串連資料組。 僅選擇標籤列，然後從 **"欄位**"下拉清單中選擇 **"標籤**"。 
+ 如果選擇錯誤的列作為標籤，則可以從 **"欄位**"中選擇 **"清除標籤**"以修復列上的中繼資料。 
  
|例外狀況訊息|
|------------------------|
|資料集中沒有標籤資料行。|
|"{dataset_name}"中沒有標籤列。|


## <a name="error-0025"></a>錯誤 0025  
 如果資料集未包含計算資料行，就會發生例外狀況。  

 如果計算模型的輸入不包含有效的分數列，則 Azure 機器學習中會發生此錯誤。 例如，使用者嘗試在使用正確的訓練模型對資料集進行評分之前對其進行評估，或者分數列被顯式丟棄到上游。 如果兩個資料集上的分數列不相容，也會發生此異常。 例如，您可能嘗試將線性遞減器的精度與二進位分類器進行比較。  

**解析度：** 重新訪問評估模型的輸入，並檢查該模型是否包含一個或多個分數列。 如果沒有，則未對資料集進行評分，或者分數列在上游模組中被丟棄。  

|例外狀況訊息|
|------------------------|
|資料集中沒有分數資料行。|
|"{dataset_name}"中沒有分數列。|
|"{dataset_name}"中沒有由"{learner_type}"生成的分數列。 請使用正確類型的學習模組來評分資料集。|


## <a name="error-0026"></a>錯誤 0026  
 如果不允許具有相同名稱的列，則會發生異常。  

 如果多個列具有相同的名稱，則 Azure 機器學習中會發生此錯誤。 接收此錯誤的一種方法是，如果資料集沒有標題列，並且自動分配列名稱：Col0、Col1 等。  

**解析度：** 如果列具有相同的名稱，請在輸入資料集和模組之間插入["編輯中繼資料"](edit-metadata.md)模組。 使用["編輯中繼資料"](edit-metadata.md)中的欄選取器選擇要重命名的列，將新名稱鍵入 **"新建"列名稱**文字方塊中。  

|例外狀況訊息|
|------------------------|
|引數中指定相同的資料行名稱。 模組不允許有相等的資料行名稱。|
|不允許參數"{arg_name_1}"和"{arg_name_2}"中的等列名稱。 請指定不同的名稱。|


## <a name="error-0027"></a>錯誤 0027  
 當兩個物件必須是相同大小但卻不是時，就會發生例外狀況。  

 這是 Azure 機器學習中的常見錯誤，可能由許多條件引起。  

**解析度：** 沒有具體的決議。 但是，您可以檢查以下條件：  

-   如果要重命名列，請確保每個清單（輸入列和新名稱清單）具有相同的項數。  
  
-   如果要聯接或串聯兩個資料集，請確保它們具有相同的架構。  
  
-   如果要聯接兩個具有多個列的資料集，請確保鍵列具有相同的資料類型，並選擇"**允許重複"並在選擇中保留列順序**。  

|例外狀況訊息|
|------------------------|
|傳遞的物件大小不一致。|
|"{friendly_name1}"的大小與"{friendly_name2}"的大小不一致。|


## <a name="error-0028"></a>錯誤 0028  
 當資料行集包含重複的資料行名稱且不允許如此時，就會發生例外狀況。  

 Azure 機器學習中出現此錯誤，當重複列名稱時;如果重複列名稱，則會出現此錯誤。也就是說，不是唯一的。  

**解析度：** 如果任何列具有相同的名稱，請在輸入資料集和引發錯誤的模組之間添加["編輯中繼資料"](edit-metadata.md)實例。 使用["編輯中繼資料](edit-metadata.md)"中的欄選取器選擇要重命名的列，並在 **"新建列名稱**文本"框中鍵入新列名稱。 如果要重命名多個列，請確保在**New 列名稱**中鍵入的值是唯一的。  

|例外狀況訊息|
|------------------------|
|資料行集包含重複的資料行名稱。|
|名稱"duplicated_name}"重複。|
|名稱"{duplicated_name}"在"{arg_name}"中重複。|
|名稱"duplicated_name}"重複。 詳細資訊： [詳細資訊]|


## <a name="error-0029"></a>錯誤 0029  
 當傳遞無效的 URI 時，就會發生例外狀況。  

 Azure 機器學習中出現此錯誤，以防傳遞無效 URI。  如果以下任一條件為 true，您將收到此錯誤：  

-   為 Azure Blob 存儲提供用於讀取或寫入的公共或 SAS URI 包含錯誤。  
  
-   SAS 的時間視窗已過期。  
  
-   通過 HTTP 源的 Web URL 表示檔或迴圈回 URI。  
  
-   通過 HTTP 的 Web URL 包含格式不正確的 URL。  
  
-   遠端源無法解析 URL。  

**解析度：** 重新訪問模組並驗證 URI 的格式。 如果資料來源是通過 HTTP 的 Web URL，請驗證目標源不是檔還是回回 URI（本地主機）。  

|例外狀況訊息|
|------------------------|
|傳遞無效的 Uri。|
|Uri"{invalid_url}"無效。|


## <a name="error-0030"></a>錯誤 0030  
 無法下載檔案時，就會發生例外狀況。  

 Azure 機器學習中出現此異常，當無法下載檔案時。 在三 （3） 次重試嘗試後嘗試從 HTTP 源讀取失敗時，您將收到此異常。  

**解析度：** 驗證 HTTP 源的 URI 是否正確，以及網站當前是否可通過 Internet 訪問。  

|例外狀況訊息|
|------------------------|
|無法下載檔案。|
|下載檔案時出錯： {file_url}。|


## <a name="error-0031"></a>錯誤 0031  
 如果資料行集的資料行數目少於所需，就會發生例外狀況。  

 如果所選列數小於所需列，則 Azure 機器學習中會發生此錯誤。  如果未選擇所需的最小列數，您將收到此錯誤。  

**解析度：** 使用**欄選取器**向列選擇添加其他列。  

|例外狀況訊息|
|------------------------|
|資料行集中的資料行數目小於所需。|
|至少應為輸入參數"{arg_name}"指定 {required_columns_count} 列。|
|至少應為輸入參數"{arg_name}"指定 {required_columns_count} 列。 指定列的實際數量為 [input_columns_count]。|


## <a name="error-0032"></a>錯誤 0032  
 如果引數不是數字，就會發生例外狀況。  

 如果參數為雙精度或 NaN，則將在 Azure 機器學習中收到此錯誤。  

**解析度：** 修改指定的參數以使用有效值。  

|例外狀況訊息|
|------------------------|
|引數不是數字。|
|"[arg_name]"不是數位。|


## <a name="error-0033"></a>錯誤 0033  
 如果引數是無限大，就會發生例外狀況。  

 如果參數是無限的，則 Azure 機器學習中會發生此錯誤。 如果參數為 或`double.NegativeInfinity``double.PositiveInfinity`，您將收到此錯誤。  

**解析度：** 將指定的參數修改為有效值。  

|例外狀況訊息|
|------------------------|
|參數必須是有限的。|
|"{arg_name}"不是有限的。|


## <a name="error-0034"></a>錯誤 0034  
 如果給定的使用者-項目配對存在一個以上的評等，就會發生例外狀況。  

 如果使用者項對具有多個分級值，則建議中會出現此錯誤。  

**解析度：** 確保使用者項對僅具有一個評級值。  

|例外狀況訊息|
|------------------------|
|資料集中的值存在多個分級。|
|評級預測資料表中的使用者 [使用者] 和項 [項] 的多個評級。|
|[資料集]中使用者 [使用者] 和項 [項] 的多個評級。|


## <a name="error-0035"></a>錯誤 0035  
 如果沒有提供指定使用者或項目的特徵，就會發生例外狀況。  

 Azure 機器學習中出現此錯誤，您正在嘗試使用建議模型進行評分，但找不到要素向量。  

**解決方案：**

匹配框推薦器具有某些要求，在使用專案功能或使用者功能時必須滿足這些要求。  此錯誤指示您作為輸入提供的使用者或項缺少要素向量。 確保每個使用者或專案的資料中都有要素向量可用。  

 例如，如果您使用使用者的年齡、位置或收入等功能訓練推薦模型，但現在想要為在培訓期間未看到的新使用者創建分數，則必須提供一些等效的功能集（即年齡、位置和新使用者的收入值），以便對它們做出適當的預測。 

 如果對這些使用者沒有任何功能，請考慮要素工程以生成適當的功能。  例如，如果您沒有個人使用者年齡或收入值，則可以生成用於一組使用者的近似值。 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 解決方案不適用於您的案件？ 歡迎您發送有關本文的回饋，並提供有關方案的資訊，包括模組和列中的行數。 將來，我們將使用此資訊提供更詳細的故障排除步驟。

|例外狀況訊息|
|------------------------|
|未提供特徵給必要的使用者或項目。|
|需要 [required_feature_name] 的功能，但未提供。|


## <a name="error-0036"></a>錯誤 0036  
 如果對給定的使用者或項目提供多個特徵向量，就會發生例外狀況。  

 如果多次定義要素向量，則 Azure 機器學習中會發生此錯誤。  

**解析度：** 確保要素向量未定義多次。  

|例外狀況訊息|
|------------------------|
|使用者或項目的特徵定義重複。|


## <a name="error-0037"></a>錯誤 0037  
 如果指定多個標籤資料行，但只允許一個，就會發生例外狀況。  

 如果選擇多個列作為新標籤列，則 Azure 機器學習中會發生此錯誤。 大多數受監督的學習演算法需要將單個列標記為目標或標籤。  

**解析度：** 請確保選擇單個列作為新標籤列。  

|例外狀況訊息|
|------------------------|
|指定多個標籤資料行。|
|在"{dataset_name}"中指定了多個標籤列。|


## <a name="error-0039"></a>錯誤 0039  
 如果操作失敗，則會發生異常。  

 當無法完成內部操作時，Azure 機器學習中會發生此錯誤。  

**解析度：** 此錯誤是由許多條件引起的，並且沒有具體的補救措施。  
 下表包含此錯誤的泛型消息，隨後是條件的特定說明。 

 如果沒有詳細資訊，[請發送回饋](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)並提供有關建置錯誤和相關條件的模組的資訊。

|例外狀況訊息|
|------------------------|
|作業失敗。|
|完成操作時出錯："{failed_operation}"。|
|完成操作時出錯："{failed_operation}"。 原因："[原因]"。|


## <a name="error-0042"></a>錯誤 0042  
 無法將資料行轉換成另一種類型時，就會發生例外狀況。  

 當無法將列轉換為指定類型時，Azure 機器學習中會發生此錯誤。  如果模組需要特定的資料類型（如日期時間、文本、浮點編號或整數），您將收到此錯誤，但無法將現有列轉換為所需類型。  

例如，您可以選擇一列，並嘗試將其轉換為數字資料類型，用於數學運算，如果列包含無效資料，則獲取此錯誤。 

如果嘗試使用包含浮點數或許多唯一值的列作為分類列，則可能會收到此錯誤。 

**解決方案：**

+ 打開建置錯誤的模組的説明頁，並驗證資料類型要求。
+ 查看輸入資料集中列的資料類型。
+ 檢查源自所謂的無架構資料來源的資料。
+ 檢查資料集中缺少的值或可能阻止轉換為所需資料類型的特殊字元。 
    + 數位資料類型應一致：例如，在整數列中檢查浮點數。
    + 在數位列中查找文本字串或 NA 值。 
    + 布林值可以轉換為適當的表示形式，具體取決於所需的資料類型。
    + 檢查文本列中的非單碼字元、選項卡字元或控制項字元
    + 日期時間資料應一致，以避免建模錯誤，但由於多種格式，清理可能很複雜。 考慮使用 <!--the [Execute R Script](execute-r-script.md) or -->[執行 Python 腳本](execute-python-script.md)模組以執行清理。  
+ 如有必要，修改輸入資料集中的值，以便可以成功轉換列。 修改可能包括分箱、截斷或舍入操作、消除異常值或歸因缺失值。 有關機器學習中的一些常見資料轉換方案，請參閱以下文章：
    + [清除遺漏的資料](clean-missing-data.md)
    + [將資料標準化](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 解決方案不明確，還是不適用於您的案件？ 歡迎您發送有關本文的回饋，並提供有關方案的資訊，包括模組和列的資料類型。 將來，我們將使用此資訊提供更詳細的故障排除步驟。  

|例外狀況訊息|
|------------------------|
|不允許轉換。|
|無法將類型 [type1] 的列轉換為類型 [type2] 的列。|
|無法將類型 [type1] 的列"{col_name1}"轉換為類型 [type2]的列。|
|無法將類型 [type1] 的列"{col_name1}轉換為類型 [type2] 的"{col_name2}"列。|


## <a name="error-0044"></a>錯誤 0044  
 無法從現有值衍生資料行的元素類型時，就會發生例外狀況。  

 當無法推斷資料集中列或列的類型時，Azure 機器學習中會發生此錯誤。 這通常發生在將兩個或多個資料集與不同的元素類型串聯時。 如果 Azure 機器學習無法確定能夠表示列或列中的所有值而不丟失資訊的常見類型，它將生成此錯誤。  

**解析度：** 確保要合併的兩個資料集中給定列中的所有值都具有相同的類型（數位、布林、分類、字串、日期等），或者可以強制轉換為同一類型。  

|例外狀況訊息|
|------------------------|
|無法衍生資料行的元素類型。|
|無法派生列"{column_name}" 衍生元素類型 -- 所有元素都是空引用。|
|無法派生資料集"{dataset_name}"列"{column_name}"的元素類型 - 所有元素都是空引用。|


## <a name="error-0045"></a>錯誤 0045  
 因為來源中的混合元素類型而無法建立資料行時，就會發生例外狀況。  

 當要組合的兩個資料集的元素類型不同時，將生成 Azure 機器學習中的此錯誤。  

**解析度：** 確保要合併的兩個資料集中給定列中的所有數值型別相同（數位、布林、分類、字串、日期等）。  

|例外狀況訊息|
|------------------------|
|無法建立混合元素類型的資料行。|
|無法創建具有混合元素類型的 id"{column_id}"的列：<br />資料類型[row_1]，[column_id]"type_1"。"，"column_id"。"，"column_id"。"，"column_id"表示。 <br />資料類型[row_2]，[column_id]是"{type_2}"。|
|無法創建具有混合元素類型的 id"{column_id}"的列：<br />鍵入塊 [chunk_id_1] 是"{type_1}"。 <br />類型塊 [chunk_id_2] 是"{type_2}"，塊大小： {chunk_size}。|


## <a name="error-0046"></a>錯誤 0046  
 無法在指定的路徑上建立目錄時，就會發生例外狀況。  

 當無法在指定的路徑上創建目錄時，Azure 機器學習中會發生此錯誤。 如果 Hive 查詢的輸出目錄路徑的任何部分不正確或無法訪問，您將收到此錯誤。  

**解析度：** 重新訪問模組並驗證目錄路徑格式正確，以及當前憑據是否可訪問該模組。  

|例外狀況訊息|
|------------------------|
|請指定有效的輸出目錄。|
|無法創建目錄：[路徑]。 請指定有效的路徑。|


## <a name="error-0047"></a>錯誤 0047  
 如果傳遞給模組的某些資料集的特徵資料行數目太少，就會發生例外狀況。  

 如果到培訓的輸入資料集不包含演算法所需的最小列數，則會出現此錯誤。 通常，資料集為空或僅包含訓練列。  

**解析度：** 重新訪問輸入資料集，以確保除了標籤列之外還有一個或多個其他列。  

|例外狀況訊息|
|------------------------|
|輸入資料集的特徵資料行數目小於允許的最小值。|
|輸入資料集中的要素列數小於允許的最小 [required_columns_count] 列。|
|輸入資料集"{arg_name}"中的要素列數小於允許的 [required_columns_count] 列的最小值。|


## <a name="error-0048"></a>錯誤 0048  
 無法開啟檔案時，就會發生例外狀況。  

 當無法打開檔進行讀取或寫入時，Azure 機器學習中會發生此錯誤。 由於以下原因，您可能會收到此錯誤：  

-   容器或檔（blob）不存在  
  
-   檔或容器的存取層級不允許訪問該檔  
  
-   檔太大，無法讀取或格式錯誤  

**解析度：** 重新訪問要閱讀的模組和檔。  

 驗證容器和檔的名稱是否正確。  

 使用 Azure 經典門戶或 Azure 存儲工具驗證您是否具有訪問該檔的許可權。  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|例外狀況訊息|
|------------------------|
|無法開啟檔案。|
|打開檔時出錯： [file_name]。|
|打開檔時出錯： [file_name]。 存儲異常消息：[例外]。|


## <a name="error-0049"></a>錯誤 0049  
 無法剖析檔案時，就會發生例外狀況。  

 當無法分析檔時，Azure 機器學習中會發生此錯誤。 如果[導入資料](import-data.md)模組中選擇的檔案格式與檔的實際格式不匹配，或者檔包含無法識別的字元，您將收到此錯誤。  

**解析度：** 如果模組與檔的格式不匹配，請重新訪問模組並更正該檔案格式選擇。 如果可能，請檢查該檔以確認該檔不包含任何非法字元。  

|例外狀況訊息|
|------------------------|
|無法剖析檔案。|
|分析 {file_format} 檔時出錯。|
|分析 {file_format} 檔時出錯：{file_name}。|
|分析 {file_format} 檔時出錯。 原因：[failure_reason]。|
|分析 {file_format} 檔時出錯：{file_name}。 原因：[failure_reason]。|


## <a name="error-0052"></a>錯誤 0052  
 如果未正確指定 Azure 儲存體帳戶金鑰，就會發生例外狀況。  

 如果用於訪問 Azure 存儲帳戶的金鑰不正確，則 Azure 機器學習中會發生此錯誤。 例如，如果在複製和粘貼時截斷 Azure 存儲金鑰，或者使用了錯誤的金鑰，則可能會看到此錯誤。  

 有關如何獲取 Azure 存儲帳戶的金鑰的詳細資訊，請參閱[查看、複製和重新生成存儲訪問金鑰](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  

**解析度：** 重新訪問模組並驗證 Azure 存儲金鑰組帳戶是否正確;請檢查 Azure 存儲金鑰是否對帳戶正確。如有必要，請從 Azure 經典門戶再次複製金鑰。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶金鑰不正確。|


## <a name="error-0053"></a>錯誤 0053  
 當沒有使用者功能或匹配框建議的專案時，會出現異常。  

 在 Azure 機器學習中，當找不到要素向量時，將生成此錯誤。  

**解析度：** 確保輸入資料集中存在要素向量。  

|例外狀況訊息|
|------------------------|
|需要使用者特徵或/和項目，但未提供。|


## <a name="error-0056"></a>錯誤 0056  
 如果為操作選擇的列違反要求，則會發生異常。  

 Azure 機器學習中出現此錯誤，當您為需要列為特定資料類型的操作選擇列時。 

 如果列是正確的資料類型，則也可能發生此錯誤，但您使用的模組要求將列標記為要素、標籤或分類列。  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**解決方案：**

1.  查看當前選擇的列的資料類型。 

2. 確定所選列是分類列、標籤列還是要素列。  
  
3.  查看選擇列的模組的說明主題，以確定資料類型或列使用方式是否有特定要求。  
  
3.  使用["編輯中繼資料](edit-metadata.md)"在此操作期間更改列類型。 如果需要，請確保使用另一個實例["編輯中繼資料](edit-metadata.md)"將列類型更改回其原始值。  

|例外狀況訊息|
|------------------------|
|一或多個選取的資料行不在允許的類別中。|
|名稱為"{col_name}"的列不在允許的類別中。|


## <a name="error-0057"></a>錯誤 0057  
 當您嘗試建立的檔案或 Blob 已存在時，就會發生例外狀況。  

 當您使用[匯出資料](export-data.md)模組或其他模組將 Azure 機器學習中管道的結果保存到 Azure Blob 存儲，但嘗試創建已存在的檔或 Blob 時，將出現此異常。   

**解決方案：**

 僅當以前將屬性**Azure Blob 存儲寫入模式**設置為 **"錯誤**"時，才會收到此錯誤。 根據設計，如果您嘗試將資料集寫入已存在的 Blob，則此模組將引發錯誤。

 - 打開模組屬性並將屬性 Azure **blob 存儲寫入模式**更改為**覆蓋**。
 - 或者，您可以鍵入其他目標 Blob 或檔的名稱，並確保指定不存在的 Blob。  

|例外狀況訊息|
|------------------------|
|檔案或 Blob 已經存在。|
|檔或 Blob"{file_path}"已存在。|


## <a name="error-0058"></a>錯誤 0058  
 如果資料集不包含預期標籤列，則 Azure 機器學習中會發生此錯誤。  

 當提供的標籤列與學員預期的資料或資料類型不匹配或值錯誤時，也可能發生此異常。 例如，在訓練二進位分類器時，使用實值標籤列時會產生此異常。  

**解析度：** 解析度取決於您使用的學員或培訓師以及資料集中列的資料類型。 首先，驗證機器學習演算法或培訓模組的要求。  

 重新訪問輸入資料集。 驗證預期被視為標籤的列具有適合您所創建模型的資料類型。  

 檢查輸入是否缺失值，並在必要時消除或替換它們。  

 如有必要，請添加["編輯中繼資料"](edit-metadata.md)模組，並確保標籤列被標記為標籤。  

|例外狀況訊息|
|------------------------|
|標籤列值和評分標籤列值不可比較。|
|標籤列在"{dataset_name}"中未按預期進行。|
|標籤列在"[dataset_name]"[原因]中未按預期進行。|
|"{dataset_name}"中不需要標籤列"{column_name}"。|
|標籤列"{column_name}"在"{dataset_name}"，"原因"中不預期。|


## <a name="error-0059"></a>錯誤 0059  
 如果無法剖析資料行選擇器中所指定的資料行索引，就會發生例外狀況。  

 如果無法分析使用欄選取器時指定的列索引，則 Azure 機器學習中會發生此錯誤。  當列索引的格式無效，無法解析時，您將收到此錯誤。  

**解析度：** 修改列索引以使用有效的索引值。  

|例外狀況訊息|
|------------------------|
|無法剖析一個或多個指定的資料行索引或索引範圍。|
|無法分析列索引或範圍"{column_index_or_range}"。|


## <a name="error-0060"></a>錯誤 0060  
 當資料行選擇器中指定超出範圍的資料行範圍時，就會發生例外狀況。  

 在"欄選取器"中指定了範圍外列範圍時，Azure 機器學習中會發生此錯誤。 如果列選取器中的列範圍與資料集中的列不對應，您將收到此錯誤。  

**解析度：** 修改列選取器中的列範圍以對應于資料集中的列。  

|例外狀況訊息|
|------------------------|
|指定的資料行索引範圍無效或超出範圍。|
|列範圍"{column_range}"無效或範圍不一。|


## <a name="error-0061"></a>錯誤 0061  
 當您嘗試將一個資料列加入至 DataTable，但包含資料行數目與資料表不同時，就會發生例外狀況。  

 當您嘗試將行添加到具有與資料集不同的資料集時，Azure 機器學習中會發生此錯誤。  如果添加到資料集的行與輸入資料集的列數不同，您將收到此錯誤。  如果列數不同，則無法將該行追加到資料集。  

**解析度：** 修改輸入資料集，使列數與添加的行數相同，或修改添加的行，以具有與資料集相同的列數。  

|例外狀況訊息|
|------------------------|
|所有的資料表必須有相同數目的資料行。|
|塊"{chunk_id_1}"中的列與塊"{chunk_id_2}"不同，塊大小為 {chunk_size}。|
|檔"{filename_1}"（計數{column_count_1}）中的列計數與檔"{filename_2}"（count{column_count_2}）不同。|


## <a name="error-0062"></a>錯誤 0062  
 當您嘗試比較兩個具有不同學習類型的模型時，就會發生例外狀況。  

 當無法比較兩個不同評分資料集的評估指標時，將生成 Azure 機器學習中的此錯誤。 在這種情況下，無法比較用於生成兩個評分資料集的模型的有效性。  

**解析度：** 驗證評分結果是否由同一類型的機器學習模型（二進位分類、回歸、多類分類、推薦、聚類、異常檢測等）生成。比較的所有模型都必須具有相同的學員類型。  

|例外狀況訊息|
|------------------------|
|所有模型都必須都具有相同的學習類型。|
|獲得不相容的學員類型："{actual_learner_type}"。 預期的學員類型是："{expected_learner_type_list}"。|


## <a name="error-0064"></a>錯誤 0064  
 如果未正確指定 Azure 儲存體帳戶金鑰或儲存體金鑰，就會發生例外狀況。  

 如果 Azure 存儲帳戶名稱或存儲金鑰指定不正確，則 Azure 機器學習中會發生此錯誤。 如果您輸入了不正確的存儲帳戶的帳戶名稱或密碼，您將收到此錯誤。 如果您手動輸入帳戶名稱或密碼，則可能發生這種情況。 如果帳戶已被刪除，也可能發生這種情況。  

**解析度：** 驗證帳戶名稱和密碼輸入正確，以及帳戶是否存在。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶名稱或儲存體金鑰不正確。|
|帳戶名稱的 Azure 存儲帳戶名稱"{account_name}"或存儲金鑰不正確。|


## <a name="error-0065"></a>錯誤 0065  
 如果未正確指定 Azure Blob 名稱，就會發生例外狀況。  

 如果指定的 Azure blob 名稱不正確，則 Azure 機器學習中會發生此錯誤。  如果出現以下情況，您將收到錯誤：  

-   在指定的容器中找不到 blob。  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   僅當格式為 Excel 或帶有編碼的 CSV 時，才在[導入資料](import-data.md)請求中指定容器為源;不允許將這些格式串聯容器中所有 Blob 的內容。  
  
-   SAS URI 不包含有效 Blob 的名稱。  

**解析度：** 重新訪問引發異常的模組。 驗證指定的 Blob 是否確實存在在存儲帳戶中的容器中，以及許可權是否允許您查看 Blob。 如果具有具有編碼格式的 Excel 或 CSV，請驗證輸入是否為表單**容器名/檔案名**。 驗證 SAS URI 是否包含有效 Blob 的名稱。  

|例外狀況訊息|
|------------------------|
|Azure 存儲 Blob 名稱不正確。|
|Azure 存儲 blob 名稱"{blob_name}"不正確。|
|首碼為"{blob_name_prefix}"的 Azure 存儲 blob 名稱不存在。|
|找不到容器"{container_name}"下的任何 Azure 存儲 blob。|
|找不到具有萬用字元路徑"{blob_wildcard_path}"的任何 Azure 存儲 Blob。|


## <a name="error-0066"></a>錯誤 0066  
 如果資源無法上傳至 Azure Blob，就會發生例外狀況。  

 如果資源無法上載到 Azure Blob，則 Azure 機器學習中會發生此錯誤。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 兩者都與包含輸入檔的帳戶保存到同一 Azure 存儲帳戶。  

**解析度：** 重新訪問模組。 驗證 Azure 帳戶名稱、存儲金鑰和容器是否正確，以及帳戶是否具有寫入容器的許可權。  

|例外狀況訊息|
|------------------------|
|無法將資料上傳至 Azure 儲存體。|
|檔"{source_path}"不能以"{dest_path}"身份上載到 Azure 存儲。|


## <a name="error-0067"></a>錯誤 0067  
 如果資料集具有不同於預期的資料行數目，就會發生例外狀況。  

 如果資料集的列數與預期數不同，則會出現 Azure 機器學習中的此錯誤。  當資料集中的列數與模組在執行期間期望的列數不同時，您將收到此錯誤。  

**解析度：** 修改輸入資料集或參數。  

|例外狀況訊息|
|------------------------|
|datatable 中的資料行數目不符預期。|
|資料集"{dataset_name}"中意外的列數。|
|預期的"{expected_column_count}"列，但卻找到"{actual_column_count}"列。|
|在輸入資料集"{dataset_name}"中，預期"{expected_column_count}"列，但卻找到"{actual_column_count}"列。|


## <a name="error-0068"></a>錯誤 0068  
 如果指定的 Hive 指令碼不正確，就會發生例外狀況。  

 如果 Hive QL 腳本中存在語法錯誤，或者 Hive 解譯器在執行查詢或腳本時遇到錯誤，則會出現此錯誤。  

**解決方案：**

來自 Hive 的錯誤訊息通常在錯誤日誌中報告回來，以便您可以根據特定錯誤執行操作。 

+ 打開模組並檢查查詢是否有錯誤。  
+ 通過登錄到 Hadoop 群集的 Hive 主控台並執行查詢，驗證查詢在 Azure 機器學習之外是否正常工作。  
+ 請嘗試將注釋放在單獨的行中，而不是將可執行語句和注釋混合在一行中。  

### <a name="resources"></a>資源

有關學習 Hive 查詢的説明，請參閱以下文章：

+ [建立 Hive 資料表，並從 Azure Blob 儲存體載入資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [使用 Hive 查詢流覽表中的資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [針對使用 Hive 查詢之 Hadoop 叢集中的資料建立特性](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [適用于 SQL 使用者備忘表 （PDF） 的蜂巢](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|例外狀況訊息|
|------------------------|
|Hive 指令碼不正確。|


## <a name="error-0069"></a>錯誤 0069  
 如果指定的 SQL 指令碼不正確，就會發生例外狀況。  

 如果指定的 SQL 腳本存在語法問題，或者腳本中指定的列或表無效，則 Azure 機器學習中會發生此錯誤。 

 如果 SQL 引擎在執行查詢或腳本時遇到任何錯誤，您將收到此錯誤。 SQL 錯誤訊息通常在錯誤日誌中報告回來，以便您可以根據特定錯誤執行操作。  

**解析度：** 重新訪問模組並檢查 SQL 查詢是否有錯誤。  

 通過直接登錄到資料庫伺服器並執行查詢，驗證查詢在 Azure ML 之外是否正常工作。  

 如果模組異常報告了 SQL 生成的消息，則根據報告的錯誤執行操作。 例如，錯誤訊息有時包含有關可能錯誤的特定指南：
+ *沒有這樣的列或缺少資料庫*，表明您可能鍵入了錯誤的列名稱。 如果確定列名稱正確，請嘗試使用括弧或引號來包含列識別碼。
+ *SQL 關鍵字\>附近的\<SQL 邏輯錯誤*，指示在指定的關鍵字之前可能存在語法錯誤

  
|例外狀況訊息|
|------------------------|
|SQL 指令碼不正確。|
|SQL 查詢"{sql_query}"不正確。|
|SQL 查詢"{sql_query}"不正確。 異常消息：[例外]。|


## <a name="error-0070"></a>錯誤 0070  
 當您嘗試存取不存在的 Azure 資料表時，就會發生例外狀況。  

 當您嘗試訪問不存在的 Azure 表時，Azure 機器學習中會發生此錯誤。 如果在 Azure 存儲中指定表，則將收到此錯誤，該表在讀取或寫入 Azure 表存儲時不存在。 如果鍵入所需表的名稱錯誤，或者目標名稱和存儲類型不匹配，則可能發生此情況。 例如，您打算從表中讀取，而是輸入 blob 的名稱。  

**解析度：** 重新訪問模組以驗證表的名稱是否正確。  

|例外狀況訊息|
|------------------------|
|Azure 資料表不存在。|
|Azure 表"{table_name}"不存在。|


## <a name="error-0072"></a>錯誤 0072  
 連接逾時時會出現異常。  

 Azure 機器學習中出現此錯誤，當連接逾時時發生。如果當前資料來源或目標存在連接問題（如互聯網連線速度慢，或者資料集較大和/或要在資料中讀取的 SQL 查詢執行複雜的處理，您將收到此錯誤。  

**解析度：** 確定當前與 Azure 存儲或 Internet 連線速度慢的問題。  

|例外狀況訊息|
|------------------------|
|發生連接逾時。|


## <a name="error-0073"></a>錯誤 0073  
 如果將資料行轉換成其他類型時發生錯誤，就會發生例外狀況。  

 當無法將列轉換為其他類型時，Azure 機器學習中會發生此錯誤。  如果模組需要特定類型，並且無法將列轉換為新類型，您將收到此錯誤。  

**解析度：** 修改輸入資料集，以便可以根據內部異常轉換列。  

|例外狀況訊息|
|------------------------|
|無法轉換資料行。|
|無法將列轉換為 [target_type]。|


## <a name="error-0075"></a>錯誤 0075  
當量化資料集使用無效的分類收納函數時，就會發生例外狀況。  

當您嘗試使用不支援的方法對資料進行裝箱時，或者參數組合無效時，Azure 機器學習中會發生此錯誤。  

**解決方案：**

此事件的錯誤處理在 Azure 機器學習的早期版本中引入，該版本允許對分箱方法進行更多自訂。 目前，所有分箱方法都基於下拉清單中的選擇，因此從技術上講，不應再出現此錯誤。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|例外狀況訊息|
|------------------------|
|使用無效的分類收納函數。|


## <a name="error-0077"></a>錯誤 0077  
 當通過未知 blob 檔寫入模式時，將發生異常。  

 如果在 Blob 檔目標或源的規範中傳遞無效參數，則 Azure 機器學習中會發生此錯誤。  

**解析度：** 在幾乎所有將資料導入或匯出資料到 Azure Blob 存儲的模組中，控制寫入模式的參數值都使用下拉清單分配;因此，無法傳遞無效值，並且不應出現此錯誤。 此錯誤將在以後的版本中棄用。  

|例外狀況訊息|
|------------------------|
|不支援的 Blob 寫入模式。|
|不支援的 blob 寫入模式：[blob_write_mode]。|


## <a name="error-0078"></a>錯誤 0078  
 當[導入資料的](import-data.md)HTTP 選項收到指示重定向的 3xx 狀態碼時，將發生異常。  

 當[導入資料的](import-data.md)HTTP 選項收到指示重定向的 3xx（301、302、304 等）狀態碼時，Azure 機器學習中會發生此錯誤。 如果您嘗試連接到將瀏覽器重定向到另一個頁面的 HTTP 源，您將收到此錯誤。 出於安全原因，不允許重定向網站作為 Azure 機器學習的資料來源。  

**解析度：** 如果網站是受信任的網站，請直接輸入重定向的 URL。  

|例外狀況訊息|
|------------------------|
|不允許進行 Http 重定向。|


## <a name="error-0079"></a>錯誤 0079  
 如果未正確指定 Azure 儲存體容器名稱，就會發生例外狀況。  

 如果 Azure 存儲容器名稱指定不正確，則 Azure 機器學習中會發生此錯誤。 如果在寫入 Azure Blob 存儲時未使用**從容器選項開始的路徑**指定容器和 Blob（檔）名稱，您將收到此錯誤。  

**解析度：** 重新訪問[匯出資料](export-data.md)模組，並驗證 blob 的指定路徑是否同時包含容器和檔案名（格式**容器/檔案名**）。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體容器名稱不正確。|
|Azure 存儲容器名稱"{container_name}"不正確;因此，請執行以下操作。應使用格式容器/blob 的容器名稱。|


## <a name="error-0080"></a>錯誤 0080  
 當模組不允許資料行遺漏所有值時，就會發生例外狀況。  

 當模組使用的一個或多個列包含所有缺失值時，將生成 Azure 機器學習中的此錯誤。 例如，如果模組正在計算每列的聚合統計資訊，則無法對不包含資料的列進行操作。 在這種情況下，模組執行將在此例外的情況下停止。  

**解析度：** 重新訪問輸入資料集並刪除包含所有缺失值的任何列。  

|例外狀況訊息|
|------------------------|
|不允許完全為遺漏值的資料行。|
|列 [col_index_or_name] 缺少所有值。|


## <a name="error-0081"></a>錯誤 0081  
 在 PCA 模組中，如果要減少到的維度數目等於輸入資料集的特徵資料行數目 (包含至少一個疏鬆特徵資料行)，就會發生例外狀況。  

 如果滿足以下條件，則生成 Azure 機器學習中的此錯誤：（a） 輸入資料集至少有一個稀疏列，（b） 請求的最終維度數與輸入維度數相同。  

**解析度：** 請考慮將輸出中的尺寸數量減少到小於輸入中的尺寸數。 這在 PCA 應用中是常見的。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|例外狀況訊息|
|------------------------|
|包含要減少維度數量的稀疏特徵資料行的資料集，應該小於特徵資料行數目。|


## <a name="error-0082"></a>錯誤 0082  
 模型無法成功還原序列化時，就會發生例外狀況。  

 當保存的機器學習模型或轉換無法由較新版本的 Azure 機器學習運行時由於重大更改而載入時，就會發生 Azure 機器學習中的此錯誤。  

**解析度：** 必須重新運行生成模型或轉換的訓練管道，並且必須重新保存模型或轉換。  

|例外狀況訊息|
|------------------------|
|模型無法還原序列化，因為它可能是以較舊的序列化格式序列化。 請重新訓練並重新保存模型。|


## <a name="error-0083"></a>錯誤 0083  
 如果用於定型的資料集不適用於具體的學習類型，就會發生例外狀況。  

 當資料集與正在培訓的學員不相容時，將生成 Azure 機器學習中的此錯誤。 例如，資料集可能在每個行中至少包含一個缺失值，因此，在培訓期間將跳過整個資料集。 在其他情況下，某些機器學習演算法（如異常檢測）不期望存在標籤，如果資料集中存在標籤，則可能會引發此異常。  

**解析度：** 請參閱用於檢查輸入資料集要求的學員的文檔。 檢查列以查看所有必需的列都存在。  

|例外狀況訊息|
|------------------------|
|用於定型的資料集無效。|
|[data_name] 包含用於培訓的無效資料。|
|[data_name] 包含用於培訓的無效資料。 學員類型： [learner_type]。|
|[data_name] 包含用於培訓的無效資料。 學員類型： [learner_type]。 原因： [原因]。|
|無法對訓練資料 [data_name] 應用"{action_name}"操作。 原因： [原因]。|


## <a name="error-0084"></a>錯誤 0084  
 評估從 R 指令碼產生的分數時發生例外狀況。 這目前不支援。  

 如果嘗試使用其中一個模組來評估具有包含分數的 R 腳本輸出的模型，則 Azure 機器學習中會出現此錯誤。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|當前不支援評估自訂模型生成的分數。|


## <a name="error-0085"></a>錯誤 0085  
 指令碼評估失敗並發生錯誤時，就會發生例外狀況。  

 Azure 機器學習中出現此錯誤，當您運行包含語法錯誤的自訂腳本時。  

**解析度：** 在外部編輯器中查看代碼並檢查錯誤。  

|例外狀況訊息|
|------------------------|
|指令碼評估期間發生錯誤。|
|在指令碼評估期間發生下列錯誤，請檢視輸出記錄檔以取得詳細資訊：<br />---------- 從 {script_language} 個解譯器----------開始錯誤訊息<br />[消息]<br />----------來自 {script_language} 個解譯器----------的錯誤訊息結束|


## <a name="error-0090"></a>錯誤 0090  
 當 Hive 表創建失敗時，將發生異常。  

 當您使用[匯出資料](export-data.md)或其他選項將資料保存到 HDInsight 群集且無法創建指定的 Hive 表時，Azure 機器學習中會出現此錯誤。  

**解析度：** 檢查與群集關聯的 Azure 存儲帳戶名稱，並驗證在模組屬性中是否使用相同的帳戶。  

|例外狀況訊息|
|------------------------|
|無法創建 Hive 表。 對於 HDInsight 群集，請確保與群集關聯的 Azure 存儲帳戶名稱與通過模組參數傳入的內容相同。|
|無法創建 Hive 表"{table_name}"。 對於 HDInsight 群集，請確保與群集關聯的 Azure 存儲帳戶名稱與通過模組參數傳入的內容相同。|
|無法創建 Hive 表"{table_name}"。 對於 HDInsight 群集，請確保與群集關聯的 Azure 存儲帳戶名稱為"{cluster_name}"。|


## <a name="error-0102"></a>錯誤 0102  
 在無法提取 ZIP 檔時引發。  

 Azure 機器學習中出現此錯誤，當您導入帶有 .zip 副檔名的壓縮包，但包不是 ZIP 檔案，或者該檔不使用支援的 zip 格式。  

**解析度：** 確保所選檔是有效的 .ZIP 檔案，並且使用受支援的壓縮演算法之一對其進行壓縮。  

 如果在以壓縮格式導入資料集時出現此錯誤，請驗證所有包含的檔是否都使用受支援的檔案格式之一，並且採用 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 嘗試將所需檔讀取到新的壓縮壓縮資料夾，然後嘗試再次添加自訂模組。  

|例外狀況訊息|
|------------------------|
|給定 ZIP 檔案格式不正確。|


## <a name="error-0105"></a>錯誤 0105  
 當模組定義檔包含不受支援的參數類型時，將顯示此錯誤  
  
 在 Azure 機器學習中，當創建自訂模組 xml 定義時，會產生此錯誤，並且定義中的參數或參數的類型與支援的類型不匹配。  
  
**解析度：** 確保自訂模組 xml 定義檔中任何**Arg**元素的類型屬性是受支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的參數類型。|  
|指定的不支援參數類型{0}'|  


## <a name="error-0107"></a>錯誤 0107  
 當模組定義檔定義不支援的輸出類型時引發  
  
 當自訂模組 xml 定義中的輸出埠類型與支援的類型不匹配時，將生成 Azure 機器學習中的此錯誤。  
  
**解析度：** 確保自訂模組 xml 定義檔中的 Output 元素的類型屬性是受支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸出類型。|  
|指定的不支援的輸出類型"{output_type}"。|  


## <a name="error-0125"></a>錯誤 0125  
 當多個資料集的架構不匹配時引發。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|資料集結構描述不符。|


## <a name="error-0127"></a>錯誤 0127  
 影像像素大小超過允許的限制  

 如果要從圖像資料集讀取圖像進行分類，並且圖像大於模型可以處理的圖像，則會發生此錯誤。  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|例外狀況訊息|
|------------------------|
|影像像素大小超過允許的限制。|
|檔中的圖像圖元大小"{file_path}"超過了允許的限制："{size_limit}"。|


## <a name="error-0128"></a>錯誤 0128  
 分類列的條件概率數超過限制。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|分類列的條件概率數超過限制。|
|分類列的條件概率數超過限制。 列"{column_name_or_index_1}"和"{column_name_or_index_2}"是有問題的對。|


## <a name="error-0129"></a>錯誤 0129  
 資料集中的列數超過允許的限制。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|資料集中的列數超過允許的限制。|
|資料集中的列數（在"{dataset_name}"中超過允許的列數）。|
|"{dataset_name}"中資料集中的列數超過了允許的"[component_name}"限制。|
|資料集中的列數（在"{dataset_name}"中超過允許的"{limit_columns_count}"限制[component_name]]。|


## <a name="error-0134"></a>錯誤 0134
當標籤列丟失或標記的行數量不足時，將發生異常。  

當模組需要標籤列，但列選擇中未包含一個，或者標籤列缺少的值太多時，將發生此錯誤。

當以前的操作更改資料集，使下游操作可用的行不足時，也可能發生此錯誤。 例如，假設您使用**分區和示例**模組中的運算式按值劃分資料集。 如果運算式未找到匹配項，則由分區生成的資料集之一將為空。

解決方案： 

 如果在列選擇中包含標籤列，但無法識別該列，請使用["編輯中繼資料"](edit-metadata.md)模組將其標記為標籤列。

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  然後，可以使用["清理缺失資料"](clean-missing-data.md)模組刪除標籤列中缺少值的行。 

 檢查輸入資料集，以確保它們包含有效的資料，並有足夠的行來滿足操作的要求。 如果許多演算法需要一些最小數量的行資料，則許多演算法將建置錯誤消息，但資料僅包含幾行或標頭。

|例外狀況訊息|
|------------------------|
|當標籤列丟失或標記的行數量不足時，將發生異常。|
|當標籤列丟失或標記的行少於 {required_rows_count}時，將發生異常。|
|當資料集 [dataset_name] 中的標籤列缺失或標記的行小於 [required_rows_count] 時，將發生異常。|


## <a name="error-0138"></a>錯誤 0138  
 記憶體已耗盡，無法完成模組的運行。 對資料集進行向下採樣可能有助於緩解問題。  

 當正在運行的模組需要的記憶體多於 Azure 容器中可用的記憶體時，將發生此錯誤。 如果使用大型資料集，並且當前操作無法放入記憶體中，則可能發生此情況。  

**解析度：** 如果嘗試讀取大型資料集，但操作無法完成，則對資料集進行向下採樣可能會有所説明。  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|例外狀況訊息|
|------------------------|
|記憶體已耗盡，無法完成模組的運行。|
|記憶體已耗盡，無法完成模組的運行。 詳細資訊： [詳細資訊]|


## <a name="error-0141"></a>錯誤 0141  
 如果分類列和字串列中所選數位列和唯一值的數量太小，則會出現異常。  

 當所選列中沒有足夠的唯一值來執行操作時，Azure 機器學習中會發生此錯誤。  

**解析度：** 某些操作對要素列和分類列執行統計操作，如果沒有足夠的值，則操作可能會失敗或返回不正確結果。 檢查資料集以查看要素和標注列中有多少值，並確定嘗試執行的操作在統計上是否有效。  

 如果源資料集有效，還可以檢查某些上游資料操作或中繼資料操作是否更改了資料並刪除了某些值。  

 如果上游操作包括拆分、採樣或重採樣，請驗證輸出是否包含預期的行數和值。  

|例外狀況訊息|
|------------------------|
|分類列和字串列中所選數位列和唯一值的數量太小。|
|分類列和字串列（當前 {actual_num}）中所選數位列和唯一值的總數應至少為 [lower_boundary]。|


## <a name="error-0154"></a>錯誤 0154  
 當使用者嘗試聯接與不相容列類型的關鍵列上的資料時，將發生異常。

|例外狀況訊息|
|------------------------|
|鍵列元素類型不相容。|
|鍵列元素類型不相容。（左： [keys_left]; 右側： [keys_right]）|


## <a name="error-0155"></a>錯誤 0155  
 當資料集的列名稱不是字串時，將發生異常。

|例外狀況訊息|
|------------------------|
|資料框列名稱必須是字串類型。 列名稱不是字串。|
|資料框列名稱必須是字串類型。 列名稱 [column_names] 不是字串。|


## <a name="error-0156"></a>錯誤 0156  
 當無法從 Azure SQL 資料庫讀取資料時，將發生異常。

|例外狀況訊息|
|------------------------|
|無法從 Azure SQL 資料庫讀取資料。|
|無法從 Azure SQL 資料庫讀取資料： {detailed_message} 資料庫： {database_server_name}：{database_name} 查詢：{sql_statement}|


## <a name="error-0157"></a>錯誤 0157  
 找不到資料存儲。

|例外狀況訊息|
|------------------------|
|資料存儲資訊無效。|
|資料存儲資訊無效。 無法在工作區"{workspace_name}"中獲取 AzureML 資料存儲"{datastore_name}"。|


## <a name="error-0158"></a>錯誤 0158
 轉換目錄無效時引發。

|例外狀況訊息|
|------------------------------------------------------------|
|給定轉換目錄無效。|
|轉換目錄"{arg_name}"無效。 原因： [原因]。 請重新運行生成轉換檔的訓練實驗。 如果已刪除訓練實驗，請重新創建並保存轉換檔。|


## <a name="error-1000"></a>錯誤 1000  
內部程式庫例外狀況。  

提供此錯誤是為了捕獲未處理的內部引擎錯誤。 因此，此錯誤的原因可能不同，具體取決於建置錯誤的模組。  

為了獲得更多説明，我們建議您將伴隨該錯誤的詳細消息發佈到 Azure 機器學習論壇，以及方案說明，包括用作輸入的資料。 此回饋將説明我們確定錯誤優先順序，並確定最重要的問題，以便進一步開展工作。  

|例外狀況訊息|
|------------------------|
|程式庫例外狀況。|
|庫異常：[例外]。|
|未知庫異常：[例外]。 [customer_support_guidance] 。|

