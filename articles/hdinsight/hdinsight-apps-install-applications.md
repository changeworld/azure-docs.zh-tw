---
title: 在 Azure HDInsight 上安裝協力廠商應用程式
description: 了解如何在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: e0d1f9ad99e1b64560321312a22f61f5a2ef3dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016031"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式

了解如何在 Azure HDInsight 上安裝協力廠商 [Apache Hadoop](https://hadoop.apache.org/) 應用程式。 如需您自己的應用程式的安裝指示，請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)。

HDInsight 應用程式是使用者可以在 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。  

下列清單顯示已發佈的應用程式：

|Application |叢集類型 | 描述 |
|---|---|---|
|[AtScale 情報平台](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale 會將您的 HDInsight 叢集轉換成向外延展的 OLAP 伺服器，讓您能夠以互動方式使用您已經知道、擁有和愛用的 BI 工具 (從 Microsoft Excel、Power BI、Tableau 軟體到 QlikView)，來查詢數十億列的資料。 |
|[適用於 HDInsight 的 CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP 是第一個適用於巨量資料的統一整合平台，可加速實現 Hadoop 的價值，並可讓 IT 提供自助資料。 開放原始碼並可延伸的 CDAP，能夠移除創新的障礙。 需求：4 個區域節點，至少 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer 是可靈活調整規模的自助式平台，用來準備、探索及掌管供分析使用的資料，加速將複雜的多來源資料轉變為有價值的商務就緒資訊，更快提供更聰明的企業級深入解析。 |
|[HDInsight 上的 Dataiku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |企業資料科學平台中的 Dataiku DSS，可讓資料科學家和資料分析師共同作業，以更有效率的方式設計和執行新的資料產品和服務，並將原始資料轉換成影響力預測。 |
|[WANdisco Fusion HDI 應用程式](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.wandisco-liveanalytics) |Hadoop、Spark、HBase、Storm、Kafka |在分散式環境中保持資料的一致性是資料作業的一大挑戰。 企業級軟體平台 WANdisco Fusion 能在任何環境中保持非結構化資料的一致性，解決這個問題。 |
|[適用於 HDInsight 的 H2O SparklingWater](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water 支援下列分散式演算法：GLM、貝氏 (Naïve Bayes)、分散式隨機樹系 (Distributed Random Forest)、梯度提升機 (Gradient Boosting Machine)、深度類神經網路 (Deep Neural Networks)、深度學習、K-Means、PCA、一般化低秩模型 (Generalized Low Rank Model)、異常偵測、自動編碼器 (Autoencoder)。 |
|[適用於將即時資料整合至 HDInsight 的 Striim](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop、HBase、Storm、Spark、Kafka |Striim (讀為 "stream") 是端對端串流資料整合與智慧平台，可持續擷取、處理及分析不同的資料串流。 |
|[加速企業巨量資料分析的 Jumbune](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |概括而言，Jumbune 能夠協助企業，因為它：1. 可加速 Tez、MapReduce 與以 Spark 引擎為基礎的 Hive、JAVA、Scala 工作負載效能。 2. 擁有主動式 Hadoop 叢集監視，3. 可在分散式檔案系統上建立資料品質管理。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop、HBase、Spark |Kyligence Enterprise 由 Apache Kylin 技術支援，能夠在巨量資料上進行 BI。 Kyligence Enterprise 是 Hadoop 上的企業 OLAP 引擎，可讓商業分析師透過採用業界標準的資料倉儲和 BI 方法，在 Hadoop 上設計 BI。 |
|[適用於 Azure HDInsight 的 Starburst Presto](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto 是快速且可調整規模的分散式 SQL 查詢引擎。 Presto 專為分隔儲存體與計算所打造的架構，最適合用來查詢 Azure Data Lake Storage、Azure Blob 儲存體、SQL 與 NoSQL 資料庫以及其他資料來源中的資料。 |
|[適用於 HDInsight Cloud 的 StreamSets Data Collector](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、HBase、Spark、Kafka |StreamSets Data Collector 是輕量型且功能強大的引擎，可即時串流資料。 使用 Data Collector 可路由和處理資料流中的資料。 隨附 30 天試用版授權。 |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop、Spark、HBase |適用於 HDInsight 的 Trifacta Wrangler Enterprise 支援將整個企業任何規模的資料進行資料整頓。 在 Azure 上執行 Trifacta 的成本，是 Trifacta 訂閱成本加上虛擬機器的 Azure 基礎結構成本的結合。 |
|[Unifi 資料平台](https://unifisoftware.com/platform/) |Hadoop、HBase、Storm、Spark |Unifi 資料平台是緊密整合的自助式資料工具套件，其設計可讓企業用戶解決資料難題，促進增加營收、降低成本或操作複雜度。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |適用於 HDInsight Spark 叢集的解決資料應用程式。 |
|[Waterline AI 導向資料目錄](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline 可使用 AI 歸類、整理及掌管資料，以商務詞彙自動標記資料。 Waterline 是自助式分析、合規性與治理以及 IT 管理計劃成功的重要環節。 |

本文提供的指示將使用 Azure 入口網站。 您也可以從入口網站匯出 Azure Resource Manager 範本或從廠商取得 Resource Manage 範本的複本，然後使用 Azure PowerShell 與 Azure 傳統 CLI 來部署範本。  請參閱[使用 Resource Manager 範本在 HDInsight 上建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>Prerequisites
如果您想要在現有的 HDInsight 叢集上安裝 HDInsight 應用程式，您必須有 HDInsight 叢集。 若要建立叢集，請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。 您也可以在建立 HDInsight 叢集時安裝 HDInsight 應用程式。

## <a name="install-applications-to-existing-clusters"></a>將應用程式安裝到現有的叢集
下列程序示範如何將 HDInsight 應用程式安裝到現有的 HDInsight 叢集。

**安裝 HDInsight 應用程式**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表，瀏覽至 [所有服務] > [分析] > [HDInsight 叢集]。
3. 從清單中選取 [HDInsight 叢集]。  如果您沒有叢集，則必須先建立一個。  請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
4. 在 [設定] 類別下方，選取 [應用程式]。 您可以在主視窗中看見已安裝應用程式清單。 
   
    ![HDInsight 應用程式入口網站功能表](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 從功能表中選取 [+新增]。 您可以看到可用應用程式清單。  如果 [+新增] 為灰色，則表示沒有任何應用程式適用於這個版本的 HDInsight 叢集。
   
    ![HDinsight 應用程式可用的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 選取其中一個可用的應用程式，並依照指示接受法律條款。

您可以從入口網站通知看到安裝狀態 (選取入口網站頂端的鈴鐺圖示)。 安裝應用程式之後，應用程式會出現在 [已安裝的應用程式] 清單上。

## <a name="install-applications-during-cluster-creation"></a>在叢集建立期間安裝應用程式

您可以選擇在建立叢集時安裝 HDInsight 應用程式。 在此過程中，HDInsight 應用程式會在叢集建立並處於執行中狀態後安裝。 若要在叢集建立期間使用 Azure 入口網站安裝應用程式，請從 [設定 + 定價] 索引標籤中，選取 [新增應用程式]。

![Azure 入口網站叢集設定應用程式](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安裝的 HDInsight 應用程式和屬性
入口網站會顯示叢集的已安裝 HDInsight 應用程式清單，以及每個已安裝應用程式的屬性。

**列出 HDInsight 應用程式並顯示屬性**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表，瀏覽至 [所有服務] > [分析] > [HDInsight 叢集]。
3. 從清單中選取 [HDInsight 叢集]。
4. 在 [設定] 類別下方，選取 [應用程式]。 您可以在主視窗中看見已安裝應用程式清單。 
   
    ![HDinsight 應用程式安裝的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 選取其中一個已安裝的應用程式，以顯示屬性。 此屬性會列出：

    |屬性 | 描述 |
    |---|---|
    |應用程式名稱 |應用程式名稱。 |
    |狀態 |應用程式狀態。 |
    |網頁 |您已部署到邊緣節點之 Web 應用程式的 URL。 此認證與您針對叢集設定的 HTTP 使用者認證相同。 |
    |SSH 端點 |您可以使用 SSH 來連線到邊緣節點。 SSH 認證與您針對叢集設定的 SSH 使用者認證相同。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |描述 | 應用程式描述。 |

6. 若要刪除應用程式，請以滑鼠右鍵按一下應用程式，然後按一下操作功能表中的 [刪除]。

## <a name="connect-to-the-edge-node"></a>連接到邊緣節點
您可以使用 HTTP 和 SSH 連接到邊緣節點。 您可以在 [入口網站](#list-installed-hdinsight-apps-and-properties)中找到端點資訊。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 端點認證是您已針對 HDInsight 叢集設定的 HTTP 使用者認證；SSH 端點認證就是您已針對 HDInsight 叢集設定的 SSH 認證。

## <a name="troubleshoot"></a>疑難排解
請參閱[針對安裝問題進行疑難排解](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>後續步驟
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)：了解如何將自訂 HDInsight 應用程式發佈到 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
* [在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)︰了解如何使用空白邊緣節點來存取 HDInsight 叢集、測試 HDInsight 應用程式，以及裝載 HDInsight 應用程式。

