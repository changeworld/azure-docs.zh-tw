---
title: 對模組錯誤進行疑難排解
titleSuffix: Azure Machine Learning
description: 使用錯誤碼對 Azure Machine Learning 設計工具中的模組例外狀況進行疑難排解
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: bfb70aaa092cc62fbff87e9e3e327ee7364f8701
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833808"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>設計工具 (預覽) 的例外狀況和錯誤碼

本文說明 Azure Machine Learning 設計工具 (預覽) 中的錯誤訊息和例外狀況代碼，以協助您對機器學習管線進行疑難排解。

您可以依照下列步驟，在設計工具中尋找錯誤訊息：  

- 選取失敗的模組，移至 [輸出 + 記錄] 索引標籤，即可在 **azureml-logs** 類別下的 **70_driver_log.txt** 檔案中找到詳細記錄。

- 如需詳細的模組錯誤，您可以在 **module_statistics** 類別下的 error_info.json 中加以查看。

以下是設計工具中各個模組的錯誤碼。

## <a name="error-0001"></a>錯誤 0001  
 如果找不到資料集的一或多個指定的資料行，就會發生例外狀況。  

 如果為模組進行資料行選擇，但選取的資料行不存在於輸入資料集中，您就會看到此錯誤。 當您執行管線時，如果您手動輸入了資料行名稱，或是資料行選取器所提供的建議資料行不存在於您的資料集中，就可能發生此錯誤。  

**解決方案：** 重新瀏覽擲回此例外狀況的模組，並驗證資料行名稱正確無誤，且所有參考的資料行都存在。  

|例外狀況訊息|
|------------------------|
|找不到一或多個指定的資料行。|
|找不到名稱或索引為 "{column_id}" 的資料行。|
|名稱或索引為 "{column_id}" 的資料行不存在於 "{arg_name_missing_column}" 中。|
|名稱或索引為 "{column_id}" 的資料行不存在於 "{arg_name_missing_column}" 中，但存在於 "{arg_name_has_column}" 中。|
|找不到名稱或索引為 "{column_names}" 的資料行。|
|名稱或索引為 "{column_names}" 的資料行不存在於 "{arg_name_missing_column}" 中。|
|名稱或索引為 "{column_names}" 的資料行不存在於 "{arg_name_missing_column}" 中，但存在於 "{arg_name_has_column}" 中。|


## <a name="error-0002"></a>錯誤 0002  
 如果有一或多個參數無法剖析或從指定的類型轉換成目標方法所需的類型，就會發生例外狀況。  

 如果您將參數指定為輸入，但值類型與預期的類型不同，且無法執行隱含轉換，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 檢查模組需求，並確認所需的值類型 (字串、整數、雙精度浮點數等)  

|例外狀況訊息|
|------------------------|
|無法剖析參數。|
|無法剖析 "{arg_name_or_column}" 參數。|
|無法將 "{arg_name_or_column}" 參數轉換為 "{to_type}"。|
|無法將 "{arg_name_or_column}" 參數從 "{from_type}" 轉換為 "{to_type}"。|
|無法將 "{arg_name_or_column}" 參數值 "{arg_value}" 從 "{from_type}" 轉換為 "{to_type}"。|
|無法將資料行 "{arg_name_or_column}" 中的值 "{arg_value}" 從 "{from_type}" 轉換為 "{to_type}"，並提供 "{fmt}" 格式的使用方式。|


## <a name="error-0003"></a>錯誤 0003  
 如果一或多個輸入為 Null 或空白，就會發生例外狀況。  

 如果模組的任何輸入或參數為 Null 或空白，您就會在 Azure Machine Learning 中看到此錯誤。  例如，當您未輸入任何參數值時，就可能會發生此錯誤。 如果您選擇有遺漏值的資料集或空的資料集，也可能會發生此錯誤。  

**解決方案：**

+ 開啟產生例外狀況的模組，並確認所有輸入皆已指定。 請確定所有必要的輸入皆已指定。 
+ 確定從 Azure 儲存體載入的資料可供存取，且帳戶名稱或金鑰並未變更。  
+ 檢查輸入資料中是否有遺漏值或 Null。
+ 如果對資料來源使用查詢，請確認資料是以您預期的格式傳回。 
+ 檢查資料規格中是否有錯字或其他變更。
  
|例外狀況訊息|
|------------------------|
|一或多個輸入為 Null 或空白。|
|輸入 "{name}" 為 Null 或空白。|


## <a name="error-0004"></a>錯誤 0004  
 如果參數小於或等於特定值，就會發生例外狀況。  

 如果訊息中的參數低於模組處理資料所需的界限值，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 重新瀏覽擲回例外狀況的模組，並將參數修改為大於指定的值。  

|例外狀況訊息|
|------------------------|
|參數應大於界限值。|
|參數 "{arg_name}" 值應大於 {lower_boundary}。|
|參數 "{arg_name}" 的值 "{actual_value}" 應大於 {lower_boundary}。|


## <a name="error-0005"></a>錯誤 0005  
 如果參數小於特定值，就會發生例外狀況。  

 如果訊息中的參數低於或等於模組處理資料所需的界限值，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 重新瀏覽擲回例外狀況的模組，並將參數修改為大於或等於指定的值。  

|例外狀況訊息|
|------------------------|
|參數應該大於或等於界限值。|
|參數 "{arg_name}" 值應大於或等於 {lower_boundary}。|
|參數 "{arg_name}" 的值 "{value}" 應大於或等於 {lower_boundary}。|


## <a name="error-0006"></a>錯誤 0006  
 如果參數大於或等於指定值，就會發生例外狀況。  

 如果訊息中的參數大於或等於模組處理資料所需的界限值，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 重新瀏覽擲回例外狀況的模組，並將參數修改為小於指定的值。  

|例外狀況訊息|
|------------------------|
|參數不相符。 其中一個參數應該小於另一個。|
|參數 "{arg_name}" 值應小於參數 "{upper_boundary_parameter_name}" 值。|
|參數 "{arg_name}" 的值 "{value}" 應小於 {upper_boundary_parameter_name}。|


## <a name="error-0007"></a>錯誤 0007  
 如果參數大於特定值，就會發生例外狀況。  

 如果您在模組的屬性中指定了大於允許值的值，就會在 Azure Machine Learning 中看到此錯誤。 例如，您可能會指定超出支援日期範圍的資料，或者，您可能會在只有三個可用的資料行時指定要使用五個資料行。 

 如果您指定需要以某種方式相符的兩組資料，也可能會看到此錯誤。 例如，如果您要重新命名資料行，並依索引指定資料行，則您提供的名稱數目必須符合資料行索引的數目。 此外，使用兩個資料行，且兩者必須具有相同資料列數目的數學運算，也可能屬於此例。 

**解決方案：**

 + 開啟有問題的模組，並檢閱任何數值屬性設定。
 + 確定任何參數值都落在該屬性的支援值範圍內。
 + 如果模組使用多個輸入，請確定輸入的大小相同。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 檢查資料集或資料來源是否有所變更。 有時，使用舊版資料的值在資料行數目、資料行的資料類型或資料大小變更之後，即會失敗。  

|例外狀況訊息|
|------------------------|
|參數不相符。 其中一個參數應小於或等於另一個。|
|參數 "{arg_name}" 值應小於或等於參數 "{upper_boundary_parameter_name}" 值。|
|參數 "{arg_name}" 的值 "{actual_value}" 應小於或等於 {upper_boundary}。|
|參數 "{arg_name}" 值 {actual_value} 應小於或等於參數 "{upper_boundary_parameter_name}" 值 {upper_boundary}。|
|參數 "{arg_name}" 值 {actual_value} 應小於或等於 {upper_boundary_meaning} 值 {upper_boundary}。|


## <a name="error-0008"></a>錯誤 0008  
 如果參數不在範圍內，就會發生例外狀況。  

 如果訊息中的參數不在模組處理資料所需的界限內，您就會在 Azure Machine Learning 中看到此錯誤。  

 例如，如果您嘗試使用[ [新增資料列]](add-rows.md) 來結合兩個具有不同資料行數目的資料集，就會顯示此錯誤。  

**解決方案：** 重新瀏覽擲回例外狀況的模組，並將參數修改為位於指定範圍內。  

|例外狀況訊息|
|------------------------|
|參數值不在指定的範圍內。|
|參數 "{arg_name}" 值不在範圍內。|
|參數 "{arg_name}" 值應在 [{lower_boundary}, {upper_boundary}] 的範圍內。|
|參數 "{arg_name}" 值不在範圍內。 {reason}|


## <a name="error-0009"></a>錯誤 0009  
 當指定的 Azure 儲存體帳戶名稱或容器名稱不正確時，就會發生例外狀況。  

當您為 Azure 儲存體帳戶指定參數，但無法解析名稱或密碼時，就會在 Azure Machine Learning 設計工具中發生此錯誤。 密碼或帳戶名稱發生錯誤可能有許多原因：

 + 帳戶的類型錯誤。 某些新的帳戶類型不支援與 Machine Learning 設計工具搭配使用。 如需詳細資訊，請參閱[匯入資料](import-data.md)。
 + 您輸入的帳戶名稱不正確
 + 帳戶已不存在
 + 儲存體帳戶的密碼錯誤或已變更
 + 您未指定容器名稱，或容器不存在
 + 您未完整指定檔案路徑 (Blob 的路徑)
   

**解決方案：**

這類問題通常發生在您嘗試手動輸入帳戶名稱、密碼或容器路徑時。 我們建議您對[匯入資料](import-data.md)模組使用新的精靈，這有助於您查閱和檢查名稱。

同時請檢查帳戶、容器或 Blob 是否已刪除。 請使用另一個 Azure 儲存體公用程式，確認帳戶名稱和密碼皆已正確輸入，且容器確實存在。 

