---
title: 什麼是 Azure 資料存儲第 1 代？ | Microsoft Docs
description: 資料湖存儲第 1 代（以前稱為 Azure 資料存儲）的概述，以及它在其他資料存儲上提供的值
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118794"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什麼是 Azure 資料存儲第 1 代？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Azure Data Lake 可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

使用與 WebHDFS 相容的 REST API，可以從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 它旨在啟用對存儲的資料的分析，並針對資料分析方案進行性能調整。 資料存儲單元 1 包括所有企業級功能：安全性、可管理性、可擴充性、可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>主要功能

Data Lake Storage Gen1 的一些重要功能包括下列項目。

### <a name="built-for-hadoop"></a>專為 Hadoop 而建置

資料存儲湖存儲 Gen1 是一個 Apache Hadoop 檔案系統，與 Hadoop 分散式檔案系統 （HDFS） 相容，並與 Hadoop 生態系統配合使用。 採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與 Data Lake Storage Gen 1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

您可以使用 Hadoop 分析框架（如 MapReduce 或 Hive）輕鬆分析資料存儲在資料存儲第 1 代中的資料。 您可以預配 Azure HDInsight 群集，並將其配置為直接存取資料存儲在資料存儲 Gen1 中的資料。

### <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

資料存儲庫 Gen1 提供無限的存儲，可以存儲各種資料進行分析。 它不對帳戶大小、檔案大小或可存儲在資料湖中的資料量施加任何限制。 單個檔的大小可以從千位元組到 PB 不等。 通過進行多個副本來持久存儲資料。 資料存儲在資料湖中的持續時間沒有限制。

### <a name="performance-tuned-for-big-data-analytics"></a>針對巨量資料分析調整效能

資料存儲庫 Gen1 專為運行需要大量輸送量以查詢和分析大量資料的大型分析系統而設計。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。

### <a name="enterprise-ready-highly-available-and-secure"></a>企業就緒：高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 您的資料資產可藉由製作備援複本來長期儲存，以防範任何未預期的失敗。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有資料

資料存儲湖存儲 Gen1 可以存儲任何資料的原生格式，而無需任何以前的轉換。 載入資料前，Data Lake Storage Gen1 不需要定義結構描述，而是留待個別的分析架構在分析時解譯資料及定義結構描述。 通過存儲任意大小和格式的檔，Data Lake 存儲 Gen1 能夠處理結構化、半結構化和非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 使用 SDK、Azure 門戶和 Azure 電源外殼對存儲的資料進行操作。 如果使用這些介面並使用適當的容器將資料放入存儲中，則可以存儲任何類型的資料。 Data Lake Storage Gen1 不會根據其儲存的資料類型來對資料執行任何特殊處理。

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>保護資料

資料存儲第 1 代使用 Azure 活動目錄 （Azure AD） 進行身份驗證，存取控制清單 （ACL） 管理對資料的訪問。

| 功能 | 描述 |
| --- | --- |
| 驗證 |資料存儲庫 Gen1 與 Azure AD 集成，用於對資料存儲在資料存儲第 1 代中的所有資料進行標識和訪問管理。 由於集成，Data Lake 存儲 Gen1 受益于所有 Azure AD 功能，如多重要素驗證、條件訪問、基於角色的存取控制、應用程式使用方式監視、安全監視和警報等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱[資料湖存儲第 1 代身份驗證](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 您可以在根資料夾、子資料夾和單個檔上啟用 ACL。 有關 ACL 在資料存儲第 1 代的上下文中如何工作的詳細資訊，請參閱[資料湖存儲 Gen1 中的存取控制](data-lake-store-access-control.md)。 |
| 加密 |資料存儲庫 Gen1 還為帳戶中存儲的資料提供加密。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](data-lake-store-encryption.md)。 有關如何提供加密相關配置的說明，請參閱[使用 Azure 門戶開始使用資料存儲第 1 代](data-lake-store-get-started-portal.md)。 |

如需有關如何在 Data Lake Storage Gen1 中保護資料的指示，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>應用程式相容性

Data Lake Storage Gen1 與 Hadoop 生態系統中的大部分開放原始碼元件相容。 它還與其他 Azure 服務很好地集成。 要瞭解有關如何使用資料湖存儲 Gen1 與開源元件和其他 Azure 服務有關，請使用以下連結：

- 如需可與 Data Lake Storage Gen1 互通的開放原始碼應用程式清單，請參閱[與 Azure Data Lake Storage Gen1 相容的應用程式與服務](data-lake-store-compatible-oss-other-applications.md)。
- 請參閱[與其他 Azure 服務集成](data-lake-store-integrate-with-other-services.md)，瞭解如何將資料存儲庫 Gen1 與其他 Azure 服務一起啟用更廣泛的方案。
- 請參閱[使用 Data Lake Storage Gen1 的案例](data-lake-store-data-scenarios.md)，以了解如何在擷取資料、處理資料、下載資料和視覺化資料等案例中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>資料存儲湖存儲第一代檔案系統

資料湖存儲第 1 代可通過檔案系統 AzureDataLakeFile 系統 （adl://）在 Hadoop 環境中訪問（可通過 HDInsight 群集提供）。 使用adl://的應用程式和服務可以利用 WebHDFS 中當前不可用的進一步性能優化。 因此，Data Lake 存儲 Gen1 使您能夠靈活地使用最佳性能，並推薦使用adl://，或者通過繼續直接使用 WebHDFS API 來維護現有代碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 的最佳效能。

您可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`，在 Data Lake Storage Gen1 中存取您的資料。 有關如何訪問資料存儲 Gen1 中的資料的詳細資訊，請參閱[查看存儲資料的屬性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 門戶開始使用資料存儲第 1 代](data-lake-store-get-started-portal.md)
- [使用 .NET SDK 開始使用資料存儲第 1 代](data-lake-store-get-started-net-sdk.md)
- [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)