---
title: Azure Blob 儲存體中的範例資料-Team Data 科學流程
description: 以程式設計方式下載儲存在 Azure Blob 儲存體中的資料，然後使用以 Python 撰寫的程式進行取樣。
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
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788836"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Azure Blob 儲存體中的範例資料

本文涵蓋以程式設計方式下載 Azure Blob 儲存體儲存在中的資料，然後使用以 Python 撰寫的程式進行取樣。

**為何要對您的資料進行取樣？**
如果您規劃分析的資料集很龐大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。 取樣有助於資料理解、探索和特徵工程。 它在 Cortana 分析程序中扮演的角色是能夠快速建立資料處理函式與機器學習服務模型的原型。

這個取樣工作是 [Team Data Science Process (TDSP)](./index.yml)中的一個步驟。

## <a name="download-and-down-sample-data"></a>下載和降低取樣資料
1. 使用下列範例 Python 程式碼中的 Blob 服務，從 Azure Blob 儲存體下載資料： 

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

2. 從上述下載的檔案中將資料讀取至 Pandas 資料框架。

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. 使用 `numpy` 的 `random.choice` 以進行降低取樣資料，如下所示：

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

現在您可以使用其中一個 Percent 範例來處理上述的資料框架，以進行進一步的探索和功能產生。

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>將資料上傳並將其讀入 Azure Machine Learning
您可以使用下列程式碼範例，對資料進行向下取樣，並直接在 Azure Machine Learning 中使用它：

1. 將資料框架寫入本機檔案

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. 使用下列程式碼範例，將本機檔案上傳至 Azure Blob：

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
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. 使用 Azure Machine Learning [匯入資料](/azure/machine-learning/studio-module-reference/import-data) 從 Azure Blob 讀取資料，如下圖所示：

![讀取器 Blob](./media/sample-data-blob/reader_blob.png)