Azure Machine Learning 不支援某些較新的帳戶類型。 例如，新的「經常性存取」或「冷」儲存體類型無法用於機器學習。 傳統儲存體帳戶和建立為「一般用途」的儲存體帳戶都可正常運作。

如果指定了 Blob 的完整路徑，請確認路徑指定為 **container/blobname**，且容器和 Blob 都存在於帳戶中。  

 路徑不應包含前置斜線。 例如， **/container/blob** 並不正確，而應輸入為 **container/blob**。  


|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶名稱或容器名稱不正確。|
|Azure 儲存體帳戶名稱 "{account_name}" 或容器名稱 "{container_name}" 不正確；容器名稱的格式應為 container/blob。|


## <a name="error-0010"></a>錯誤 0010  
 如果輸入資料集有應符合但卻不符的資料行名稱，就會發生例外狀況。  

 如果訊息中的資料行索引在兩個輸入資料集中有不同的資料行名稱，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 使用[編輯中繼資料](edit-metadata.md)或修改原始資料集，讓指定的資料行索引具有相同的資料行名稱。  

|例外狀況訊息|
|------------------------|
|在輸入資料集中有對應索引的資料行具有不同的名稱。|
|輸入資料集 (分別為 {dataset1} 和 {dataset2}) 的資料行 {col_index} (以零起始) 的資料行名稱不相同。|


## <a name="error-0011"></a>錯誤 0011  
 如果傳遞的資料行集引數不適用於任何資料集資料行，就會發生例外狀況。  

 如果指定的資料行選取不符合給定資料集中的任何資料行，您就會在 Azure Machine Learning 中看到此錯誤。  

 如果您未選取資料行，而模組至少必須有一個資料行才能運作，也可能發生此錯誤。  

**解決方案：** 修改模組中的資料行選取，使其適用於資料集中的資料行。  

 如果模組要求您選取特定資料行 (例如標籤資料行)，請確認您已選取正確的資料行。  

 如果選取了不適當的資料行，請加以移除，然後重新執行管線。  

|例外狀況訊息|
|------------------------|
|指定的資料行集不適用於任何資料集資料行。|
|指定的資料行集 "{column_set}" 不適用於任何資料集資料行。|


## <a name="error-0012"></a>錯誤 0012  
 如果無法以傳入的引數集合建立類別的執行個體，就會發生例外狀況。  

**解決方案：** 使用者無法對此錯誤採取任何動作，且未來的版本將會淘汰此錯誤。  

|例外狀況訊息|
|------------------------|
|未定型的模型，請先定型模型。|
|未定型的模型 ({arg_name})，請使用已定型的模型。|


## <a name="error-0013"></a>錯誤 0013  
 如果傳至模組的學習模組是無效的類型，就會發生例外狀況。  

 當定型的模型與連線的評分模組不相容時，就會發生此錯誤。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**解決方案：**

確認定型模組所產生的學習模組類型，並決定適用於學習模組的評分模組。 

如果模型是使用任何特殊化定型模組進行定型，則定型的模型僅應連線至對應的特殊化評分模組。 


|模型類型|定型模組| 評分模組|
|----|----|----|
|任何分類器|[訓練模型](train-model.md) |[評分模型](score-model.md)|
|任何迴歸模型|[訓練模型](train-model.md) |[評分模型](score-model.md)|

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
|傳遞的學習模組類型無效。|
|學習模組 "{arg_name}" 的類型無效。|
|學習模組 "{arg_name}" 的類型 "{learner_type}" 無效。|
|傳遞的學習模組類型無效。 例外狀況訊息：{exception_message}|


## <a name="error-0014"></a>錯誤 0014  
 如果資料行唯一值的計數大於允許計數，就會發生例外狀況。  

 當資料行包含太多唯一值時，就會發生此錯誤。  例如，如果您指定要將資料行當作類別資料來處理，但資料行中有太多唯一值而無法完成處理，就可能會出現此錯誤。 如果兩個輸入中的唯一值數目不相符，也可能會出現此錯誤。   

**解決方案：**

開啟產生錯誤的模組，並識別作為輸入的資料行。 對於某些模組，您可以用滑鼠右鍵按一下資料集輸入，然後選取 [視覺化] 以取得個別資料行的統計資料，包括唯一值的數目及其分布。

對於您想要用於分組或分類的資料行，請採取相關步驟以減少資料行中的唯一值數目。 有多種方式可以減少此數目，視資料行的資料類型而定。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到與您的案例相符的解決方法嗎？ 您可以提供此主題的意見反應，包括產生錯誤的模組名稱，以及資料行的資料類型和基數。 我們將使用此資訊，為常見案例提供更具針對性的疑難排解步驟。   

|例外狀況訊息|
|------------------------|
|資料行的唯一值數量大於允許的數量。|
|資料行 "{column_name}" 中的唯一值數目大於允許的數目。|
|資料行 "{column_name}" 中的唯一值數目超過 {limitation} 的元組計數。|


## <a name="error-0015"></a>錯誤 0015  
 如果資料庫連線失敗，就會發生例外狀況。  

 如果您輸入不正確的 SQL 帳戶名稱、密碼、資料庫伺服器或資料庫名稱，或因為資料庫或伺服器發生問題而無法建立資料庫的連線，就會看到此錯誤。  

**解決方案：** 確認帳戶名稱、密碼、資料庫伺服器和資料庫皆已正確輸入，且指定的帳戶具有正確的權限層級。 確認資料庫目前可供存取。  

|例外狀況訊息|
|------------------------|
|建立資料庫連線時發生錯誤。|
|建立資料庫連線時發生錯誤：{connection_str}。|


## <a name="error-0016"></a>錯誤 0016  
 如果傳至模組的輸入資料集應有相容的資料行類型，但卻沒有，就會發生例外狀況。  

 如果傳入兩個或更多個資料集的資料行類型彼此不相容，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 使用[編輯中繼資料](edit-metadata.md)或修改原始輸入資料集，<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 以確保資料行的類型相容。  

|例外狀況訊息|
|------------------------|
|在輸入資料集中有對應索引的資料行具有不相容的類型。|
|定型和測試資料之間的資料行 '{first_col_names}' 不相容。|
|資料行 '{first_col_names}' 與 '{second_col_names}' 不相容。|
|輸入資料集 (分別為 {first_dataset_names} 和 {second_dataset_names}) 的資料行 '{first_col_names}' (以零起始) 的資料行元素類型不相容。|


## <a name="error-0017"></a>錯誤 0017  
 如果選取的資料行使用目前的模組不支援的資料類型，就會發生例外狀況。  

 例如，如果您的資料行選取包含的資料行具有無法由模組處理的資料類型 (例如，數學運算的字串資料行，或需要類別特徵資料行的分數資料行)，就可能會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：**
 1. 識別問題所在的資料行。
 2. 檢閱模組的需求。
 3. 修改資料行，使其符合需求。 您可能需要使用下列幾個模組來進行變更，視資料行和您嘗試進行的轉換而定：
    + 使用[編輯中繼資料](edit-metadata.md)來變更資料行的資料類型，或將資料行的使用方式從特徵變更為數值、從類別變更為非類別等等。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 若上述方式都不可行，您可能需要修改原始的輸入資料集。

> [!TIP]
> 找不到與您的案例相符的解決方法嗎？ 您可以提供此主題的意見反應，包括產生錯誤的模組名稱，以及資料行的資料類型和基數。 我們將使用此資訊，為常見案例提供更具針對性的疑難排解步驟。 

|例外狀況訊息|
|------------------------|
|無法處理目前類型的資料行。 模組不支援此類型。|
|無法處理 {col_type} 類型的資料行。 模組不支援此類型。|
|無法處理 {col_type} 類型的資料行 "{col_name}"。 模組不支援此類型。|
|無法處理 {col_type} 類型的資料行 "{col_name}"。 模組不支援此類型。 參數名稱：{arg_name}。|


## <a name="error-0018"></a>錯誤 0018  
 如果輸入資料集無效，就會發生例外狀況。  

**解決方案：** Azure Machine Learning 中的這項錯誤可能出現在許多內容中，因此並沒有單一解決方法。 一般而言，此錯誤表示提供作為模組輸入的資料具有錯誤的資料行數目，或資料類型不符合模組的需求。 例如：  

-   模組需要標籤資料行，但沒有任何資料行標示為標籤，或您尚未選取標籤資料行。  
  
-   模組需要的是類別資料，但您的資料是數值。  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   資料的格式錯誤。  
  
-   匯入的資料包含無效的字元、錯誤的值或超出範圍的值。  
-   資料行是空的，或包含太多遺漏值。  

 若要確認需求和資料可能的屬性，請檢閱將使用資料集作為輸入的模組適用的說明主題。  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->。  

|例外狀況訊息|
|------------------------|
|資料集無效。|
|{dataset1} 包含無效的資料。|
|{dataset1} 和 {dataset2} 的資料行應一致。|
|{dataset1} 包含無效的資料，{reason}。|
|{dataset1} 包含 {invalid_data_category}。 {troubleshoot_hint}|
|{dataset1} 無效，{reason}。 {troubleshoot_hint}|


## <a name="error-0019"></a>錯誤 0019  
 如果資料行應包含已排序的值，但卻沒有，就會發生例外狀況。  

 如果指定的資料行值順序錯誤，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 手動修改輸入資料集並重新執行模組，以排序資料行值。  

|例外狀況訊息|
|------------------------|
|資料行中的值未排序。|
|資料行 "{col_index}" 中的值未排序。|
|資料集 "{dataset}" 的資料行 "{col_index}" 中的值未排序。|
|引數 "{arg_name}" 中的值未依照 "{sorting_order}" 順序排序。|


