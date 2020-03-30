---
title: 使用 Windows PC 搭配 Hadoop on HDInsight - Azure
description: 從 Hadoop on HDInsight 中的 Windows PC 作業。 使用 PowerShell、Visual Studio 和 Linux 工具來管理和查詢叢集。 使用 .NET 開發巨量資料解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933942"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>從 Windows 電腦在 HDInsight 上的 Apache Hadoop 生態系統中作業

了解 Windows 電腦上的開發和管理選項，以便在 HDInsight 上的 Apache Hadoop 生態系統中作業。

HDInsight 是以 Apache Hadoop 和 Hadoop 元件為基礎，採用在 Linux 上開發的開放原始碼技術。 HDInsight 3.4 版及更新版本會使用 Ubuntu Linux 發行版本作為叢集的基礎作業系統。 不過，您可以從 Windows 用戶端或 Windows 開發環境使用 HDInsight。

## <a name="use-powershell-for-deployment-and-management-tasks"></a>使用 PowerShell 進行部署和管理工作

Azure PowerShell 是一種指令碼環境，可讓您從 Windows 在 HDInsight 中用來控制及自動執行部署和管理工作。

您可以使用 PowerShell 執行的工作範例︰

* [使用 PowerShell 創建群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)。
* [使用 PowerShell 運行 Apache Hive 查詢](hadoop/apache-hadoop-use-hive-powershell.md)。
* [使用 PowerShell 管理群集](hdinsight-administer-use-powershell.md)。

請遵循步驟來[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 以取得最新的版本。

## <a name="utilities-you-can-run-in-a-browser"></a>您可以在瀏覽器中執行的公用程式

下列公用程式具有可在瀏覽器中執行的 Web UI：
* **[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview)** 是一個互動式命令列外殼，在瀏覽器中運行，在 Azure 門戶中運行。

* **[Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** 是 Azure 入口網站中可用的管理和監視公用程式，可用來管理不同種類的作業，例如︰
    * [使用 Apache Ambari 搭配 REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Ambari 中的 Apache Hive 檢視](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Ambari 中的 Apache Tez 檢視](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Tools for Visual Studio

使用 Data Lake Tools for Visual Studio 來部署和管理 Storm 拓撲。 Data Lake Tools 也會安裝 SCP.NET SDK，其可讓您使用 Visual Studio 開發 C# Storm 拓撲。

在您進行下列範例之前，請[安裝並嘗試 Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

您可以使用 Visual Studio 和 Data Lake Tools for Visual Studio 執行的工作範例︰
* [從 Visual Studio 部署和管理 Storm 拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)
* [使用 Visual Studio 開發 Storm 的 C# 拓撲](storm/apache-storm-develop-csharp-visual-studio-topology.md)。 其中包含您可以連接到資料庫 (例如 Azure Cosmos DB 和 SQL Database) 之 Storm 拓撲的範例範本。

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio 和 .NET SDK

您可以使用 Visual Studio 搭配 .NET SDK 來管理叢集和開發巨量資料應用程式。 您可以將其他 IDE 用於下列工作，但範例顯示在 Visual Studio 中。

您可以在 Visual Studio 中使用 .NET SDK 執行的工作範例︰
* [Azure HDInsight SDK . NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [使用 .NET SDK 運行 Apache Hive 查詢](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)。
* [使用C#使用者定義的函數與阿帕奇蜂巢和阿帕奇豬流在阿帕奇哈多普](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)。

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>適用於 Spark 叢集的 Intellij IDEA 和 Eclipse IDE

[Intellij IDEA](https://www.jetbrains.com/idea/download) 和 [Eclipse IDE](https://www.eclipse.org/downloads/) 都可以用來︰
* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式。
* 存取 Spark 叢集資源。
* 在本機開發並執行 Scala Spark 應用程式。

這些文章顯示如何︰
* Intellij IDEA：[使用用於 Intellij 外掛程式的 Azure 工具組和 Scala SDK 創建 Apache Spark 應用程式。](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE 或 Scala IDE：[創建 Apache Spark 應用程式和 Eclipse 的 Azure 工具組](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>適用於資料科學家的 Spark Notebook

HDInsight 中的 Apache Spark 叢集包含可與 Jupyter Notebook 搭配使用的 Apache Zeppelin Notebook 和核心。

* [了解如何使用 Apache Spark 叢集上的核心搭配 Jupyter Notebook 來測試 Spark 應用程式](spark/apache-spark-zeppelin-notebook.md)
* [了解如何使用 Apache Spark 叢集上的 Apache Zeppelin Notebook 來執行 Spark 作業](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>在 Windows 上執行以 Linux 為基礎的工具和技術

如果您遇到必須使用僅在 Linux 上可用的工具或技術的情況，請考慮以下選項：

* **在 Windows 10 上的 Ubuntu 上的 Bash**在 Windows 上提供了一個 Linux 子系統。 Bash 可讓您直接執行 Linux 公用程式，而不必維護專用的 Linux 安裝。 如需安裝步驟，請參閱 [Windows 10 適用於 Linux 的 Windows 子系統的安裝指南](https://docs.microsoft.com/windows/wsl/install-win10)。  其他 [Unix 殼層](https://www.gnu.org/software/bash/)也可正常運作。
* **Docker for Windows** 可供存取許多以 Linux 為基礎的工具，並可以直接從 Windows 執行。 例如，您可以使用 Docker 直接從 Windows 執行 Hive 適用的 Beeline 用戶端。 您也可以使用 Docker 來執行本機 Jupyter Notebook，並從遠端連線到 HDInsight 上的 Spark。 [開始使用 Docker for Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** 可讓您透過 SSH 連線，以圖形方式瀏覽叢集檔案系統。

## <a name="cross-platform-tools"></a>跨平臺工具

Azure 命令列介面 (CLI) 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。  有關詳細資訊，請參閱[Azure 命令列介面 （CLI）。](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

如果您不熟悉使用以 Linux 為基礎的叢集，請參閱下列文章︰
* [設定 Apache Hadoop、Apache Kafka、Apache Spark 或其他叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [Linux 上的 HDInsight 叢集祕訣](hdinsight-hadoop-linux-information.md)
