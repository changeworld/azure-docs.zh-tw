---
title: 使用 Python 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 Python 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.custom: devx-track-python
ms.openlocfilehash: f57fa1b668cacdaaceb2026f9e5d7fff0cc56d7f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371353"
---
# <a name="azure-storage-samples-using-v12-python-client-libraries"></a>使用 v12 Python 用戶端程式庫的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

> [!NOTE]
> 這些範例會使用最新的 Azure 儲存體 .NET v12 程式庫。 如需舊版的 v2.1 程式碼，請參閱 [Azure 儲存體：開始在 Python 中使用 Azure 儲存體](https://github.com/Azure-Samples/storage-blob-python-getting-started) (位於 GitHub 存放庫中)。

## <a name="blob-samples"></a>Blob 範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [使用連接字串建立 Blob 服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L45)
   :::column-end:::
   :::column span="":::
      [使用連接字串建立容器用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L50)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用連接字串建立 Blob 用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L56)
   :::column-end:::
   :::column span="":::
      [使用共用存取金鑰建立 Blob 服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [從 URL 建立 Blob 用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L75)
   :::column-end:::
   :::column span="":::
      [建立 Blob 用戶端 SAS URL](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 ClientSecretCredential 建立 Blob 服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L88)
   :::column-end:::
   :::column span="":::
      [建立 SAS 權杖](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L110)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 Azure 身分識別建立 Blob 服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L125)
   :::column-end:::
   :::column span="":::
      [建立 Blob 快照集](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L56)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob 服務

:::row:::
   :::column span="":::
      [取得 Blob 服務帳戶資訊](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L30)
   :::column-end:::
   :::column span="":::
      [設定 Blob 服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得 Blob 服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L61)
   :::column-end:::
   :::column span="":::
      [取得 Blob 服務統計資料](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L71)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用服務用戶端建立容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L82)
   :::column-end:::
   :::column span="":::
      [列出容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用服務用戶端刪除容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L103)
   :::column-end:::
   :::column span="":::
      [取得容器用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L117)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [取得 Blob 用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L130)
   :::column-end:::
:::row-end:::

### <a name="container"></a>容器

