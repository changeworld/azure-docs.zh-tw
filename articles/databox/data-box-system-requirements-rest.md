---
title: Microsoft Azure 資料箱 Blob 儲存體需求 | Microsoft Docs
description: 了解針對 Azure 資料箱 Blob 儲存體所支援的 API、SDK 和用戶端程式庫版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "61436489"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure 資料箱 Blob 儲存體需求

本文列出資料箱 Blob 儲存體所支援的 Azure Api、Azure 用戶端程式庫和工具的版本。 資料箱 Blob 儲存體會使用與 Azure 一致的語意來提供 Blob 管理功能。 本文也會摘要說明 Azure 資料箱 Blob 儲存體與 Azure 儲存體服務間的已知差異。

建議您先仔細檢閱資訊，之後再連線到資料箱 Blob 儲存體，然後視需要回顧參考。


## <a name="storage-differences"></a>儲存體差異

|     功能                                             |     Azure 儲存體                                     |     資料箱 Blob 儲存體 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 檔案儲存體                                   |    支援雲端式 SMB 檔案共用              |    不支援      |
|    待用資料的服務加密                  |    256 位元 AES 加密                             |    256 位元 AES 加密 |
|    儲存體帳戶類型                                 |    一般用途和 Azure Blob 儲存體帳戶    |    僅限一般用途 v1|
|    Blob 名稱                                            |    1,024 個字元 (2,048 個位元組)                     |    880 個字元 (1,760 個位元組)|
|    區塊 Blob 大小上限                              |    4.75 TB (100 MB X 50,000 個區塊)                   |    適用於 Azure 資料箱 1.8 版和更新版本的 4.75 TB (100 MB X 50,000 個區塊)。|
|    分頁 Blob 大小上限                               |    8 TB                                               |    1 TB                   |
|    分頁 Blob 分頁大小                                  |    512 個位元組                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支援的 API 版本

資料箱 Blob 儲存體支援下列版本的 Azure 儲存體服務 API：

Azure 資料箱 1.8 和更新版本

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>支援的 Azure 用戶端程式庫

針對資料箱 Blob 儲存體，有特定的用戶端程式庫以及特定的端點尾碼需求。 資料箱 Blob 端點與最新版的 Azure Blob 儲存體 REST API 並非完全相同，請參閱 [Azure 資料箱 1.8 和更新版本支援的版本](#supported-api-versions)。 對於儲存體用戶端程式庫，您需要知道與 REST API 相容的版本。

### <a name="azure-data-box-18-onwards"></a>Azure 資料箱 1.8 和更新版本

| 用戶端程式庫     |資料箱 Blob 儲存體支援的版本     | 連結   |     端點規格      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget 封裝：https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub 版本：https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config 檔案                 |
|    Java                |    7.0.0                                           |    Maven 封裝：https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub 版本：https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    連接字串設定         |
|    Node.js             |    2.8.3                                           |    NPM 連結： https://www.npmjs.com/package/azure-storage （執行： `npm install azure-storage@2.7.0` ）   <br>GitHub 版本：https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    服務執行個體宣告    |
|    C++                 |    5.2.0                                           |    Nuget 封裝：https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub 版本：https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    連接字串設定         |
|    PHP                 |    1.2.0                                           |    GitHub 版本：<br>一般：https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob：https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>透過編輯器安裝 (若要深入了解，請參閱下面的詳細資料)。                                                                                                             |    連接字串設定         |
|    Python              |    1.1.0                                           |    GitHub 版本：<br>一般：https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob：https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    服務執行個體宣告    |
|    Ruby                |    1.0.1                                           |    RubyGems 套件：<br>一般：https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob：https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub 版本：<br>一般：https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob：https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    連接字串設定         |



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

Azure 資料箱 Blob 儲存體端點包含兩個部分：區域的名稱和資料箱網域。 在資料箱 Blob 儲存體 SDK 中，預設端點是 `\<serial no. of the device>.microsoftdatabox.com` 。  如需有關 Blob 服務端點的詳細資訊，請參閱[透過資料箱 Blob 儲存體連線](data-box-deploy-copy-data-via-rest.md)。
 
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
