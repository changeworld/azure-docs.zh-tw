---
title: HDInsight 中的 Azure Data Lake Storage Gen1 總覽
description: HDInsight 中的 Data Lake Storage Gen1 總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187240"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight 中的 Azure Data Lake Storage Gen1 總覽

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 您可以使用 Azure Data Lake 來捕捉任何大小、類型和內嵌速度的資料。 和在一個位置進行操作和探索分析。

使用與 WebHDFS 相容的 REST API，從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 Data Lake Storage Gen1 專為預存資料分析而設計，並針對資料分析案例效能而調整。 Gen1 包含真實企業使用案例不可或缺的功能。 這些功能包括安全性、管理性、適應性、可靠性和可用性。

如需有關 Azure Data Lake Storage Gen1 的詳細資訊，請參閱詳細的 [Azure Data Lake Storage Gen1 概觀](../data-lake-store/data-lake-store-overview.md)。

Data Lake Storage Gen1 的重要功能包括下列項目。

## <a name="compatibility-with-hadoop"></a>與 Hadoop 相容

Data Lake Storage Gen1 是與 HDFS 和 Hadoop 環境相容的 Apache Hadoop 檔案系統。  使用 WebHDFS API 的 HDInsight 應用程式或服務可以輕鬆地與 Data Lake Storage Gen1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

您可以使用 Hadoop 分析架構輕鬆地分析儲存在 Data Lake Storage Gen1 中的資料。 MapReduce 或 Hive 之類的架構。 您可以佈建並設定 Azure HDInsight 叢集，以直接存取 Data Lake Storage Gen1 中儲存的資料。

## <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

Data Lake Storage Gen1 提供無限制的儲存空間，適合用來儲存不同種類的分析資料。 它不會限制帳戶大小或檔案大小。 或可儲存在 data lake 中的資料量。 個別檔案的大小範圍是從 kb 到 pb，因此 Data Lake Storage Gen1 儲存任何類型資料的絕佳選擇。 建立多個複本以永久儲存資料。 而且資料可以儲存在 data lake 中的時間長度沒有限制。

## <a name="performance-tuning-for-big-data-analytics"></a>針對巨量資料分析調整效能

Data Lake Storage Gen1 是針對分析系統所設計。 需要大量輸送量來查詢和分析大量資料的系統。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 當您分析資料時，此設定可改善平行讀取檔案時的讀取輸送量。

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Enterprise 的準備就緒：高可用性和安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 資料資產可長期儲存：備援複本可防範任何非預期的失敗。 企業可以在其解決方案中使用 Data Lake Storage Gen1，以成為其現有資料平台的重要部分。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#data-security-in-data-lake-storage-gen1)。

## <a name="flexible-data-structures"></a>彈性資料結構

Data Lake Storage Gen1 能以其原生格式 (原樣) 儲存任何資料，而不需要先前轉換。 載入資料之前，Data Lake Storage Gen1 不需要定義結構描述。 個別的分析架構可解譯資料，並且在分析階段定義結構描述。 Data Lake Storage Gen1 可以處理結構化資料。 和半結構化，以及非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您可以使用 SDK、Azure 入口網站和 Azure PowerShell 來操作儲存的資料。 使用這些介面和容器將資料放入存放區中，可以儲存任何資料類型。 Data Lake Storage Gen1 不會根據資料類型來執行任何特殊的資料處理。

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 中的資料安全性

Data Lake Storage Gen1 會使用 Azure Active Directory 進行驗證，並使用存取控制清單 (ACL) 來管理對資料的存取。

| **功能** | **說明** |
| --- | --- |
| 驗證 |Data Lake Storage Gen1 整合了 Azure Active Directory (Azure AD)，可對 Data Lake Storage Gen1 中儲存的所有資料進行身分識別與存取管理。 由於整合的結果，Data Lake Storage Gen1 受惠於所有的 Azure AD 功能。 這些功能包括：多重要素驗證、條件式存取，以及角色型存取控制。 此外，應用程式使用方式監視、安全性監視和警示等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱[使用 Azure Active Directory 在 Azure Data Lake Storage Gen1 內驗證](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 ACL 可在根資料夾、子資料夾和個別檔案上啟用。 如需 ACL 如何在 Data Lake Storage Gen1 的環境中運作的詳細資訊，請參閱 [Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 也會為帳戶中儲存的資料提供加密功能。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](../data-lake-store/data-lake-store-encryption.md)。 如需如何提供加密相關設定的指示，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

若要深入了解如何在 Data Lake Storage Gen1 中保護資料，請參閱[保護儲存在 Azure Data Lake Storage Gen1 中的資料](../data-lake-store/data-lake-store-secure-data.md)。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>與 Data Lake Storage Gen1 相容的應用程式

Data Lake Storage Gen1 與 Hadoop 環境中的大部分開放原始碼元件相容。 此外，還與其他 Azure 服務完美整合。  請遵循下列連結，深入瞭解如何搭配開放原始碼元件和其他 Azure 服務使用 Data Lake Storage Gen1。

* 請參閱[可與 Azure Data Lake Storage Gen1 搭配使用的開放原始碼 big data 應用程式](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 請參閱[整合 Azure Data Lake Storage Gen1 與其他 Azure 服務](../data-lake-store/data-lake-store-integrate-with-other-services.md)，以了解如何使用 Data Lake Storage Gen1 搭配其他 Azure 服務，以促成更廣泛的案例。
* [如需 big Data 需求，請參閱使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md)。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 檔案系統 (adl://)

在 Hadoop 環境中，您可以透過新的檔案系統（AzureDataLakeFilesystem （adl://））存取 Data Lake Storage Gen1。 使用`adl://`的應用程式和服務效能，可以透過 WebHDFS 中目前無法使用的方式來優化。 如此一來，您就可以使用建議的 adl://，彈性地取得最佳效能。 或是繼續直接使用 WebHDFS API 來維護現有的程式碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 最佳效能。

使用下列 URI，在 Data Lake Storage Gen1 中存取您的資料：

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

如需有關如何在 Data Lake Storage Gen1 中存取資料的詳細資訊，請參閱[預存資料可用的動作](../data-lake-store/data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2 概觀](./overview-data-lake-storage-gen2.md)