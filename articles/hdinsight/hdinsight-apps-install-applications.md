---
title: 在 Azure HDInsight 上安裝協力廠商應用程式
description: 瞭解如何在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366337"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式

瞭解如何在 Azure HDInsight 上安裝協力廠商[Apache Hadoop](https://hadoop.apache.org/)應用程式。 如需您自己的應用程式的安裝指示，請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)。

HDInsight 應用程式是使用者可以在 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。  

下列清單顯示已發佈的應用程式：

|Application |群集類型 | 描述 |
|---|---|---|
|[AtScale 智慧平臺](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale 將您的 HDInsight 群集轉換為橫向擴展的 OLAP 伺服器，允許您使用您已經知道、擁有和喜愛的 BI 工具（從 Microsoft Excel、Power BI、Tableau 軟體到 QlikView）以對話模式查詢數十億行資料。 |
|[HDInsight 的 CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP 是大資料的第一個統一集成平臺，可加快 Hadoop 的價值實現時間，並使 IT 部門能夠提供自助服務資料。 開放源碼和可擴展的 CDAP 消除了創新障礙。 要求：4 個區域節點，最小 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer 的自助服務可擴展平臺，用於準備、流覽和管理分析資料，從而加速將複雜的多來源資料轉化為有價值的業務就緒資訊，從而在企業規模上提供更快、更智慧的見解。 |
|[HDInsight 上的資料庫 DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |Dataiku DSS 處於企業資料科學平臺中，使資料科學家和資料分析師能夠協作，更高效地設計和運行新的資料產品和服務，將原始資料轉化為有影響力的預測。 |
|[萬迪斯可融合HDI應用程式](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |哈多普， 火花， HBase， 風暴， 卡夫卡 |在分散式環境中保持資料一致性是一項巨大的資料操作挑戰。 WANdisco Fusion 是一個企業級軟體平臺，通過跨任何環境實現非結構化資料一致性來解決此問題。 |
|[H2O 閃泡水，適用于 HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O 閃閃發光的水支援以下分散式演算法：GLM、Na_ve 貝葉斯、分散式隨機林、梯度提升機、深度神經網路、深度學習、K 表示、PCA、通用低等級模型、異常檢測、自動編碼器。 |
|[用於即時資料整合到 HDInsight 的 Striim](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |哈多普， HBase， 風暴， 火花， 卡夫卡 |Striim (讀為 "stream") 是端對端串流資料整合與智慧平台，可持續擷取、處理及分析不同的資料串流。 |
|[朱姆布恩企業-加速大資料分析](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |在高級別上，Jumbune 協助企業， 1. 加速 Tez，Map 減少& Spark 引擎的 Hive、JAVA、Scala 工作負載性能。 2. 主動 Hadoop 群集監視，3。 在分散式檔案系統上建立資料品質管理。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |哈多普，HBase，火花 |在 Apache Kylin 的驅動下，Kyligence 企業支援大資料上的 BI。 作為 Hadoop 上的企業 OLAP 引擎，Kyligence 企業使商務分析師能夠使用行業標準資料倉儲和 BI 方法在 Hadoop 上構建 BI。 |
|[Azure HDInsight 的星爆前置](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto 是一個快速且可擴展的分散式 SQL 查詢引擎。 Presto 專為分隔儲存體與計算所打造的架構，最適合用來查詢 Azure Data Lake Storage、Azure Blob 儲存體、SQL 與 NoSQL 資料庫以及其他資料來源中的資料。 |
|[面向 HDInsight 雲的流集資料收集器](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |哈多普， HBase， 火花， 卡夫卡 |StreamSet 資料收集器是一個羽量級、功能強大的引擎，用於即時資料流資料。 使用資料收集器路由和處理資料流程中的資料。 它配備了30天的試用許可證。 |
|[三法師絞車企業](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |哈多普， 火花， HBase |HDInsight 的三法公司支援企業範圍的資料爭用，用於任何規模的資料。 在 Azure 上運行 Trifacta 的成本是三法會訂閱成本加上虛擬機器的 Azure 基礎結構成本的組合。 |
|[Unifi 資料平臺](https://unifisoftware.com/platform/) |哈多普， HBase， 風暴， 火花 |Unifi 資料平臺是一套無縫集成的自助服務資料工具套件，旨在使企業用戶能夠應對資料挑戰，從而增加收入、降低成本或運營複雜性。 |
|[解散資料 APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |為 HDInsight Spark 群集分解資料應用。 |
|[水線 AI 驅動資料目錄](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |使用 AI 使用業務術語自動標記資料的水線編目、組織和管理資料。 Waterline 的業務素養目錄是自助服務分析、合規性和治理以及 IT 管理計畫的關鍵成功組成部分。 |

本文提供的指示將使用 Azure 入口網站。 您也可以從入口網站匯出 Azure Resource Manager 範本或從廠商取得 Resource Manage 範本的複本，然後使用 Azure PowerShell 與 Azure 傳統 CLI 來部署範本。  請參閱[使用 Resource Manager 範本在 HDInsight 上建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>Prerequisites
如果您想要在現有的 HDInsight 叢集上安裝 HDInsight 應用程式，您必須有 HDInsight 叢集。 若要建立叢集，請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。 您也可以在建立 HDInsight 叢集時安裝 HDInsight 應用程式。

## <a name="install-applications-to-existing-clusters"></a>將應用程式安裝到現有的叢集
下列程序示範如何將 HDInsight 應用程式安裝到現有的 HDInsight 叢集。

**安裝 HDInsight 應用程式**

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從左側功能表導航到**所有服務** > **分析** > **HDInsight 群集**。
3. 從清單中選擇 HDInsight 群集。  如果您沒有叢集，則必須先建立一個。  請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
4. 在 **"設置"** 類別下，選擇 **"應用程式**"。 您可以在主視窗中看到已安裝的應用程式的清單。 
   
    ![HDInsight 應用程式入口網站功能表](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 從功能表中選擇 **"添加**"。 您可以看到可用應用程式的清單。  如果 **_Add**已灰顯，則意味著此版本的 HDInsight 群集沒有應用程式。
   
    ![HDinsight 應用程式可用的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 選擇其中一個可用的應用程式，然後按照說明接受法律條款。

您可以從門戶通知中查看安裝狀態（選擇門戶頂部的鈴形圖示）。 安裝應用程式之後，應用程式會出現在 [已安裝的應用程式] 清單上。

## <a name="install-applications-during-cluster-creation"></a>在叢集建立期間安裝應用程式

您可以選擇在建立叢集時安裝 HDInsight 應用程式。 在此過程中，HDInsight 應用程式會在叢集建立並處於執行中狀態後安裝。 要使用 Azure 門戶在群集創建期間安裝應用程式，請從 **"配置 + 定價**"選項卡中選擇 **+添加應用程式**。

![Azure 門戶群集配置應用程式](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安裝的 HDInsight 應用程式和屬性
入口網站會顯示叢集的已安裝 HDInsight 應用程式清單，以及每個已安裝應用程式的屬性。

**列出 HDInsight 應用程式並顯示屬性**

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從左側功能表導航到**所有服務** > **分析** > **HDInsight 群集**。
3. 從清單中選擇 HDInsight 群集。
4. 在 **"設置"** 類別下，選擇 **"應用程式**"。 您可以在主視窗中看到已安裝的應用程式的清單。 
   
    ![HDinsight 應用程式安裝的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 選擇已安裝的應用程式之一以顯示該屬性。 此屬性會列出：

    |屬性 | 描述 |
    |---|---|
    |應用程式名稱 |應用程式名稱。 |
    |狀態 |應用程式狀態。 |
    |網頁 |您已部署到邊緣節點之 Web 應用程式的 URL。 此認證與您針對叢集設定的 HTTP 使用者認證相同。 |
    |SSH 終結點 |您可以使用 SSH 來連線到邊緣節點。 SSH 認證與您針對叢集設定的 SSH 使用者認證相同。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |描述 | 應用程式描述。 |

6. 若要刪除應用程式，請以滑鼠右鍵按一下應用程式，然後按一下操作功能表中的 [刪除]****。

## <a name="connect-to-the-edge-node"></a>連接到邊緣節點
您可以使用 HTTP 和 SSH 連接到邊緣節點。 您可以在 [入口網站](#list-installed-hdinsight-apps-and-properties)中找到端點資訊。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 端點認證是您已針對 HDInsight 叢集設定的 HTTP 使用者認證；SSH 端點認證就是您已針對 HDInsight 叢集設定的 SSH 認證。

## <a name="troubleshoot"></a>疑難排解
請參閱[針對安裝問題進行疑難排解](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>後續步驟
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰了解如何將自訂 HDInsight 應用程式發佈至 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
* [在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)︰了解如何使用空白邊緣節點來存取 HDInsight 叢集、測試 HDInsight 應用程式，以及裝載 HDInsight 應用程式。