## <a name="error-0020"></a>錯誤 0020  
 如果傳至模組的某些資料集中的資料行數目太少，就會發生例外狀況。  

 如果為模組選取的資料行不夠多，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 重新瀏覽模組，並確定資料行選取器已選取正確數量的資料行。  

|例外狀況訊息|
|------------------------|
|輸入資料集中的資料行數目小於允許的最小值。|
|輸入資料集 "{arg_name}" 中的資料行數目小於允許的最小值。|
|輸入資料集中的資料行數目小於允許的最小資料行數目 {required_columns_count}。|
|輸入資料集 "{arg_name}" 中的資料行數目小於允許的最小資料行數目 {required_columns_count}。|


## <a name="error-0021"></a>錯誤 0021  
 如果傳至模組的某些資料集中的資料列數目太少，就會發生例外狀況。  

 當資料集中沒有足夠的資料列可執行指定的作業時，就會在 Azure Machine Learning 中出現此錯誤。 例如，如果輸入資料集是空的，或您嘗試執行的作業至少需要特定數量的有效資料列，就可能會出現此錯誤。 這類作業包括 (但不限於) 以統計方法為基礎的分組或分類、特定類型的量化，以及透過計數的學習。  

**解決方案：**

 + 開啟傳回錯誤的模組，並檢查輸入資料集和模組屬性。 
 + 確認輸入資料集不是空的，且有足夠的資料列可符合模組說明中說明的需求。  
 + 如果您的資料是從外部來源載入的，請確定該資料來源可供使用，且資料定義中沒有任何錯誤或變更會導致匯入程序取得較少的資料列。
 + 如果您要對模組的上游資料執行可能對資料類型或值的數目產生影響的作業 (例如清除、分割或聯結作業)，請檢查這些作業的輸出，以確認傳回的資料列數目。  

|例外狀況訊息|
|------------------------|
|輸入資料集中的資料列數目小於允許的最小值。|
|輸入資料集中的資料列數目小於允許的最小資料列數目 {required_rows_count}。|
|輸入資料集中的資料列數目小於允許的最小資料列數目 {required_rows_count}。 {reason}|
|輸入資料集 "{arg_name}" 中的資料列數目小於允許的最小資料列數目 {required_rows_count}。|
|輸入資料集 "{arg_name}" 中的資料列數目為 {actual_rows_count}，小於允許的最小資料列數目 {required_rows_count}。|
|輸入資料集 "{arg_name}" 中的 "{row_type}" 資料列數目小於允許的最小資料列數目 {required_rows_count}。|


## <a name="error-0022"></a>錯誤 0022  
 如果輸入資料集中選取的資料行數目不等於預期的數目，就會發生例外狀況。  

 當下游模組或作業需要特定數目的資料行或輸入，但您提供了太少或太多的資料行或輸入時，就會在 Azure Machine Learning 中發生此錯誤。 例如：  

-   您指定單一標籤資料行或索引鍵資料行，但不慎選取了多個資料行。  
  
-   您要重新命名資料行，但提供的名稱多於或少於資料行的數目。  
  
-   來源或目的地中的資料行數目已變更，或不符合模組所使用的資料行數目。  
  
-   您為輸入提供了以逗點分隔的值清單，但值的數目不相符，或有多個輸入不受支援。  

**解決方案：** 重新瀏覽模組，並檢查資料行選取，以確定已選取正確的資料行數目。 確認上游模組的輸出，以及下游作業的需求。  

 如果您使用一個可選取多個資料行 (資料行索引、所有特徵、所有數值等等) 的資料行選取選項，請驗證該選項所傳回的確切資料行數目。  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 確認上游資料行的數目或類型並未變更。  

 如果您要使用建議資料集來定型模型，請您切記，推薦系統預期資料行數目應有限制，且對應於使用者項目配對或使用者項目排名。 在定型模型或分割建議資料集之前，請先移除多餘的資料行。 如需詳細資訊，請參閱[分割資料](split-data.md)。  

|例外狀況訊息|
|------------------------|
|輸入資料集中選取的資料行數目不等於預期的數目。|
|輸入資料集中選取的資料行數目不等於 {expected_col_count}。|
|資料行選取模式 "{selection_pattern_friendly_name}" 提供的在輸入資料集中選取的資料行數目不等於 {expected_col_count}。|
|資料行選取模式 "{selection_pattern_friendly_name}" 應提供在輸入資料集中選取的 {expected_col_count} 個資料行，但實際上提供了 {selected_col_count} 個資料行。|


## <a name="error-0023"></a>錯誤 0023  

如果輸入資料集的目標資料行對目前的定型模組而言無效，就會發生例外狀況。  

如果目標資料行 (在模組參數中選取的資料行) 不是有效的資料類型、包含所有遺漏值，或不是預期的類別，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 重新瀏覽模組輸入，以檢查標籤/目標資料行的內容。 請確定資料行並沒有所有遺漏值。 如果模組預期目標資料行是類別資料行，請確定目標資料行中多個相異值。  

|例外狀況訊息|
|------------------------|
|輸入資料集具有不支援的目標資料行。|
|輸入資料集具有不支援的目標資料行 "{column_index}"。|
|輸入資料集具有 {learner_type} 類型的學習模組不支援的目標資料行 "{column_index}"。|


## <a name="error-0024"></a>錯誤 0024  
如果資料集未包含標籤資料行，就會發生例外狀況。  

 當模組需要標籤資料行，但資料集沒有標籤資料行時，就會在 Azure Machine Learning 中發生此錯誤。 例如，在評估已評分的資料集時，通常需要有標籤資料行才能計算正確性計量。  

如果資料集有標籤資料行，但 Azure Machine Learning 無法正確偵測到，也可能會發生此錯誤。

**解決方案：**

+ 開啟產生錯誤的模組，並確認標籤資料行是否存在。 資料行的名稱或資料類型並不重要，只要資料行包含您嘗試預測的單一結果 (或相依變數) 即可。 如果您不確定哪一個資料行具有標籤，請尋找*類別*或*目標*之類的一般名稱。 
+  如果資料集未包含標籤資料行，表示可能已在上游明確或意外刪除了標籤資料行。 此外也可能是因為資料集不是上游評分模組的輸出。
+ 若要明確地將資料行標示為標籤資料行，請新增[編輯中繼資料](edit-metadata.md)模組，並連接資料集。 請選取標籤資料行，然後從 [欄位] 下拉式清單中選取 [標籤]。 
+ 如果選擇了錯誤的資料行作為標籤，您可以從 [欄位] 中選取 [清除標籤]，以修正資料行上的中繼資料。 
  
|例外狀況訊息|
|------------------------|
|資料集中沒有標籤資料行。|
|"{dataset_name}" 中沒有標籤資料行。|


## <a name="error-0025"></a>錯誤 0025  
 如果資料集未包含分數資料行，就會發生例外狀況。  

 如果評估模型的輸入未包含有效的分數資料行，就會在 Azure Machine Learning 中發生此錯誤。 例如，使用者在使用正確的定型模型進行評分之前即嘗試評估資料集，或分數資料行已在上游明確遭到捨棄。 如果兩個資料集上的分數資料行不相容，也會發生此例外狀況。 例如，如果您嘗試比較線性迴歸輸入變數與二元分類器的正確性。  

**解決方案：** 重新瀏覽評估模型的輸入，並檢查其中是否包含一或多個分數資料行。 如果不是，表示資料集並未評分，或分數資料行已在上游模組中遭到捨棄。  

|例外狀況訊息|
|------------------------|
|資料集中沒有分數資料行。|
|"{dataset_name}" 中沒有分數資料行。|
|"{dataset_name}" 中沒有由 "{learner_type}" 產生的分數資料行。 請使用正確類型的學習模組為資料集評分。|


## <a name="error-0026"></a>錯誤 0026  
 如果不允許具有相同名稱的資料行，就會發生例外狀況。  

 如果多個資料行具有相同的名稱，就會在 Azure Machine Learning 中發生此錯誤。 您會看到此錯誤的狀況之一，是系統在資料集沒有標頭資料列的情況下自動指派了資料行名稱：Col0、Col1 等等。  

**解決方案：** 如果多個資料行具有相同的名稱，請在輸入資料集與模組之間插入[編輯中繼資料](edit-metadata.md)模組。 請使用[編輯中繼資料](edit-metadata.md)中的資料行選取器來選取要重新命名的資料行，然後在 [新的資料行名稱] 文字方塊中輸入新名稱。  

|例外狀況訊息|
|------------------------|
|在引數中指定了相同的資料行名稱。 模組不允許相同的資料行名稱。|
|引數 "{arg_name_1}" 和 "{arg_name_2}" 中不允許相同的資料行名稱。 請指定其他名稱。|


## <a name="error-0027"></a>錯誤 0027  
 如果兩個物件的大小必須相同，但卻不是，就會發生例外狀況。  

 這是 Azure Machine Learning 中常見的錯誤，可能由許多狀況所造成。  

**解決方案：** 沒有特定的解決方法。 不過，您可以確認以下情況：  

-   如果您要重新命名資料行，請確定每份清單 (輸入資料行和新名稱清單) 都有相同數目的項目。  
  
-   如果您要聯結或串連兩個資料集，請確定兩者具有相同的結構描述。  
  
-   如果您要聯結兩個具有多個資料行的資料集，請確定索引鍵資料行具有相同的資料類型，然後選取 [允許選取範圍中有重複項並保留資料行順序] 選項。  

|例外狀況訊息|
|------------------------|
|傳遞的物件大小不一致。|
|"{friendly_name1}" 的大小與 "{friendly_name2}" 的大小不一致。|