:::row:::
   :::column span="":::
      [從服務建立容器用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L35)
   :::column-end:::
   :::column span="":::
      [使用 SAS URL 建立容器用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L44)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用容器用戶端建立容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L52)
   :::column-end:::
   :::column span="":::
      [取得容器屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用容器用戶端刪除容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L61)
   :::column-end:::
   :::column span="":::
      [在容器上取得租用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [設定容器中繼資料](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L101)
   :::column-end:::
   :::column span="":::
      [設定容器存取原則](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L128)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得容器存取原則](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L141)
   :::column-end:::
   :::column span="":::
      [產生 SAS 權杖](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L145)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用 SAS 權杖建立容器用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L158)
   :::column-end:::
   :::column span="":::
      [將 Blob 上傳至容器](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L182)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [列出容器中的 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L189)
   :::column-end:::
   :::column span="":::
      [取得 Blob 用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L213)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [上傳 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L68)
   :::column-end:::
   :::column span="":::
      [下載 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [刪除 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L80)
   :::column-end:::
   :::column span="":::
      [取消刪除 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得 Blob 屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L105)
   :::column-end:::
   :::column span="":::
      [刪除多個 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L133)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [從 URL 複製 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L193)
   :::column-end:::
   :::column span="":::
      [中止從 URL 複製 Blob](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L205)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [在 Blob 上取得租用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L167)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 範例

### <a name="data-lake-service"></a>Data Lake 服務

:::row:::
   :::column span="2":::
      [建立 Data Lake 服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L64)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>檔案系統

:::row:::
   :::column span="":::
      [建立檔案系統用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L74)
   :::column-end:::
   :::column span="":::
      [刪除檔案系統](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L81)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>目錄

:::row:::
   :::column span="":::
      [建立目錄用戶端](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L14)
   :::column-end:::
   :::column span="":::
      [取得目錄權限](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [設定目錄權限](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L23)
   :::column-end:::
   :::column span="":::
      [重新命名目錄](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得目錄屬性](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L25)
   :::column-end:::
   :::column span="":::
      [刪除目錄](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L29)
   :::column-end:::
:::row-end:::

### <a name="file"></a>檔案

:::row:::
   :::column span="":::
      [建立檔案用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L13)
   :::column-end:::
   :::column span="":::
      [建立檔案](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得檔案權限](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L35)
   :::column-end:::
   :::column span="":::
      [設定檔案權限](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [將資料附加至檔案](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L18)
   :::column-end:::
   :::column span="":::
      [從檔案讀取資料](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L29)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure 檔案儲存體範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [從連接字串建立共用服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L42)
   :::column-end:::
   :::column span="":::
      [從帳戶和存取金鑰建立共用服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [產生 SAS 權杖](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L59)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>檔案服務

:::row:::
   :::column span="":::
      [設定服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L39)
   :::column-end:::
   :::column span="":::
      [取得服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用檔案服務用戶端建立共用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L77)
   :::column-end:::
   :::column span="":::
      [使用檔案服務用戶端列出共用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用檔案服務用戶端刪除共用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L91)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>檔案共用

:::row:::
   :::column span="":::
      [從連接字串建立共用用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L52)
   :::column-end:::
   :::column span="":::
      [取得共用用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用檔案共用用戶端建立共用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L39)
   :::column-end:::
   :::column span="":::
      [建立共用快照集](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用檔案共用用戶端刪除共用](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L47)
   :::column-end:::
   :::column span="":::
      [設定共用配額](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L61)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [設定共用中繼資料](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L66)
   :::column-end:::
   :::column span="":::
      [取得共用屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L47)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>目錄

:::row:::
   :::column span="":::
      [建立目錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L45)
   :::column-end:::
   :::column span="":::
      [將檔案上傳到目錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [從目錄中刪除檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L55)
   :::column-end:::
   :::column span="":::
      [刪除目錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [建立子目錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L80)
   :::column-end:::
   :::column span="":::
      [列出目錄和檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [刪除子目錄](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L104)
   :::column-end:::
   :::column span="":::
      [取得子目錄用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L121)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [列出目錄中的檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L87)
   :::column-end:::
:::row-end:::

### <a name="file"></a>檔案

:::row:::
   :::column span="":::
      [建立檔案用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L65)
   :::column-end:::
   :::column span="":::
      [建立檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [上傳檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L54)
   :::column-end:::
   :::column span="":::
      [下載檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [刪除檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L67)
   :::column-end:::
   :::column span="":::
      [從 URL 複製檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L101)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>佇列範例

### <a name="authentication"></a>驗證

:::row:::
   :::column span="":::
      [使用連接字串進行驗證](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L50)
   :::column-end:::
   :::column span="":::
      [建立佇列服務用戶端權杖](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [從連接字串建立佇列用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L35)
   :::column-end:::
   :::column span="":::
      [產生佇列用戶端 SAS 權杖](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L61)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>佇列服務

:::row:::
   :::column span="":::
      [建立佇列服務用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L60)
   :::column-end:::
   :::column span="":::
      [設定佇列服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L35)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得佇列服務屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L67)
   :::column-end:::
   :::column span="":::
      [使用服務用戶端建立佇列](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L76)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [使用服務用戶端刪除佇列](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L94)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>佇列

:::row:::
   :::column span="":::
      [建立佇列用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L72)
   :::column-end:::
   :::column span="":::
      [設定佇列中繼資料](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [取得佇列屬性](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L100)
   :::column-end:::
   :::column span="":::
      [使用佇列用戶端建立佇列](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [使用佇列用戶端刪除佇列](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L62)
   :::column-end:::
   :::column span="":::
      [列出佇列](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [取得佇列用戶端](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L103)
   :::column-end:::
:::row-end:::

### <a name="message"></a>訊息

:::row:::
   :::column span="":::
      [傳送訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L117)
   :::column-end:::
   :::column span="":::
      [接收訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L125)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [查看訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L197)
   :::column-end:::
   :::column span="":::
      [更新訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L222)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [刪除訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L165)
   :::column-end:::
   :::column span="":::
      [清除訊息](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L173)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [設定訊息存取原則](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L47)
   :::column-end:::
:::row-end:::


## <a name="table-samples-sdk-v21"></a>資料表範例 (SDK v2.1)

:::row:::
   :::column span="":::
      [建立資料表](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46)
   :::column-end:::
   :::column span="":::
      [刪除實體/資料表](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [插入/合併/取代實體](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57)
   :::column-end:::
   :::column span="":::
      [查詢實體](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [查詢資料表](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py)
   :::column-end:::
   :::column span="":::
      [資料表 ACL/屬性](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [更新實體](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 程式碼範例程式庫

若要檢視完整的 Python 範例程式庫，請移至：

* [Azure Blob 程式碼範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)
* [Azure Data Lake 程式碼範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Azure 檔案儲存體程式碼範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples)
* [Azure 佇列程式碼範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

您可以瀏覽並複製每個程式庫的 GitHub 存放庫。

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [開始在 Python 中使用 Azure Blob 服務](../blobs/storage-quickstart-blobs-python.md) \(英文\)
* [開始在 Python 中使用 Azure 佇列服務](../queues/storage-quickstart-queues-python.md) \(英文\)
* [開始在 Python 中使用 Azure 表格服務](../../cosmos-db/table-storage-how-to-use-python.md) \(英文\)
* [開始在 Python 中使用 Azure 檔案服務](../files/storage-python-how-to-use-file-storage.md) \(英文\)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 儲存體範例](storage-samples-javascript.md)
* C++：[使用 C++ 的 Azure 儲存體範例](storage-samples-c-plus-plus.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
