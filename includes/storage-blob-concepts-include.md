---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cb4cac18f887a72c05fe520d6103dc00399cc05e
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828778"
---
Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。

## <a name="about-blob-storage"></a>關於 Blob 儲存體

Blob 儲存體設計用來：

* 直接提供映像或文件給瀏覽器。
* 儲存檔案供分散式存取。
* 串流傳輸視訊和音訊。
* 寫入記錄檔。
* 儲存備份和還原、災害復原和封存資料。
* 儲存資料供內部部署或 Azure 託管服務進行分析。

使用者或用戶端應用程式可以從世界各地透過 HTTP/HTTPS 存取 Blob 儲存體中的物件。 Blob 儲存體中的物件可透過 [Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 儲存體用戶端程式庫存取。 用戶端程式庫適用於不同的語言，包括：

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>有關 Azure Data Lake Storage Gen2

Blob 儲存體支援 Azure Data Lake Storage Gen2，這是適用於雲端的 Microsoft 企業巨量資料分析解決方案。 Azure Data Lake Storage Gen2 會提供階層式檔案系統，以及 Blob 儲存體的優點，包括：

* 低成本的分層式儲存體
* 高可用性
* 強式一致性
* 災害復原功能

如需 Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../articles/storage/data-lake-storage/introduction.md)。