## <a name="error-0028"></a>錯誤 0028  
 當資料行集包含重複的資料行名稱，但不允許如此時，就會發生例外狀況。  

 當資料行名稱重複 (即不是唯一的) 時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 若有任何資料行具有相同的名稱，請在輸入資料集與引發錯誤的模組之間新增[編輯中繼資料](edit-metadata.md)的執行個體。 請使用[編輯中繼資料](edit-metadata.md)中的資料行選取器來選取要重新命名的資料行，然後在 [新的資料行名稱] 文字方塊中輸入新的資料行名稱。 如果您要重新命名多個資料行，請確定您在 [新的資料行名稱] 中輸入的值是唯一的。  

|例外狀況訊息|
|------------------------|
|資料行集包含重複的資料行名稱。|
|名稱 "{duplicated_name}" 重複。|
|"{arg_name}" 中的名稱 "{duplicated_name}" 重複。|
|名稱 "{duplicated_name}" 重複。 詳細資料：{details}|


## <a name="error-0029"></a>錯誤 0029  
 傳入無效的 URI 時，就會發生例外狀況。  

 傳入無效的 URI 時，就會在 Azure Machine Learning 中發生此錯誤。  下列任一條件成立時，您就會看到此錯誤：  

-   為 Azure Blob 儲存體提供以用於讀取或寫入的公用或 SAS URI 包含錯誤。  
  
-   SAS 的時間範圍已過期。  
  
-   透過 HTTP 來源的 Web URL 代表檔案或回送 URI。  
  
-   透過 HTTP 的 Web URL 包含格式不正確的 URL。  
  
-   遠端來源無法解析 URL。  

**解決方案：** 重新瀏覽模組，並確認 URI 的格式。 如果資料來源是透過 HTTP 的 Web URL，請確認預定的來源並不是檔案或回送 URI (localhost)。  

|例外狀況訊息|
|------------------------|
|傳遞的 URI 無效。|
|URI "{invalid_url}" 無效。|


## <a name="error-0030"></a>錯誤 0030  
 無法下載檔案時，就會發生例外狀況。  

 無法下載檔案時，就會在 Azure Machine Learning 中發生此例外狀況。 在嘗試讀取 HTTP 來源時若歷經了三 (3) 次重試而失敗，就會發生此例外狀況。  

**解決方案：** 確認 HTTP 來源的 URI 正確無誤，且目前可透過網際網路存取該網站。  

|例外狀況訊息|
|------------------------|
|無法下載檔案。|
|下載檔案時發生錯誤：{file_url}。|


## <a name="error-0031"></a>錯誤 0031  
 如果資料行集中的資料行數目少於所需，就會發生例外狀況。  

 如果選取的資料行數目少於所需，就會在 Azure Machine Learning 中發生此錯誤。  如果未選取所需的最小資料行數目，您就會看到此錯誤。  

**解決方案：** 使用**資料行選取器**，在資料行選取中新增更多資料行。  

|例外狀況訊息|
|------------------------|
|資料行集中的資料行數目小於所需。|
|對於輸入引數 "{arg_name}" 至少應指定 {required_columns_count} 個資料行。|
|對於輸入引數 "{arg_name}" 至少應指定 {required_columns_count} 個資料行。 指定資料行的實際數目是 {input_columns_count}。|


## <a name="error-0032"></a>錯誤 0032  
 如果引數不是數字，就會發生例外狀況。  

 如果引數為雙精度浮點數或 NaN，您就會在 Azure Machine Learning 中看到此錯誤。  

**解決方案：** 將指定的引數修改為使用有效的值。  

|例外狀況訊息|
|------------------------|
|引數不是數字。|
|"{arg_name}" 不是數字。|


## <a name="error-0033"></a>錯誤 0033  
 如果引數無限制，就會發生例外狀況。  

 如果引數無限制，就會在 Azure Machine Learning 中發生此錯誤。 如果引數是 `double.NegativeInfinity` 或 `double.PositiveInfinity`，您就會看到此錯誤。  

**解決方案：** 將指定的引數修改為有效的值。  

|例外狀況訊息|
|------------------------|
|引數必須是有限的。|
|"{arg_name}" 不是有限的。|
|資料行 "{column_name}" 包含無限值。|


## <a name="error-0034"></a>錯誤 0034  
 如果給定的使用者/項目配對有多個評等，就會發生例外狀況。  

 如果使用者/項目配對有多個評等值，就會在 Azure Machine Learning 的建議中發生此錯誤。  

**解決方案：** 確定使用者/項目配對只有一個評等值。  

|例外狀況訊息|
|------------------------|
|資料集中的值有多個評等。|
|評等預測資料表中的使用者 {user} 和項目 {item} 有多個評等。|
|{dataset} 中的使用者 {user} 和項目 {item} 有多個評等。|


## <a name="error-0035"></a>錯誤 0035  
 若未提供指定使用者或項目的特徵，就會發生例外狀況。  

 當您嘗試使用建議模型進行評分，但找不到特徵向量時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：**

Matchbox 推薦具有某些必須在使用項目特徵或使用者特徵時必須符合的需求。  發生此錯誤時，表示您提供作為輸入的使用者或項目缺少特徵向量。 請確定每個使用者或項目的資料中都有可用的特徵向量。  

 例如，如果您使用像是使用者年齡、地點或收入等特徵來定型建議模型，但現在想要為在進行定型期間未看到的新使用者建立分數，則必須針對新使用者提供一組對等的特徵 (也就是年齡、地點和收入值)，為他們做出適當的預測。 

 如果您沒有這些使用者的任何特徵，請考慮使用特徵工程來產生適當的特徵。  例如，如果您沒有個別使用者的年齡或收入值，您可以產生近似值供使用者群組使用。 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 解決方法不適用於您的案例嗎？ 歡迎您傳送本文的意見反應，並提供案例的相關資訊，包括模組和資料行中的資料列數目。 我們未來將使用此資訊提供更詳細的疑難排解步驟。

|例外狀況訊息|
|------------------------|
|未針對必要的使用者或項目提供特徵。|
|需要 {required_feature_name} 的特徵，但未提供。|


## <a name="error-0036"></a>錯誤 0036  
 如果未給定的使用者或項目提供多個特徵向量，就會發生例外狀況。  

 如果重複定義了特徵向量，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 確定未重複定義特徵向量。  

|例外狀況訊息|
|------------------------|
|使用者或項目的特徵定義重複。|


## <a name="error-0037"></a>錯誤 0037  
 如果指定了多個標籤資料行，但僅允許一個，就會發生例外狀況。  

 如果選取了多個資料行作為新的標籤資料行，就會在 Azure Machine Learning 中發生此錯誤。 受監督的學習演算法大多都需要將單一資料行標示為目標或標籤。  

**解決方案：** 確實選取單一資料行作為新的標籤資料行。  

|例外狀況訊息|
|------------------------|
|指定了多個標籤資料行。|
|在 "{dataset_name}" 中指定了多個標籤資料行。|


## <a name="error-0039"></a>錯誤 0039  
 如果作業失敗，就會發生例外狀況。  

 當內部作業無法完成時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 此錯誤由許多狀況所造成，並沒有特定的補救方式。  
 下表包含此錯誤的一般訊息，其後則是條件的具體說明。 

 如果沒有可用的詳細資訊，請參閱 [Microsoft 問答集頁面以傳送意見反應](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html)，並提供有關於產生錯誤的模組和相關條件的資訊。

|例外狀況訊息|
|------------------------|
|作業失敗。|
|完成作業時發生錯誤："{failed_operation}"。|
|完成作業時發生錯誤："{failed_operation}"。 原因："{reason}"。|


## <a name="error-0042"></a>錯誤 0042  
 無法將資料行轉換為另一種類型時，就會發生例外狀況。  

 如果無法將資料行轉換為指定的類型，就會在 Azure Machine Learning 中發生此錯誤。  如果模組需要特定的資料類型，例如日期時間、文字、浮點數或整數，但無法將現有的資料行轉換為所需的類型，您就會看到此錯誤。  

例如，當您選取資料行，然後嘗試將其轉換為用於數學運算的數值資料類型時，如果資料行包含無效的資料，您就會看到此錯誤。 

另一個可能出現此錯誤的原因是，您嘗試將包含浮點數或多個唯一值的資料行作為類別資料行。 

**解決方案：**

+ 開啟產生錯誤的模組適用的說明頁面，並確認資料類型需求。
+ 在輸入資料集中檢閱資料行的資料類型。
+ 檢查源自於所謂的無結構描述資料來源的資料。
+ 檢查資料集是否有遺漏值或特殊字元導致無法轉換為所需的資料類型。 
    + 數值資料類型應該是一致的：例如，請在整數的資料行中檢查是否有浮點數。
    + 在數位資料行中尋找文字字串或 NA 值。 
    + 布林值可根據所需的資料類型轉換為適當的表示法。
    + 檢查文字資料行中是否有非 unicode 字元、定位字元或控制字元
    + 日期時間資料應保持一致，以避免發生模型化錯誤，但清除作業可能因格式繁多而十分複雜。 請考慮使用 <!--the [Execute R Script](execute-r-script.md) or -->[執行 Python 指令碼](execute-python-script.md)模組來執行清除。  
