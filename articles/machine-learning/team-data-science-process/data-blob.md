---
title: 使用進階分析處理 Azure Blob 資料 - Team Data Science Process
description: 使用進階分析來探索資料，並從 Azure Blob 儲存體中所儲存的資料產生特徵。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bb2a9bf8c26b1abfca0685248fef2058d63c03bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087550"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>處理使用進階分析的 Azure Blob 資料
本文件涵蓋探索資料以及從 Azure Blob 儲存體中儲存的資料產生功能的說明。 

## <a name="load-the-data-into-a-pandas-data-frame"></a>將資料載入至 Pandas 資料框架
若要探索和運算元據集，必須從 blob 來源將資料集下載到可在 Pandas 資料框架中載入的本機檔案。 以下是此程序的遵循步驟：

1. 使用 Blob 服務，透過下列 Python 程式碼範例，從 Azure blob 下載資料。 使用您的特定值來取代下列程式碼中的變數： 
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```
2. 從下載的檔案中，將資料讀取至 Pandas 資料框架。
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

現在您已經準備好探索資料並在此資料集上產生功能。

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>資料探索
以下是數個可使用 Pandas 探索資料的範例方式：

1. 檢查資料列和資料行的數目 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. 檢查資料集中的前幾個或最後幾個資料列，如下所示：
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. 檢查使用下列程式碼範例匯入之每個資料行的資料類型
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. 檢查資料集中資料行的基本統計資料，如下所示
   
    ```python
    dataframe_blobdata.describe()
    ```
5. 查看每個資料行值的項目數，如下所示
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. 使用下列程式碼範例，來計算每個資料行中的遺漏值與實際項目數
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. 如果您在資料的特定資料行中有遺漏值，則可卸除它們，如下所示：
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   取代遺漏值的另一種方式是使用模式函式：
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. 使用變動數目的分類收納組來建立長條圖，以繪製變數的分佈    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. 使用散佈圖或使用內建的相互關聯函式，來查看變數之間的相互關聯
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>功能產生
我們可以使用 Python 來產生功能，如下所示：

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>以指標值為基礎的特徵產生
類別功能可使用如下的方式來建立：

1. 檢查類別資料行的分佈：
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. 產生每個資料行值的指標值
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. 將指標資料行與原始資料框架聯結在一起 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. 移除原始變數本身：
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>分類收納功能產生
若要產生分類收納功能，我們可使用如下的方式繼續進行：

1. 新增一系列的資料行，以分類收納數值資料行
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. 將分類收納轉換為一系列的布林值變數
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. 最後，將虛擬變數新增回原始資料框架中
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>將資料寫回 Azure Blob 並在 AzureMachine Learning 中取用
在您探索資料並建立必要的功能之後，您可以使用下列步驟，將資料 (取樣或特徵化) 上傳至 Azure blob，並在 Azure Machine Learning 中使用：其他功能也可在 Azure Machine Learning Studio (傳統) 中建立。 

1. 將資料框架寫入本機檔案中
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. 將資料上傳至 Azure Blob，如下所示：
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
    try:
   
    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. 現在您可以使用 Azure Machine Learning [匯入資料][import-data] 模組從 Blob 讀取資料，如以下畫面所示：

![讀取器 Blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

