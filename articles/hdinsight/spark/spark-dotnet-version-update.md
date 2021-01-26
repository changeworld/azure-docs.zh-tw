---
title: 將 Apache Spark 的 .NET 更新至 HDI 版的1.0 版
description: 瞭解如何在 HDI 中將 Apache Spark 版本的 .NET 更新為1.0，以及這對您現有的程式碼和叢集有何影響。
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788124"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>將 Apache Spark 的 .NET 更新至 HDInsight 中的1.0 版

本檔討論 Apache Spark 的第一個主要 [.net](https://github.com/dotnet/spark)版本，以及它可能會如何影響您目前在 HDInsight 叢集中的生產管線。

## <a name="about-net-for-apache-spark-version-100"></a>關於 Apache Spark 1.0.0 版的 .NET

這是第一個適用于 Apache Spark 的 .NET [主要正式發行版本](https://github.com/dotnet/spark/releases/tag/v1.0.0) ，並為 spark 2.4. x 和 spark 3.0. x 以及其他功能提供資料框架 API 完整性。 如需所有功能、增強功能和 bug 修正的完整清單，請參閱官方 [1.0.0 版的版本](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)資訊。
另一個值得注意的一點是，此版本與舊版和 **不** 相容 `Microsoft.Spark` `Microsoft.Spark.Worker` 。 如果您打算將 Apache Spark 應用程式的 .NET 升級為與1.0.0 版相容，請參閱 [遷移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 。

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>在 HDInsight 中使用 .NET 進行 Apache Spark v1。0

雖然目前的 HDI 叢集不會受到影響 (也就是它們仍具有與之前相同的版本) ，新建立的 HDI 叢集將會隨附此最新的1.0.0 版 .NET 以進行 Apache Spark。 這表示：

- **您有較舊的 HDI** 叢集：如果您想要將 Spark .net 應用程式升級至1.0.0 版 (建議) ，您必須更新 `Microsoft.Spark.Worker` HDI 叢集上的版本。 如需詳細資訊，請參閱在 [HDI 叢集上變更 Apache Spark 的 .net 版本一節](#changing-net-for-apache-spark-version-on-hdinsight)。
如果您不想要更新應用程式中 Apache Spark 的最新版本，則不需要採取進一步的步驟。  

- **您有新的 HDI** 叢集：如果您想要將 Spark .net 應用程式升級至1.0.0 版 (建議的) ，則不需要任何步驟來變更 HDI 上的背景工作角色，但您必須參考 [遷移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) ，以瞭解更新程式碼和管線所需的步驟。
如果您不想要變更應用程式中 Apache Spark 的目前版本，您必須將新叢集上的 HDI 叢集版本 (預設) 變更為您所使用的任何版本。 如需詳細資訊，請參閱在 [HDI 叢集上變更 Apache Spark 的 .net 版本一節](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)。  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>變更 HDInsight 上 Apache Spark 版本的 .NET

### <a name="deploy-microsoftsparkworker"></a>部署 Microsoft.Spark.Worker

`Microsoft.Spark.Worker` 是存在於 Spark 叢集的個別背景工作節點上的後端元件。 當您想要 (使用者定義函數) 執行 c # UDF 時，Spark 需要瞭解如何啟動 .NET CLR 以執行此 UDF。 `Microsoft.Spark.Worker` 提供可啟用這項功能的 Spark 類別集合。 根據您想要在 HDI 叢集上部署 Apache Spark 的 .NET 版本，選取背景工作版本。

1. 下載您特定版本的 Microsoft. 背景工作 Linux 版本。 例如，如果您想要的話 `.NET for Apache Spark v1.0.0` ，您可以下載 [netcoreapp 3.1. linux-x64-1.0.0. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)。  

2. 下載 [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) 腳本，將步驟1中下載的背景工作二進位檔安裝到 HDI 叢集的所有背景工作節點。  

3. 將上述檔案上傳至您的叢集可存取的 Azure 儲存體帳戶。 如需詳細資訊，請參閱 [APACHE SPARK HDI 部署一文的 .net](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) 。

4. `install-worker.sh`使用腳本動作，在叢集中的所有背景工作節點上執行腳本。 如需詳細資訊，請參閱 [APACHE SPARK HDI 部署一文的 .net](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) 。

### <a name="update-your-application-to-use-specific-version"></a>更新您的應用程式以使用特定版本

您可以在專案中選擇所需的 [Microsoft Spark NuGet 套件](https://www.nuget.org/packages/Microsoft.Spark/) 版本，以將 Apache Spark 應用程式的 .net 更新為使用特定版本。 如果選擇將您的應用程式更新為1.0.0 版，請務必查看特定版本的版本資訊和上述的 [遷移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 。

## <a name="faqs"></a>常見問題集

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>我的現有 HDI 叢集 < 1.0.0 版的新發行版本會開始失敗嗎？

現有的 HDI 叢集將會繼續擁有相同的舊版 for .NET 進行 Apache Spark，而且您現有的應用程式 (具有舊版 Spark .NET) 不會受到影響。

## <a name="next-steps"></a>後續步驟

[在 HDInsight 上部署 Apache Spark 應用程式的 .NET](/dotnet/spark/tutorials/hdinsight-deployment)