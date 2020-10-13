---
title: Microsoft Azure 資料箱 Blob 儲存體需求 | Microsoft Docs
description: 了解針對 Azure 資料箱 Blob 儲存體所支援的 API、SDK 和用戶端程式庫版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744083"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure 資料箱 Blob 儲存體需求

本文列出 Azure Api、Azure 用戶端程式庫，以及資料箱 Blob 儲存體支援的工具版本。 資料箱 Blob 儲存體會使用與 Azure 一致的語意來提供 Blob 管理功能。 本文也會摘要說明 Azure 資料箱 Blob 儲存體與 Azure 儲存體服務間的已知差異。

建議您先仔細檢閱資訊，之後再連線到資料箱 Blob 儲存體，然後視需要回顧參考。


## <a name="storage-differences"></a>儲存體差異

|     功能                                             |     Azure 儲存體                                     |     資料箱 Blob 儲存體 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 檔案儲存體                                   |    支援雲端式 SMB 檔案共用              |    不支援      |
|    待用資料的服務加密                  |    256 位元 AES 加密                             |    256 位元 AES 加密 |
|    儲存體帳戶類型                                 |    一般用途和 Azure Blob 儲存體帳戶    |    僅限一般用途 v1|
|    Blob 名稱                                            |    1,024 個字元 (2,048 個位元組)                     |    880 個字元 (1,760 個位元組)|
|    區塊 Blob 大小上限                              |    4.75 TB (100 MB X 50,000 個區塊)                   |    4.75 TB (100 MB x 50000 區塊) 適用于 Azure 資料箱 v 3.0。|
|    分頁 Blob 大小上限                               |    8 TB                                               |    1 TB                   |
|    分頁 Blob 分頁大小                                  |    512 個位元組                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支援的 API 版本

資料箱 Blob 儲存體支援下列 Azure 儲存體服務 Api 版本。

### <a name="azure-data-box-30-onwards"></a>Azure 資料箱3。0

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>支援的 Azure 用戶端程式庫

針對資料箱 Blob 儲存體，有特定的用戶端程式庫以及特定的端點尾碼需求。 資料箱 Blob 儲存體端點與最新版的 Azure Blob 儲存體 REST API 沒有完全相同;請參閱 [Azure 資料箱3.0 的支援版本](#supported-api-versions)。 對於儲存體用戶端程式庫，您需要知道與 REST API 相容的版本。

### <a name="azure-data-box-30-onwards"></a>Azure 資料箱3。0

以下是針對資料箱 Blob 儲存體支援的 Azure 用戶端程式庫版本。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>透過編輯器安裝 PHP 用戶端 - 目前

若要透過編輯器安裝：(以 Blob 為例)。
1. 在專案的根目錄中，使用下列程式碼建立一個名為 composer.json 的檔案：

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. 將 `composer.phar` 下載至專案根目錄。

3. 執行：php composer.phar install。

### <a name="endpoint-declaration"></a>端點宣告

在資料箱 Blob 儲存體 SDK 中，端點尾碼- `<device serial number>.microsoftdatabox.com` -識別資料箱網域。 如需 blob 服務端點的詳細資訊，請移至透過 [資料箱 blob 儲存體連接](data-box-deploy-copy-data-via-rest.md)。
 
## <a name="examples"></a>範例

### <a name="net"></a>.NET

針對資料箱 Blob 儲存體，端點尾碼會指定於 `app.config` 檔案中：

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

針對資料箱 Blob 儲存體，端點尾碼會指定於連接字串的設定中：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

針對資料箱 Blob 儲存體，端點尾碼會指定於宣告執行個體中：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

針對資料箱 Blob 儲存體，端點尾碼會指定於連接字串的設定中：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

針對資料箱 Blob 儲存體，端點尾碼會指定於連接字串的設定中：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

針對資料箱 Blob 儲存體，端點尾碼會指定於宣告執行個體中：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

針對資料箱 Blob 儲存體，端點尾碼會指定於連接字串的設定中：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
