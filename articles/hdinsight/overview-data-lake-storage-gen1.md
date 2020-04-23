---
title: HDInsight 中的 Azure 資料儲存第 1 代概述
description: HDInsight 中數據存儲第 1 代的概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873351"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight 中的 Azure 資料儲存第 1 代概述

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 使用 Azure 數據湖,可以捕捉任何大小、類型和引入速度的資料。 並在一個位置進行操作和探索性分析。

使用與 WebHDFS 相容的 REST API，從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 Data Lake Storage Gen1 專為預存資料分析而設計，並針對資料分析案例效能而調整。 Gen1 包括對實際企業用例至關重要的功能。 這些功能包括安全性、可管理性、適應性、可靠性和可用性。

如需有關 Azure Data Lake Storage Gen1 的詳細資訊，請參閱詳細的 [Azure Data Lake Storage Gen1 概觀](../data-lake-store/data-lake-store-overview.md)。

Data Lake Storage Gen1 的重要功能包括下列項目。

## <a name="compatibility-with-hadoop"></a>與 Hadoop 相容

數據儲存湖儲存第 1 代是一個 Apache Hadoop 檔案系統,相容 HDFS 和 Hadoop 環境。  使用 WebHDFS API 的 HDInsight 應用程式或服務可以輕鬆地與資料儲存第 1 代整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

使用 Hadoop 分析框架可以輕鬆地分析儲存在資料儲存庫第 1 代的數據。 框架,如映射減少或蜂巢。 您可以佈建並設定 Azure HDInsight 叢集，以直接存取 Data Lake Storage Gen1 中儲存的資料。

## <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

數據存儲庫 Gen1 提供無限的存儲,適用於存儲不同類型的數據進行分析。 它不對帳戶大小或檔大小施加限制。 或可存儲在數據湖中的數據量。 單個檔的大小範圍從千位元組到 PB,使資料儲存 Gen1 成為存儲任何類型的數據的絕佳選擇。 通過進行多個副本來持久存儲數據。 數據在數據湖中存儲的時間沒有限制。

## <a name="performance-tuning-for-big-data-analytics"></a>針對巨量資料分析調整效能

數據存儲第一代專為分析系統而設計。 需要大量輸送量來查詢和分析大量數據的系統。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 當您分析資料時，此設定可改善平行讀取檔案時的讀取輸送量。

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>企業準備:高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 資料資產可長期儲存：備援複本可防範任何非預期的失敗。 企業可以在其解決方案中使用 Data Lake Storage Gen1，以成為其現有資料平台的重要部分。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#data-security-in-data-lake-storage-gen1)。

## <a name="flexible-data-structures"></a>彈性資料結構

Data Lake Storage Gen1 能以其原生格式 (原樣) 儲存任何資料，而不需要先前轉換。 載入資料之前，Data Lake Storage Gen1 不需要定義結構描述。 個別的分析架構可解譯資料，並且在分析階段定義結構描述。 數據存儲第 1 代可以處理結構化數據。 和半結構化和非結構化數據。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您可以使用 SDK、Azure 入口網站和 Azure PowerShell 來操作儲存的資料。 使用這些介面和容器放入存儲的數據可以存儲任何數據類型。 數據存儲庫 Gen1 不會根據數據類型對數據進行任何特殊處理。

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 中的資料安全性

Data Lake Storage Gen1 會使用 Azure Active Directory 進行驗證，並使用存取控制清單 (ACL) 來管理對資料的存取。

| **功能** | **說明** |
| --- | --- |
| 驗證 |Data Lake Storage Gen1 整合了 Azure Active Directory (Azure AD)，可對 Data Lake Storage Gen1 中儲存的所有資料進行身分識別與存取管理。 由於整合的結果，Data Lake Storage Gen1 受惠於所有的 Azure AD 功能。 這些功能包括:多重身份驗證、條件訪問和基於角色的訪問控制。 此外,應用程式使用方式監視、安全監視和警報等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱[使用 Azure Active Directory 在 Azure Data Lake Storage Gen1 內驗證](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 ACL 可在根資料夾、子資料夾和個別檔案上啟用。 如需 ACL 如何在 Data Lake Storage Gen1 的環境中運作的詳細資訊，請參閱 [Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 也會為帳戶中儲存的資料提供加密功能。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](../data-lake-store/data-lake-store-encryption.md)。 如需如何提供加密相關設定的指示，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

若要深入了解如何在 Data Lake Storage Gen1 中保護資料，請參閱[保護儲存在 Azure Data Lake Storage Gen1 中的資料](../data-lake-store/data-lake-store-secure-data.md)。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>與 Data Lake Storage Gen1 相容的應用程式

數據存儲來源Gen1與Hadoop環境中的大多數開源元件相容。 此外，還與其他 Azure 服務完美整合。  請按照以下連結瞭解有關如何將數據存儲 Gen1 與開源元件和其他 Azure 服務一起使用。

* 請參考[Azure 資料的資料儲存 Gen1 的開源大數據應用程式](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 請參閱[整合 Azure Data Lake Storage Gen1 與其他 Azure 服務](../data-lake-store/data-lake-store-integrate-with-other-services.md)，以了解如何使用 Data Lake Storage Gen1 搭配其他 Azure 服務，以促成更廣泛的案例。
* 有關[大數據要求,請參閱使用 Azure 資料儲存第 1 代](../data-lake-store/data-lake-store-data-scenarios.md)。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 檔案系統 (adl://)

在 Hadoop 環境中,您可以通過新的檔案系統 AzureDataLakeFile 系統 (adl://)訪問 Data Lake 儲存第 1 代。 可以使用`adl://`的應用程式和服務的性能可以以 WebHDFS 中當前不可用的方式進行優化。 因此,您可以靈活地使用推薦的adl://來獲得最佳性能。 或者繼續直接使用 WebHDFS API 來維護現有代碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 最佳效能。

使用以下網址存取資料儲存第一代資料:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

如需有關如何在 Data Lake Storage Gen1 中存取資料的詳細資訊，請參閱[預存資料可用的動作](../data-lake-store/data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
* [Azure 資料儲存第 2 代概述](./overview-data-lake-storage-gen2.md)