+ 如有必要，請修改輸入資料集中的值，以便能夠成功轉換資料行。 修改可包括量化、截斷或捨入運算、排除極端值，或插補遺漏值。 請參閱下列文章，以了解機器學習中常見的一些資料轉換案例：
    + [清除遺漏的資料](clean-missing-data.md)
    + [將資料標準化](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 解決方法不清楚，或不適用於您的案例嗎？ 歡迎您傳送本文的意見反應，並提供案例的相關資訊，包括模組和資料行的資料類型。 我們未來將使用此資訊提供更詳細的疑難排解步驟。  

|例外狀況訊息|
|------------------------|
|不允許轉換。|
|無法將 {type1} 類型的資料行轉換為 {type2} 類型的資料行。|
|無法將 {type1} 類型的資料行 "{col_name1}" 轉換為 {type2} 類型的資料行。|
|無法將 {type1} 類型的資料行 "{col_name1}" 轉換為 {type2} 類型的資料行 "{col_name2}"。|


## <a name="error-0044"></a>錯誤 0044  
 無法從現有值衍生資料行的元素類型時，就會發生例外狀況。  

 無法推斷資料集中一或多個資料行的類型時，就會在 Azure Machine Learning 中發生此錯誤。 此錯誤通常發生在串連兩個或更多具有不同元素類型的資料集時。 Azure Machine Learning 若無法判斷能夠代表一或多個資料行中的所有值而不會遺失資訊的一般類型，就會產生此錯誤。  

**解決方案：** 確定要結合的兩個資料集中給定資料行的所有值屬於相同的類型 (數值、布林值、類別、字串、日期等)，或可以強制轉型為相同的類型。  

|例外狀況訊息|
|------------------------|
|無法衍生資料行的元素類型。|
|無法衍生資料行 "{column_name}" 的元素類型 -- 所有元素都是 Null 參考。|
|無法衍生資料集 "{dataset_name}" 的資料行 "{column_name}" 的元素類型 -- 所有元素都是 Null 參考。|


## <a name="error-0045"></a>錯誤 0045  
 因來源中的混合元素類型而無法建立資料行時，就會發生例外狀況。  

 當要結合的兩個資料集的元素類型不同時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 確定要結合的兩個資料集中給定資料行的所有值屬於相同的類型 (數值、布林值、類別、字串、日期等)。  

|例外狀況訊息|
|------------------------|
|無法建立混合元素類型的資料行。|
|無法以識別碼 "{column_id}" 建立混合元素類型的資料行：<br />資料 [{row_1}, {column_id}] 的類型為 "{type_1}"。 <br />資料 [{row_2}, {column_id}] 的類型為 "{type_2}"。|
|無法以識別碼 "{column_id}" 建立混合元素類型的資料行：<br />區塊 {chunk_id_1} 中的類型為 "{type_1}"。 <br />區塊 {chunk_id_2} 中的類型為 "{type_2}"，區塊大小為 {chunk_size}。|


## <a name="error-0046"></a>錯誤 0046  
 無法在指定的路徑上建立目錄時，就會發生例外狀況。  

 無法在指定的路徑上建立目錄時，就會在 Azure Machine Learning 中發生此錯誤。 如果 Hive 查詢輸出目錄的任何部分不正確或無法存取，就會出現此錯誤。  

**解決方案：** 重新瀏覽模組，並確認目錄路徑的格式正確，且可使用目前的認證存取。  

|例外狀況訊息|
|------------------------|
|請指定有效的輸出目錄。|
|無法建立目錄 {path}。 請指定有效的路徑。|


## <a name="error-0047"></a>錯誤 0047  
 如果傳遞給模組的某些資料集的特徵資料行數目太少，就會發生例外狀況。  

 如果定型的輸入資料集未包含演算法所需的最小資料行數目，就會在 Azure Machine Learning 中發生此錯誤。 這通常是因為資料集是空的，或僅包含定型資料行。  

**解決方案：** 重新瀏覽輸入資料集，以確定除了標籤資料行以外還有一或多個額外的資料行。  

|例外狀況訊息|
|------------------------|
|輸入資料集的特徵資料行數目小於允許的最小值。|
|輸入資料集中的特徵資料行數目小於允許的最小資料行數目 {required_columns_count}。|
|輸入資料集 "{arg_name}" 中的特徵資料行數目小於允許的最小資料行數目 {required_columns_count}。|


## <a name="error-0048"></a>錯誤 0048  
 無法開啟檔案時，就會發生例外狀況。  

 無法開啟檔案以進行讀取或寫入時，就會在 Azure Machine Learning 中發生此錯誤。 出現此錯誤的可能原因如下：  

-   容器或檔案 (Blob) 不存在  
  
-   檔案或容器的存取層級不允許您存取檔案  
  
-   檔案太大而無法讀取，或格式錯誤  

**解決方案：** 重新瀏覽模組和您嘗試讀取的檔案。  

 確認容器和檔案的名稱正確無誤。  

 使用 Azure 傳統入口網站或 Azure 儲存體工具，確認您有存取該檔案的權限。  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|例外狀況訊息|
|------------------------|
|無法開啟檔案。|
|開啟檔案時發生錯誤：{file_name}。|
|開啟檔案時發生錯誤：{file_name}。 儲存體例外狀況訊息：{exception}。|


## <a name="error-0049"></a>錯誤 0049  
 無法剖析檔案時，就會發生例外狀況。  

 無法剖析檔案時，就會在 Azure Machine Learning 中發生此錯誤。 如果在[匯入資料](import-data.md)模組中選取的檔案格式與檔案的實際格式不相符，或者檔案包含無法辨識的字元，您就會看到此錯誤。  

**解決方案：** 重新瀏覽模組，並檔案格式選取不符合檔案格式時加以更正。 可能的話，請檢查檔案以確認其中不含任何不合法的字元。  

|例外狀況訊息|
|------------------------|
|無法剖析檔案。|
|剖析檔案 {file_format} 時發生錯誤。|
|剖析 {file_format} 檔案時發生錯誤：{file_name}。|
|剖析檔案 {file_format} 時發生錯誤。 原因：{failure_reason}。|
|剖析 {file_format} 檔案時發生錯誤：{file_name}。 原因：{failure_reason}。|


## <a name="error-0052"></a>錯誤 0052  
 如果未正確指定 Azure 儲存體帳戶金鑰，就會發生例外狀況。  

 如果用來存取 Azure 儲存體帳戶的金鑰不正確，就會在 Azure Machine Learning 中發生此錯誤。 例如，如果 Azure 儲存體金鑰在複製並貼上時已截斷，或使用了錯誤的金鑰，您就可能看到此錯誤。  

 如需如何取得 Azure 儲存體帳戶金鑰的詳細資訊，請參閱[檢視、複製和重新產生儲存體存取金鑰](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  

**解決方案：** 重新瀏覽模組，並確認帳戶的 Azure 儲存體金鑰是正確的；如有必須，請從 Azure 傳統入口網站重新複製金鑰。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶金鑰不正確。|


## <a name="error-0053"></a>錯誤 0053  
 當 Machbox 建議沒有使用者特徵或項目時，就會發生例外狀況。  

 找不到特徵向量時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 確定特徵向量存在於輸入資料集中。  

|例外狀況訊息|
|------------------------|
|需要使用者特徵或/和項目，但未提供。|


## <a name="error-0056"></a>錯誤 0056  
 如果您為作業選取的資料行違反需求，就會發生例外狀況。  

 當您為資料行必須屬於特定資料類型的作業選擇資料行時，就會在 Azure Machine Learning 中發生此錯誤。 

 如果資料行是正確的資料類型，但您所使用的模組要求該資料行也須標示為特徵、標籤或類別資料行，也可能會發生此錯誤。  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**解決方案：**

1.  檢閱目前所選資料行的資料類型。 

2. 確定選取的資料行是否為類別、標籤或特徵資料行。  
  
3.  檢閱您在其中進行資料行選取的模組適用的說明主題，以確認是否有資料類型或資料行使用方式的特定需求。  
  
3.  使用[編輯中繼資料](edit-metadata.md)變更此作業執行期間的資料行類型。 如果需要使用資料行進行下游作業，請確實使用[編輯中繼資料](edit-metadata.md)的另一個執行個體將資料行類型變更回原始值。  

|例外狀況訊息|
|------------------------|
|一或多個選取的資料行不在允許的類別中。|
|資料行名稱 "{col_name}" 不在允許的類別中。|


## <a name="error-0057"></a>錯誤 0057  
 當您嘗試建立的檔案或 Blob 已存在時，就會發生例外狀況。  

 如果您使用[匯出資料](export-data.md)模組或其他模組將 Azure Machine Learning 中的管線結果儲存到 Azure Blob 儲存體，但您嘗試建立的檔案或 Blob 已存在，就會發生此例外狀況。   

**解決方案：**

 只有在您先前已將 [Azure Blob 儲存體寫入模式] 屬性設定為 [錯誤] 時，才會出現此錯誤。 根據設計，如果您嘗試將資料集寫入至已存在的 Blob，此模組就會引發錯誤。

 - 開啟模組屬性，並將 [Azure Blob 儲存體寫入模式] 屬性變更為 [覆寫]。
 - 或者，您可以輸入不同目的地 Blob 或檔案的名稱，並確實指定尚不存在的 Blob。  

|例外狀況訊息|
|------------------------|
|檔案或 Blob 已存在。|
|檔案或 Blob "{file_path}" 已存在。|


## <a name="error-0058"></a>錯誤 0058  
 如果資料集未包含預期的標籤資料行，就會在 Azure Machine Learning 中發生此錯誤。  

 當提供的標籤資料行不符合學習模組所預期的資料或資料類型，或具有錯誤的值時，也可能會發生此例外狀況。 例如，如果在定型二元分類器時使用實值標籤資料行，就會產生此例外狀況。  

**解決方案：** 解決方法取決於您所使用的學習模組或定型模組，以及資料集中所含資料行的資料類型。 首先，請確認機器學習演算法或定型模組的需求。  

 重新瀏覽輸入資料集。 請確認您預期要視為標籤的資料行，具有您要建立的模型適用的資料類型。  

 請檢查輸入中是否有遺漏值，並視需要加以排除或取代。  

 如有必要，請新增[編輯中繼資料](edit-metadata.md)模組，並確定標籤資料行已標示為標籤。  

|例外狀況訊息|
|------------------------|
|標籤資料行值與評分標籤資料行值不相符。|
|"{dataset_name}" 中的標籤資料行不符合預期。|
|"{dataset_name}" 中的標籤資料行不符合預期，{reason}。|
|"{dataset_name}" 中的標籤資料行 "{column_name}" 不符合預期。|
|"{dataset_name}" 中的標籤資料行 "{column_name}" 不符合預期，{reason}。|


## <a name="error-0059"></a>錯誤 0059  
 如果無法剖析資料行選擇器中指定的資料行索引，就會發生例外狀況。  

 如果無法剖析在使用資料行選取器時指定的資料行索引，就會在 Azure Machine Learning 中發生此錯誤。  當資料行索引的格式無效而無法剖析時，您將會看到此錯誤。  

**解決方案：** 將資料行索引修改為使用有效的索引值。  

|例外狀況訊息|
|------------------------|
|無法剖析一或多個指定的資料行索引或索引範圍。|
|無法剖析資料行索引或範圍 "{column_index_or_range}"。|


## <a name="error-0060"></a>錯誤 0060  
 當資料行選擇器中指定超出範圍的資料行範圍時，就會發生例外狀況。  

 在資料行選取器中指定了超出範圍的資料行範圍時，就會在 Azure Machine Learning 中發生此錯誤。 如果資料行選擇器中的資料行範圍未對應至資料集中的資料行，您就會看到此錯誤。  

**解決方案：** 修改資料行選擇器中的資料行範圍，以對應至資料集中的資料行。  

|例外狀況訊息|
|------------------------|
|指定的資料行索引範圍無效或超出範圍。|
|資料行範圍 "{column_range}" 無效或超出範圍。|


## <a name="error-0061"></a>錯誤 0061  
 如果您嘗試將一個資料列新增至 DataTable，但其資料行數目與資料表不同，就會發生例外狀況。  

 如果您嘗試將資料列新增至資料集，但其資料行數目與資料集不同，就會在 Azure Machine Learning 中發生此錯誤。  如果要新增至資料集的資料列的資料行數目與輸入資料集不同，您就會看到此錯誤。  如果資料行數目不同，就無法將資料列附加至資料集。  

**解決方案：** 修改輸入資料集，使其資料行數目與新增的資料列相同，或修改新增的資料列，使其具有與資料集相同的資料行數目。  

|例外狀況訊息|
|------------------------|
|所有的資料表必須有相同數目的資料行。|
|區塊 "{chunk_id_1}" 中的資料行與區塊大小為 {chunk_size} 的區塊 "{chunk_id_2}" 不同。|
|檔案 "{filename_1}" 中的資料行計數 (計數={column_count_1}) 與檔案 "{filename_2}" (計數={column_count_2}) 不同。|


## <a name="error-0062"></a>錯誤 0062  
 嘗試比較兩個具有不同學習模組類型的模型時，就會發生例外狀況。  

 當兩個不同評分資料集的評估計量無法比較時，就會在 Azure Machine Learning 中發生此錯誤。 在此情況下，無法對用來產生兩個評分資料集的模型進行有效性的比較。  

**解決方案：** 確認評分的結果是由相同類型的機器學習模型 (二元分類、迴歸、多類別分類、建議、叢集、異常偵測等) 所產生的。您比較的所有模型必須具有相同的學習模組類型。  

|例外狀況訊息|
|------------------------|
|所有模型都必須具有相同的學習模組類型。|
|產生了不相容的學習模組類型："{actual_learner_type}"。 預期的學習模組類型為："{expected_learner_type_list}"。|


## <a name="error-0064"></a>錯誤 0064  
 如果未正確指定 Azure 儲存體帳戶金鑰或儲存體金鑰，就會發生例外狀況。  

 如果未正確指定 Azure 儲存體帳戶名稱或儲存體金鑰，就會在 Azure Machine Learning 中發生此錯誤。 如果您為儲存體帳戶輸入了不正確的帳戶名稱或密碼，就會看到此錯誤。 如果您手動輸入帳戶名稱或密碼，就可能發生這種狀況。 如果帳戶已刪除，也可能發生此狀況。  

**解決方案：** 確認帳戶名稱和密碼皆已正確輸入，且帳戶確實存在。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體帳戶名稱或儲存體金鑰不正確。|
|Azure 儲存體帳戶名稱 "{account_name}" 或帳戶名稱的儲存體金鑰不正確。|


## <a name="error-0065"></a>錯誤 0065  
 如果未正確指定 Azure Blob 名稱，就會發生例外狀況。  

 如果未正確指定 Azure Blob 名稱，就會在 Azure Machine Learning 中發生此錯誤。  您將在下列情況下看到錯誤：  

-   在指定的容器中找不到 Blob。  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   當格式為具有編碼的 Excel 或 CSV 時，僅將容器指定為[匯入資料](import-data.md)要求中的來源；使用這些格式時，不允許串連容器內所有 Blob 的內容。  
  
-   SAS URI 未包含有效 Blob 的名稱。  

**解決方案：** 重新瀏覽擲回例外狀況的模組。 請確認指定的 Blob 確實存在於儲存體帳戶的容器中，且您具有查看 Blob 的權限。 如果您具有使用編碼格式的 Excel 或 CSV，請確認輸入的格式為 **containername/filename**。 確認 SAS URI 包含有效 Blob 的名稱。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體 Blob 名稱不正確。|
|Azure 儲存體 Blob 名稱 "{blob_name}" 不正確。|
|前置詞為 "{blob_name_prefix}" 的 Azure 儲存體 Blob 名稱不存在。|
|在容器 "{container_name}" 下找不到任何 Azure 儲存體 Blob。|
|找不到任何具有萬用字元路徑 "{blob_wildcard_path}" 的 Azure 儲存體 Blob。|


## <a name="error-0066"></a>錯誤 0066  
 如果資源無法上傳至 Azure Blob，就會發生例外狀況。  

 如果資源無法上傳至 Azure Blob，就會在 Azure Machine Learning 中發生此錯誤。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 兩者都會儲存到與包含輸入檔案的帳戶相同的 Azure 儲存體帳戶。  

**解決方案：** 重新瀏覽模組。 請確認 Azure 帳戶名稱、儲存體金鑰和容器皆正確，且帳戶具有寫入容器的權限。  

|例外狀況訊息|
|------------------------|
|資源無法上傳至 Azure 儲存體。|
|檔案 "{source_path}" 無法上傳至作為 "{dest_path}" 的 Azure 儲存體。|


## <a name="error-0067"></a>錯誤 0067  
 如果資料集具有不同於預期的資料行數目，就會發生例外狀況。  

 如果資料集的資料行數目與預期不同，就會在 Azure Machine Learning 中發生此錯誤。  當資料集中的資料行數目與模組在執行期間預期的資料行數目不同時，您就會看到此錯誤。  

**解決方案：** 修改輸入資料集或參數。  

|例外狀況訊息|
|------------------------|
|datatable 中的資料行數目不符合預期。|
|資料集 "{dataset_name}" 中的資料行數目不符合預期。|
|預期應為 "{expected_column_count}" 個資料行，但實際上找到 "{actual_column_count}" 個資料行。|
|在輸入資料集 "{dataset_name}" 中，預期應有 "{expected_column_count}" 個資料行，但實際上找到 "{actual_column_count}" 個資料行。|


## <a name="error-0068"></a>錯誤 0068  
 如果指定的 Hive 指令碼不正確，就會發生例外狀況。  

 如果 Hive SQL Q 指令碼中有語法錯誤，或 Hive 解譯器在執行查詢或指令碼時發生錯誤，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：**

來自 Hive 的錯誤訊息通常會回報在錯誤記錄檔中，以供您根據特定錯誤採取動作。 

+ 開啟模組，並檢查查詢是否有錯誤。  
+ 登入 Hadoop 叢集的 Hive 主控台並執行查詢，藉以確認查詢可在 Azure Machine Learning 外部正常運作。  
+ 嘗試將註解放在 Hive 指令碼的個別行中，而不是將可執行的陳述式和註解放在同一行中。  

### <a name="resources"></a>資源

如需機器學習的 Hive 查詢適用的說明，請參閱下列文章：

+ [建立 Hive 資料表，並從 Azure Blob 儲存體載入資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [使用 Hive 查詢來瀏覽資料表的資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [針對使用 Hive 查詢之 Hadoop 叢集中的資料建立特性](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive for SQL 使用者功能提要 (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|例外狀況訊息|
|------------------------|
|Hive 指令碼不正確。|


## <a name="error-0069"></a>錯誤 0069  
 如果指定的 SQL 指令碼不正確，就會發生例外狀況。  

 如果指定的 SQL 指令碼有語法問題，或指令碼中指定的資料行或資料表無效，就會在 Azure Machine Learning 中發生此錯誤。 

 如果 SQL 引擎在執行查詢或指令碼時發生任何錯誤，您就會看到此錯誤。 SQL 錯誤訊息通常會回報在錯誤記錄檔中，以供您根據特定錯誤採取動作。  

**解決方案：** 重新瀏覽模組，並檢查 SQL 查詢中是否有錯誤。  

 直接登入資料庫伺服器並執行查詢，藉以確認查詢可在 Azure ML 外部正常運作。  

 如果模組例外狀況回報了 SQL 產生的訊息，請根據回報的錯誤採取動作。 例如，錯誤訊息有時會對可能的錯誤提供特定指引：
+ *沒有這類資料行或遺失的資料庫*，表示您可能輸入了不正確的資料行名稱。 如果您確定資料行名稱正確無誤，請嘗試使用方括弧或引號括住資料行識別碼。
+ *\<SQL 關鍵字\>附近有 SQL 邏輯錯誤*，表示您指定的關鍵字前面可能有語法錯誤

  
|例外狀況訊息|
|------------------------|
|SQL 指令碼不正確。|
|SQL 查詢 "{sql_query}" 不正確。|
|SQL 查詢 "{sql_query}" 不正確。 例外狀況訊息：{exception}。|


## <a name="error-0070"></a>錯誤 0070  
 當您嘗試存取不存在的 Azure 資料表時，就會發生例外狀況。  

 當您嘗試存取不存在的 Azure 資料表時，就會在 Azure Machine Learning 中發生此錯誤。 如果您指定了 Azure 儲存體中的資料表，但在讀取或寫入 Azure 資料表儲存體時該資料表並不存在，就會出現此錯誤。 如果您拼錯了所需資料表的名稱，或目標名稱與儲存體類型不相符，就會發生這種情況。 例如，您想要讀取資料表，但卻輸入了 Blob 的名稱。  

**解決方案：** 重新瀏覽模組，以確認資料表的名稱正確無誤。  

|例外狀況訊息|
|------------------------|
|Azure 資料表不存在。|
|Azure 資料表 "{table_name}" 不存在。|


## <a name="error-0072"></a>錯誤 0072  
 若連線逾時，就會發生例外狀況。  

 若連線逾時，就會在 Azure Machine Learning 中發生此錯誤。如果資料來源或目的地目前有連線問題 (例如，網際網路連線速度緩慢)，或者，如果資料集很大，且 (或) 讀取資料的 SQL 查詢執行複雜的處理，您就會看到此錯誤。  

**解決方案：** 確認 Azure 儲存體或網際網路目前是否有連線速度緩慢的問題。  

|例外狀況訊息|
|------------------------|
|發生連線逾時。|


## <a name="error-0073"></a>錯誤 0073  
 如果將資料行轉換為其他類型時發生錯誤，就會發生例外狀況。  

 無法將資料行轉換為另一種類型時，Azure Machine Learning 中就會發生此錯誤。  如果模組需要特定類型，但無法將資料行轉換為新的類型，就會出現此錯誤。  

**解決方案：** 修改輸入資料集，使資料行可根據內部例外狀況進行轉換。  

|例外狀況訊息|
|------------------------|
|無法轉換資料行。|
|無法將資料行轉換為 {target_type}。|


## <a name="error-0075"></a>錯誤 0075  
如果在量化資料集時使用了無效的量化函式，就會發生例外狀況。  

如果您嘗試使用不支援的方法將資料量化，或參數組合無效時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：**

舊版的 Azure Machine Learning 已導入此事件的錯誤處理機制，可支援更多的量化方法自訂。 目前，所有的量化方法均以下拉式清單中的選取為主，因此，在技術上應該不會再發生此錯誤。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|例外狀況訊息|
|------------------------|
|使用了無效的量化函式。|


## <a name="error-0077"></a>錯誤 0077  
 傳入未知的 Blob 檔案寫入模式時，就會發生例外狀況。  

 如果在 Blob 檔案目的地或來源的規格中傳入了無效的引數，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 對 Azure Blob 儲存體進行資料匯入或匯出的模組，幾乎全都會使用下拉式清單來指派控制寫入模式的參數值；因此不可能傳入無效的值，而應該不會出現此錯誤。 後續版本將會淘汰此錯誤。  

|例外狀況訊息|
|------------------------|
|不支援的 Blob 寫入模式。|
|不支援的 Blob 寫入模式：{blob_write_mode}。|


## <a name="error-0078"></a>錯誤 0078  
 [匯入資料](import-data.md)的 HTTP 選項收到表示重新導向的 3xx 狀態碼時，就會發生例外狀況。  

 當[匯入資料](import-data.md)的 HTTP 選項收到 3xx (301、302、304 等等) 狀態碼 (表示重新導向) 時，就會在 Azure Machine Learning 中發生此錯誤。 如果您嘗試連線至會將瀏覽器重新導向至另一個頁面的 HTTP 來源，就會看到此錯誤。 基於安全考量，不允許以重新導向網站作為 Azure Machine Learning 的資料來源。  

**解決方案：** 如果網站是受信任的網站，請直接輸入重新導向的 URL。  

|例外狀況訊息|
|------------------------|
|不允許 HTTP 重新導向。|


## <a name="error-0079"></a>錯誤 0079  
 如果未正確指定 Azure 儲存體容器名稱，就會發生例外狀況。  

 如果未正確指定 Azure 儲存體容器名稱，就會在 Azure Machine Learning 中發生此錯誤。 如果您在寫入 Azure Blob 儲存體時未使用**以容器開頭的 Blob 路徑**來指定容器和 Blob (檔案) 名稱，您就會看到此錯誤。  

**解決方案：** 重新瀏覽[匯出資料](export-data.md)模組，並確認 Blob 的指定路徑包含容器和檔案名稱，格式為 **container/filename**。  

|例外狀況訊息|
|------------------------|
|Azure 儲存體容器名稱不正確。|
|Azure 儲存體容器名稱 "{container_name}" 不正確；容器名稱的格式應為 container/blob。|


## <a name="error-0080"></a>錯誤 0080  
 當模組不允許具有所有遺漏值的資料行時，就會發生例外狀況。  

 當模組所使用的一或多個資料行包含所有遺漏值時，就會在 Azure Machine Learning 中發生此錯誤。 例如，如果模組正在計算每個資料行的彙總統計資料，將無法對不含任何資料的資料行執行計算。 在這種情況下，模組執行將會停止，並出現此例外狀況。  

**解決方案：** 重新瀏覽輸入資料集，並移除任何包含所有遺漏值的資料行。  

|例外狀況訊息|
|------------------------|
|不允許具有所有遺漏值的資料行。|
|資料行 {col_index_or_name} 具有所有遺漏的值。|


## <a name="error-0081"></a>錯誤 0081  
 在 PCA 模組中，如果要減少到的維度數目等於輸入資料集的特徵資料行數目 (包含至少一個疏鬆特徵資料行)，就會發生例外狀況。  

 如果符合下列條件，就會在 Azure Machine Learning 中發生此錯誤：(a) 輸入資料集至少有一個疏鬆資料行，且 (b) 要求的最終維度數目與輸入維度的數目相同。  

**解決方案：** 考慮將輸出中的維度數目減少到小於輸入中的維度數目。 這在 PCA 的應用程式中是常見的做法。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|例外狀況訊息|
|------------------------|
|包含要減少維度數量的疏鬆特徵資料行的資料集，應小於特徵資料行數目。|


## <a name="error-0082"></a>錯誤 0082  
 模型無法成功還原序列化時，就會發生例外狀況。  

 當較新版的 Azure Machine Learning 執行階段因重大變更而無法載入已儲存的機器學習模型或轉換時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 產生模型或轉換的定型管線必須重新執行，且必須重新儲存模型或轉換。  

|例外狀況訊息|
|------------------------|
|模型可能是以較舊的序列化格式進行序列化，因此無法還原序列化。 請重新定型，並重新儲存模型。|


## <a name="error-0083"></a>錯誤 0083  
 如果用於定型的資料集不適用於具體類型的學習模組，就會發生例外狀況。  

 當資料集與正在定型的學習模組不相容時，Azure Machine Learning 中就會產生此錯誤。 例如，資料集可能會在每個資料列中都至少包含一個遺漏值，因此在定型期間會略過整個資料集。 在其他情況下，某些機器學習演算法 (例如異常偵測) 未預期有標籤存在，一旦資料集中有標籤存在，就可能擲回此例外狀況。  

**解決方案：** 參閱用來檢查輸入資料集需求的學習模組適用的文件。 檢查資料行，以確認所有必要的資料行皆存在。  

|例外狀況訊息|
|------------------------|
|用於定型的資料集無效。|
|{data_name} 包含不適用於定型的資料。|
|{data_name} 包含不適用於定型的資料。 學習模組類型：{learner_type}。|
|{data_name} 包含不適用於定型的資料。 學習模組類型：{learner_type}。 原因：{reason}。|
|無法將 "{action_name}" 動作套用至定型資料 {data_name}。 原因：{reason}。|


## <a name="error-0084"></a>錯誤 0084  
 評估從 R 指令碼產生的分數時發生例外狀況。 目前無此支援。  

 如果您嘗試使用其中一個模組透過 R 指令碼的輸出 (包含分數) 來評估模型，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|目前不支援對自訂模型產生的分數進行評估。|


## <a name="error-0085"></a>錯誤 0085  
 指令碼評估失敗並發生錯誤時，就會發生例外狀況。  

 當您執行包含語法錯誤的自訂指令碼時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 在外部編輯器中檢閱您的程式碼，並檢查是否有錯誤。  

|例外狀況訊息|
|------------------------|
|指令碼評估期間發生錯誤。|
|在指令碼評估期間發生下列錯誤，請檢視輸出記錄檔以取得詳細資訊：<br />---------- {script_language} 解譯器錯誤訊息的開頭 ----------<br />{message}<br />---------- {script_language} 解譯器錯誤訊息的結尾 ----------|


## <a name="error-0090"></a>錯誤 0090  
 Hive 資料表建立失敗時，就會發生例外狀況。  

 如果您使用[匯出資料](export-data.md)或另一個選項將資料儲存至 HDInsight 叢集，但無法建立指定的 Hive 資料表，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 檢查與叢集相關聯的 Azure 儲存體帳戶名稱，並確認您在模組屬性中使用的是相同帳戶。  

|例外狀況訊息|
|------------------------|
|無法建立 Hive 資料表。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱與透過模組參數傳入的名稱相同。|
|無法建立 Hive 資料表 "{table_name}"。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱與透過模組參數傳入的名稱相同。|
|無法建立 Hive 資料表 "{table_name}"。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱為 "{cluster_name}"。|


## <a name="error-0102"></a>錯誤 0102  
 在無法解壓縮 ZIP 檔案時擲回。  

 如果您匯入副檔名為 .zip 的壓縮套件，但該套件不是 zip 檔案，或檔案未使用支援的 zip 格式，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 確定選取的檔案是有效的 .zip 檔案，並且已使用其中一種支援的壓縮演算法進行壓縮。  

 如果您以壓縮格式匯入資料集時看到此錯誤，請確認所有內含的檔案均使用其中一種支援的檔案格式，且皆為 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 請嘗試將所需的檔案讀入新的壓縮 Zip 資料夾，然後再次嘗試新增自訂模組。  

|例外狀況訊息|
|------------------------|
|指定的 ZIP 檔案不是正確格式。|


## <a name="error-0105"></a>錯誤 0105  
 當模組定義檔包含不支援的參數類型時，就會顯示此錯誤  
  
 如果您建立自訂模組 xml 定義，但定義中的參數或引數類型與支援的類型不相符，就會在 Azure Machine Learning 中發生此錯誤。  
  
**解決方案：** 確定自訂模組 xml 定義檔中任何 **Arg** 元素的類型屬性皆為支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的參數類型。|  
|指定了不支援的參數類型 '{0}'。|  


## <a name="error-0107"></a>錯誤 0107  
 在模組定義檔定義了不受支援的輸出類型時擲回  
  
 當自訂模組 xml 定義中的輸出連接埠類型不符合支援的類型時，就會在 Azure Machine Learning 中發生此錯誤。  
  
**解決方案：** 在自訂模組 xml 定義檔中，確定輸出元素的類型屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸出類型。|  
|指定了不支援的輸出類型 '{output_type}'。|  


## <a name="error-0125"></a>錯誤 0125  
 在多個資料集的結構描述不相符時擲回。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|資料集結構描述不相符。|


## <a name="error-0127"></a>錯誤 0127  
 影像像素大小超過允許的限制  

 如果您要讀取影像資料集中的影像以進行分類，但影像超出模型所能處理的大小，就會發生此錯誤。  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|例外狀況訊息|
|------------------------|
|影像像素大小超過允許的限制。|
|檔案 '{file_path}' 中的影像像素大小超過允許的限制：'{size_limit}'。|


## <a name="error-0128"></a>錯誤 0128  
 類別資料行的條件機率數目超過限制。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|類別資料行的條件機率數目超過限制。|
|類別資料行的條件機率數目超過限制。 資料行 '{column_name_or_index_1}' 和 '{column_name_or_index_2}' 是有問題的配對。|


## <a name="error-0129"></a>錯誤 0129  
 資料集中的資料行數目超過允許的限制。  

**解決方案：**

|例外狀況訊息|
|------------------------|
|資料集中的資料行數目超過允許的限制。|
|資料集 '{dataset_name}' 中的資料行數目超過允許的數目。|
|資料集 '{dataset_name}' 中的資料行數目超過 '{component_name}' 允許的限制。|
|資料集 '{dataset_name}' 中的資料行數目超過 '{component_name}' 允許的限制 '{limit_columns_count}'。|


## <a name="error-0134"></a>錯誤 0134
當標籤資料行遺失或已標記的資料列數目不足時，就會發生例外狀況。  

如果模組需要標籤資料行，但您的資料行選取中未包含此資料行，或標籤資料行遺漏太多值，就會發生此錯誤。

如果先前的作業變更了資料集，使得可供下游作業使用的資料列不足，也可能會發生此錯誤。 例如，假設您在**資料分割和取樣**模組中使用運算式，以依據值來分割資料集。 如果找不到運算式的相符項目，資料分割所產生的其中一個資料集就會是空的。

解決方案： 

 如果您的資料行選取中包含標籤資料行，但無法加以辨識，請使用[編輯中繼資料](edit-metadata.md)模組將其標示為標籤資料行。

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  然後，您可以使用[清除遺漏的資料](clean-missing-data.md)模組，移除在標籤資料行中有遺漏值的資料列。 

 請檢查您的輸入資料集，以確定其中包含有效的資料，並且有足夠的資料列可因應作業需求。 許多演算法在需要最小的資料列數目的情況下，若資料僅包含幾個資料列，或只有一個標頭，就會產生錯誤訊息。

|例外狀況訊息|
|------------------------|
|當標籤資料行遺失或已標記的資料列數目不足時，就會發生例外狀況。|
|當標籤資料行遺失，或已標記的資料列少於 {required_rows_count} 個時，就會發生例外狀況。|
|當資料集 {dataset_name} 中的標籤資料行遺失，或已標記的資料列少於 {required_rows_count} 個時，就會發生例外狀況。|


## <a name="error-0138"></a>錯誤 0138  
 記憶體已用盡，無法完成模組的執行。 縮小資料集取樣可能有助於緩解此問題。  

 當執行的模組所需的記憶體超過 Azure 容器中的可用數量時，就會發生此錯誤。 如果您使用的是大型資料集，而記憶體不敷目前的作業執行所需，就可能會發生這種狀況。  

**解決方案：** 如果您嘗試讀取大型資料集，但無法完成作業，縮小資料集取樣可能會有幫助。  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|例外狀況訊息|
|------------------------|
|記憶體已用盡，無法完成模組的執行。|
|記憶體已用盡，無法完成模組的執行。 詳細資料：{details}|


## <a name="error-0141"></a>錯誤 0141  
 如果所選數值資料行的數目以及類別和字串資料行中的唯一值數目太少，就會發生例外狀況。  

 當選取的資料行中沒有足夠的唯一值可執行作業時，就會在 Azure Machine Learning 中發生此錯誤。  

**解決方案：** 某些作業會對特徵和類別資料行執行統計作業，一旦沒有足夠的值，作業就可能失敗，或傳回無效的結果。 請檢查您的資料集，以確認特徵和標籤資料行中有多少個值，並判斷您嘗試執行的作業在統計上是否有效。  

 如果源資料集有效，您也可以檢查是否有某項上游資料操作或中繼資料作業變更了資料，並將某些值移除。  

 如果上游作業包含分割、取樣或重新取樣，請確認輸出所包含的資料列和值數目符合預期。  

|例外狀況訊息|
|------------------------|
|所選數值資料行的數目以及類別和字串資料行中的唯一值數目太少。|
|選取的數值資料行以及類別和字串資料行中唯一值的總數 (目前為 {actual_num})，至少應為 {lower_boundary}。|


## <a name="error-0154"></a>錯誤 0154  
 使用者嘗試聯結具有不相容資料行類型的索引鍵資料行時，就會發生例外狀況。

|例外狀況訊息|
|------------------------|
|索引鍵資料行元素類型不相容。|
|索引鍵資料行元素類型不相容。(左：{keys_left}；右：{keys_right})|


## <a name="error-0155"></a>錯誤 0155  
 資料集的資料行名稱不是字串時，就會發生例外狀況。

|例外狀況訊息|
|------------------------|
|dataframe 資料行名稱必須是字串類型。 資料行名稱不是字串。|
|dataframe 資料行名稱必須是字串類型。 資料行名稱 {column_names} 不是字串。|


## <a name="error-0156"></a>錯誤 0156  
 無法從 Azure SQL Database 讀取資料時，就會發生例外狀況。

|例外狀況訊息|
|------------------------|
|無法從 Azure SQL Database 讀取資料。|
|無法從 Azure SQL Database 讀取資料：{detailed_message} DB：{database_server_name}:{database_name} 查詢：{sql_statement}|


## <a name="error-0157"></a>錯誤 0157  
 找不到資料存放區。

|例外狀況訊息|
|------------------------|
|資料存放區資訊無效。|
|資料存放區資訊無效。 無法取得工作區 '{workspace_name}' 中的 AzureML 資料存放區 '{datastore_name}'。|


## <a name="error-0158"></a>錯誤 0158
 在轉換目錄無效時擲回。

|例外狀況訊息|
|------------------------------------------------------------|
|給定的 TransformationDirectory 無效。|
|TransformationDirectory "{arg_name}" 無效。 原因：{reason}。 請重新執行訓練實驗以產生轉換檔案。 如果訓練實驗已刪除，請重新建立轉換檔案並加以儲存。|
|TransformationDirectory "{arg_name}" 無效。 原因：{reason}。 {troubleshoot_hint}|


## <a name="error-0159"></a>錯誤 0159
 如果傳至模組的模型目錄無效，就會發生例外狀況。 

|例外狀況訊息|
|------------------------------------------------------------|
|給定的 ModelDirectory 無效。|
|ModelDirectory "{arg_name}" 無效。|
|ModelDirectory "{arg_name}" 無效。 原因：{reason}。|
|ModelDirectory "{arg_name}" 無效。 原因：{reason}。 {troubleshoot_hint}|


## <a name="error-1000"></a>錯誤 1000  
內部程式庫例外狀況。  

提供此錯誤的用意，是為了擷取其他未處理的內部引擎錯誤。 因此，此錯誤的成因可能隨著產生錯誤的模組而有所不同。  

如需更多協助，建議您將錯誤附帶的詳細訊息張貼至 Azure Machine Learning 論壇，並附上案例的說明，包括作為輸入的資料。 這樣的意見反應可協助我們設定錯誤的優先順序，並找出最重要的問題以進一步處理。  

|例外狀況訊息|
|------------------------|
|程式庫例外狀況。|
|程式庫例外狀況：{exception}。|
|未知的程式庫例外狀況：{exception}。 {customer_support_guidance}。|

