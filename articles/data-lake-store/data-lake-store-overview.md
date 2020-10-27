---
title: 什麼是 Azure Data Lake Storage Gen1？ | Microsoft Docs
description: 概述 Data Lake Storage Gen1 (以前稱為 Azure Data Lake Store)，以及其所提供其他資料存放區沒有的價值
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149246"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什麼是 Azure Data Lake Storage Gen1？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Azure Data Lake 可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

使用與 WebHDFS 相容的 REST API，可以從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 其為預存資料分析而設計，並針對資料分析案例而調整效能。 Data Lake Storage Gen1 包含所有企業級功能：安全性、管理性、延展性、可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>主要功能

Data Lake Storage Gen1 的一些重要功能包括下列項目。

### <a name="built-for-hadoop"></a>專為 Hadoop 而建置

Data Lake Storage Gen1 是與 Hadoop 分散式檔案系統 (HDFS) 相容的 Apache Hadoop 檔案系統，可搭配 Hadoop 生態系統使用。 採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與 Data Lake Storage Gen 1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

您可以使用 Hadoop 分析架構 (例如 MapReduce 或 Hive)，輕鬆地分析 Data Lake Storage Gen1 中儲存的資料。 您可以佈建並設定 Azure HDInsight 叢集，以直接存取 Data Lake Storage Gen1 中儲存的資料。

### <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

Data Lake Storage Gen1 提供無限制的儲存空間，並可儲存各種資料以供分析。 對於帳戶大小、檔案大小，或資料湖中可儲存的資料量，均無任何限制。 個別檔案的大小範圍可以從 KB 到 PB。 資料會藉由建立多個複本的方式來永久儲存。 資料可以儲存在資料湖中的持續時間沒有任何限制。

### <a name="performance-tuned-for-big-data-analytics"></a>針對巨量資料分析調整效能

Data Lake Storage Gen1 專為執行大型分析系統而建置，而此類系統需要龐大輸送量才能查詢及分析大量資料。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。

### <a name="enterprise-ready-highly-available-and-secure"></a>符合企業需求：高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 您的資料資產可藉由製作備援複本來長期儲存，以防範任何未預期的失敗。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有資料

Data Lake Storage Gen1 能以其原生格式儲存任何資料，而不需要事先轉換。 載入資料前，Data Lake Storage Gen1 不需要定義結構描述，而是留待個別的分析架構在分析時解譯資料及定義結構描述。 Data Lake Storage Gen1 能夠儲存任意大小與格式的檔案，因此可以處理結構化、半結構化與非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您可以使用 SDK、Azure 入口網站和 Azure PowerShell 來操作儲存的資料。 如果您使用這些介面和適當容器將資料放入存放區中，就可以儲存任何類型的資料。 Data Lake Storage Gen1 不會根據其儲存的資料類型來對資料執行任何特殊處理。

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>保護資料

Data Lake Storage Gen1 會使用 Azure Active Directory (Azure AD) 進行驗證，並使用存取控制清單 (ACL) 來管理對資料的存取。

| 特徵 | 描述 |
| --- | --- |
| 驗證 |Data Lake Storage Gen1 會與 Azure AD 整合，可對 Data Lake Storage Gen1 中儲存的所有資料進行身分識別與存取管理。 由於整合的結果，Data Lake Storage Gen1 受惠於所有 Azure AD 功能，例如多重要素驗證、條件式存取、Azure 角色型存取控制、應用程式使用情況監視、安全性監視與警示等等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱 [Data Lake Storage Gen1 驗證](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 您可以在根資料夾、子資料夾和個別檔案上啟用 ACL。 如需 ACL 如何在 Data Lake Storage Gen1 的環境中運作的詳細資訊，請參閱 [Data Lake Storage Gen1 中的存取控制](data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 也會為帳戶中儲存的資料提供加密功能。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](data-lake-store-encryption.md)。 如需如何提供加密相關設定的指示，請參閱[使用 Azure 入口網站開始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)。 |

如需有關如何在 Data Lake Storage Gen1 中保護資料的指示，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>應用程式相容性

Data Lake Storage Gen1 與 Hadoop 生態系統中的大部分開放原始碼元件相容。 此外，還與其他 Azure 服務完美整合。 若要深入了解如何搭配開放原始碼元件和其他 Azure 服務來使用 Data Lake Storage Gen1，請使用下列連結：

- 如需可與 Data Lake Storage Gen1 互通的開放原始碼應用程式清單，請參閱[與 Azure Data Lake Storage Gen1 相容的應用程式與服務](data-lake-store-compatible-oss-other-applications.md)。
- 請參閱[與其他 Azure 服務整合](data-lake-store-integrate-with-other-services.md)，以了解如何搭配使用 Data Lake Storage Gen1 與其他 Azure 服務來實現更廣泛的案例。
- 請參閱[使用 Data Lake Storage Gen1 的案例](data-lake-store-data-scenarios.md)，以了解如何在擷取資料、處理資料、下載資料和視覺化資料等案例中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 檔案系統

在 Hadoop 環境中可透過檔案系統 (AzureDataLakeFilesystem (adl://)) 存取 Data Lake Storage Gen1 (透過 HDInsight 叢集提供)。 使用 adl:// 的應用程式和服務能夠利用 WebHDFS 中目前無法使用的進一步效能最佳化。 因此，Data Lake Storage Gen1 讓您具有彈性：可透過使用 adl:// 的建議選項來利用最佳效能，或繼續直接使用 WebHDFS API 來維護現有的程式碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 的最佳效能。

您可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`，在 Data Lake Storage Gen1 中存取您的資料。 如需有關如何在 Data Lake Storage Gen1 中存取資料的詳細資訊，請參閱[檢視預存資料的屬性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站開始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
- [利用 .NET SDK 開始使用 Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
- [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)