---
title: 使用 JavaScript 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 JavaScript/Node.js 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/19/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 1d6b4d37a3d7c4accc3e407fb6cf841600ac1c16
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485840"
---
# <a name="azure-storage-samples-using-v12-javascript-client-libraries"></a>使用 v12 JavaScript 用戶端程式庫的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

> [!NOTE]
> 這些範例會使用最新的 Azure 儲存體 JavaScript v12 程式庫。 如需舊版的 v11 程式碼，請參閱 GitHub 存放庫中的[開始使用 Node.js 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started)。

## <a name="blob-samples"></a>Blob 範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [使用連接字串進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
   :::column span="":::
      [使用 SAS 連接字串進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用共用金鑰認證進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/sharedKeyCred.js#L5)
   :::column-end:::
   :::column span="":::
      [使用 AnonymousCredential 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/anonymousCred.js#L18)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 Azure Active Directory 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/azureAdAuth.js#L47)
   :::column-end:::
   :::column span="":::
      [使用 Proxy 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/proxyAuth.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用自訂管線來連線](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/customPipeline.js#L26)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob 服務

:::row:::
   :::column span="2":::
      [使用 SAS URL 建立 Blob 服務用戶端](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L39)
   :::column-end:::
:::row-end:::

### <a name="container"></a>容器

:::row:::
   :::column span="":::
      [建立容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L53)
   :::column-end:::
   :::column span="":::
      [使用共用金鑰認證建立容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L48)
   :::column-end:::
   :::column span="":::
      [使用列舉程式列出容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [依頁面列出容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L53)
   :::column-end:::
   :::column span="":::
      [刪除容器](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L82)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [建立 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L60)
   :::column-end:::
   :::column span="":::
      [列出 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L67)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [下載 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L73)
   :::column-end:::
   :::column span="":::
      [使用列舉程式列出 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [依頁面列出 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L66)
   :::column-end:::
   :::column span="":::
      [依階層列出 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs-hierarchy.js#L70)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [不使用 await 而列出 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-without-await.js#L42)
   :::column-end:::
   :::column span="":::
      [建立 Blob 快照集](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [下載 Blob 快照集](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L61)
   :::column-end:::
   :::column span="":::
      [以平行方式將串流上傳至 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [以平行方式下載區塊 Blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L99)
   :::column-end:::
   :::column span="":::
      [在 Blob 上設定存取層](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L118)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>疑難排解

:::row:::
   :::column span="2":::
      [使用容器用戶端觸發可復原的錯誤](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/errorsAndResponses.js#L33)
   :::column-end:::
:::row-end:::


## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 範例

:::row:::
   :::column span="":::
      [建立 Data Lake 服務用戶端](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L36)
   :::column-end:::
   :::column span="":::
      [建立檔案系統](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L47)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出檔案系統](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L42)
   :::column-end:::
   :::column span="":::
      [建立檔案](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L54)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出檔案系統中的路徑](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L63)
   :::column-end:::
   :::column span="":::
      [下載檔案](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [刪除檔案系統](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L78)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure 檔案儲存體範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [使用連接字串進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [使用共用金鑰認證進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 AnonymousCredential 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [使用自訂管線來連線](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用 Proxy 來連線](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/proxyAuth.js)
   :::column-end:::
:::row-end:::

### <a name="share"></a>共用

:::row:::
   :::column span="":::
      [建立共用](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L48)
   :::column-end:::
   :::column span="":::
      [列出共用](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [依頁面列出共用](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L51)
   :::column-end:::
   :::column span="":::
      [刪除共用](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L104)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>目錄

:::row:::
   :::column span="":::
      [建立目錄](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L54)
   :::column-end:::
   :::column span="":::
      [列出檔案和目錄](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [依頁面列出檔案和目錄](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>檔案

:::row:::
   :::column span="":::
      [以平行方式上傳檔案](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L65)
   :::column-end:::
   :::column span="":::
      [以平行方式上傳可讀取的串流](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [以平行方式下載檔案](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L93)
   :::column-end:::
   :::column span="":::
      [列出檔案控制代碼](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [依頁面列出檔案控制代碼](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L79)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>佇列範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [使用連接字串進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [使用共用金鑰認證進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 AnonymousCredential 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [使用自訂管線來連線](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 Proxy 來連線](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/proxyAuth.js)
   :::column-end:::
   :::column span="":::
      [使用 Azure Active Directory 進行驗證](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/azureAdAuth.js)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>佇列服務

:::row:::
   :::column span="2":::
      [建立佇列服務用戶端](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L42)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>佇列

:::row:::
   :::column span="":::
      [建立新佇列](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L54)
   :::column-end:::
   :::column span="":::
      [列出佇列](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [依頁面列出佇列](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L51)
   :::column-end:::
   :::column span="":::
      [刪除佇列](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L89)
   :::column-end:::
:::row-end:::

### <a name="message"></a>訊息

:::row:::
   :::column span="":::
      [將訊息傳送至佇列](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L62)
   :::column-end:::
   :::column span="":::
      [查看訊息](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [接收訊息](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L76)
   :::column-end:::
   :::column span="":::
      [刪除訊息](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L80)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>資料表範例 (v11)

:::row:::
   :::column span="":::
      [批次實體](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87)
   :::column-end:::
   :::column span="":::
      [建立資料表](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [刪除實體/資料表](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67)
   :::column-end:::
   :::column span="":::
      [插入/合併/取代實體](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出資料表](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63)
   :::column-end:::
   :::column span="":::
      [查詢實體](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [查詢資料表](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140)
   :::column-end:::
   :::column span="":::
      [範圍查詢](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [共用存取簽章 (SAS)](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87)
   :::column-end:::
   :::column span="":::
      [資料表 ACL](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [資料表跨原始來源資源共用 (CORS) 規則](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149)
   :::column-end:::
   :::column span="":::
      [資料表屬性](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [資料表統計資料](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243)
   :::column-end:::
   :::column span="":::
      [更新實體](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 程式碼範例程式庫

若要檢視完整的 JavaScript 範例程式庫，請移至：

* [Azure Blob 程式碼範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples/javascript)
* [Azure Data Lake 程式碼範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples/javascript)
* [Azure 檔案儲存體程式碼範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-share/samples/javascript)
* [Azure 佇列程式碼範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples/javascript)

您可以瀏覽並複製每個程式庫的 GitHub 存放庫。

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [開始使用 JavaScript 中的 Azure Blob 服務](../blobs/storage-quickstart-blobs-nodejs.md)
* [開始使用 JavaScript 中的 Azure 佇列服務](../queues/storage-quickstart-queues-nodejs.md)
* [開始使用 JavaScript 中的 Azure 資料表服務](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* Python：[使用 Python 的 Azure 儲存體範例](storage-samples-python.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
