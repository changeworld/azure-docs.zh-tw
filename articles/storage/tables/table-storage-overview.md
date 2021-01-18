---
title: 資料表儲存體簡介 - Azure 中的物件儲存體 | Microsoft Docs
description: 使用 Azure 表格儲存體 (NoSQL 資料存放區) 將結構化的資料儲存在雲端。
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027735"
---
# <a name="what-is-azure-table-storage-"></a>什麼是 Azure 資料表儲存體？ 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure 表格儲存體是可將非關聯式結構化資料 (也稱為結構化 NoSQL 資料) 儲存在雲端中的服務，並提供具有無結構描述設計的索引鍵/屬性存放區。 由於表格儲存體並無結構描述，因此可輕易隨著應用程式發展需求改寫資料。 相較於類似資料量的傳統 SQL，對許多類型的應用程式而言，表格儲存體資料可快速存取且符合成本效益，通常可降低成本。

您可以使用表格儲存體來儲存具彈性的資料集，例如 Web 應用程式的使用者資料、通訊錄、裝置資訊，以及服務所需的其他中繼資料類型。 您可以在資料表中儲存任意數目的實體，且儲存體帳戶可包含任意數目的資料表，最高可達儲存體帳戶的容量限制。

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>後續步驟

* [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。

* [在 .NET 中開始使用 Azure 表格儲存體](../../cosmos-db/tutorial-develop-table-dotnet.md)

* 如需可用 API 的完整詳細資訊，請檢視資料表服務參考文件：

    * [Storage Client Library for .NET 參考資料](/dotnet/api/overview/azure/storage)

    * [REST API 參考資料](/rest/api/storageservices/)