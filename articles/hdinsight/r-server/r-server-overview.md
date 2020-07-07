---
title: Azure HDInsight 上的 ML 服務簡介
description: 了解如何使用 HDInsight 上的 ML 服務來建立適用於巨量資料分析的應用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a8d164dd50ac190d2bc14fea70cde20bfdb89361
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849915"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>什麼是 Azure HDInsight 中的 ML 服務

在 Azure 中建立 HDInsight 叢集時，可選擇 Microsoft Machine Learning Server 作為部署選項。 提供此選項的叢集類型稱為 **ML 服務**。 這項功能可讓您依需求存取 HDInsight 上的可調整、分散式的分析方法。

HDInsight 上的 ML 服務所提供的最新功能，適用於幾乎任何大小的資料集上所進行的 R 型分析， 且資料集可載入 Azure Blob 或 Data Lake 儲存體。 以 R 為基礎的應用程式可以使用 8000 個以上的開放原始碼 R 套件。 ScaleR 中的常式與 Microsoft 的巨量資料分析套件亦可供使用。

邊緣節點提供便利的地方，以便連接到叢集並執行 R 指令碼。 邊緣節點允許跨伺服器核心執行 ScaleR 平行處理的分散式函式。 您也可以使用 ScaleR 的 Hadoop Map Reduce，跨叢集的節點來執行這些函數， 或使用 Apache Spark 計算內容。

可以下載分析所產生的模型或預測，以便在內部部署使用。 您也可以在 Azure 中的其他地方使用 `operationalized`。 特別是透過 [Azure Machine Learning Studio (傳統)](https://studio.azureml.net)，以及 [Web 服務](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)。

## <a name="get-started-with-ml-services-on-hdinsight"></a>開始使用 HDInsight 上的 ML 服務

若要在 HDInsight 中建立 ML 服務叢集，請選取 [ML 服務]  叢集類型。 ML 服務叢集類型包含資料節點上的 ML 伺服器，以及邊緣節點。 邊緣節點作為 ML 服務型分析的登陸區域。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，以取得建立叢集的逐步指示。

## <a name="why-choose-ml-services-in-hdinsight"></a>為何選擇 HDInsight 中的 ML 服務？

HDInsight 中的 ML 服務提供下列優點︰

### <a name="ai-innovation-from-microsoft-and-open-source"></a>根據 Microsoft 和開放原始碼的 AI 創新

  ML 服務包含高度可調整的分散式演算法集，例如 [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)。 這些演算法可以處理大於實體記憶體大小的資料大小， 同時也會以分散的方式在各種不同的平台上執行。 深入了解產品隨附之 Microsoft 自訂 [R 套件](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)和 [Python 套件](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)的集合。
  
  ML 服務會將這些來自開放原始碼社群 (R、Python 及 AI 工具組) 的 Microsoft 創新和貢獻， 在單一企業等級平台上串連在一起。 任何 R 或 Python 開放原始碼機器學習套件，都可以與任何來自 Microsoft 的專屬創新搭配運作。

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>簡單、安全且高擴充性的運作和管理

  依賴傳統範例和環境的企業，會朝運作投入許多時間和精力。 此動作會導致成本和延遲擴大，包括：模型的平移時間、讓它們保持有效及最新狀態的反覆項目、法規核准，以及管理權限。

  ML 服務提供企業級[作業](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)。 在機器學習模型完成後，只需按幾下滑鼠即可產生 Web 服務 API。 這些 [Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)裝載於伺服器方格或雲端中，而且可以與企業營運應用程式整合。 部署到彈性方格的能力可讓您根據您的商務需求 (針對批次和即時評分) 順暢地進行調整。 如需指示，請參閱[讓 HDInsight 上的 ML 服務能運作](r-server-operationalize.md)。

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> HDInsight 上的 ML 服務叢集類型僅支援在 HDInsight 3.6 上使用。 HDInsight 3.6 已排定於 2020 年 12 月 31 日淘汰。

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight 上的 ML 服務主要功能

HDInsight 上的 ML 服務包含下列功能。

| 功能分類 | 描述 |
|------------------|-------------|
| 已啟用 R | [R 套件](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) 適用於以 R 撰寫的解決方案、具有 R 的開放原始碼散佈，以及用於執行指令碼的執行階段基礎結構。 |
| 已啟用 Python | [Python 套件](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) 適用於以 Python 撰寫的解決方案、具有 Python 的開放原始碼散佈，以及用於執行指令碼的執行階段基礎結構。
| [預先定型的模型](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 針對視覺化分析和文字情感分析，準備好要對您提供的資料進行評分。 |
| [部署和取用](r-server-operationalize.md) | 讓您的伺服器 `Operationalize`，並將解決方案部署為 Web 服務。 |
| [遠端執行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | 從用戶端工作站中，在您網路上的 ML 服務啟動遠端工作階段。 |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>HDInsight 上適用於 ML 服務的資料儲存體選項

HDFS 檔案系統的預設儲存體可以是 Azure 儲存體帳戶或 Azure Data Lake Storage。 在分析期間，會持續將資料上傳至叢集儲存體。 即使在刪除叢集之後，資料仍然可用。 各種工具都可以處理對儲存體的資料傳輸。 這些工具組括儲存體帳戶的入口網站型上傳工具，以及 AzCopy 公用程式。

您可以在叢集建立期間，啟用對其他 Blob 和 Data lake 儲存的存取， 不一定要採用使用中的主要儲存體選項。  若要深入了解如何使用多個儲存體帳戶，請參閱[適用於 HDInsight 上 ML 服務的 Azure 儲存體選項](./r-server-storage.md)文章。

您也可以將 Azure 檔案作為在邊緣節點上使用的儲存體選項。 Azure 檔案儲存體可讓您將建立於 Azure 儲存體的檔案分享至 Linux 檔案系統。 如需詳細資訊，請參閱 [HDInsight 上適用於 ML 服務的 Azure 儲存體選項](r-server-storage.md)。

## <a name="access-ml-services-edge-node"></a>存取 ML 服務邊緣節點

您可以使用瀏覽器或 SSH/PuTTY，連線到邊緣節點上的 Microsoft ML Server。 R 主控台預設會在叢集建立期間安裝。  

## <a name="develop-and-run-r-scripts"></a>開發和執行 R 指令碼

您的 R 指令碼可以使用 8000 個以上的開放原始碼 R 套件中的任何一個。 您也可以使用 ScaleR 程式庫中的平行化和分散式常式。 在邊緣節點上執行的指令碼會在該節點上的 R 解譯器內執行。 除了使用 Map 縮減Reduce (RxHadoopMR) 或 Spark (RxSpark) 計算內容呼叫 ScaleR 函式的步驟以外。 函式會以分散式方式在與資料相關聯的資料節點上執行。 如需內容選項的詳細資訊，請參閱[適用於 HDInsight 上 ML 服務的計算內容選項](r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>`Operationalize` 模型

完成資料模型化時，可以在 Azure 中或內部部署中 `operationalize` 模型，為新的資料進行預測。 這個程序稱為評分。 評分可在 HDInsight、Azure Machine Learning 或內部部署中完成。

### <a name="score-in-hdinsight"></a>在 HDInsight 中評分

若要在 HDInsight 中評分，請撰寫 R 函式。 函式可以針對已載入至儲存體帳戶的新資料檔案，呼叫模型以進行預測。 然後將預測存回儲存體帳戶。 您可以視需要在叢集的邊緣節點上執行這個常式，或使用排程作業來進行。

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning 中的評分 (AML)

若要使用 Azure Machine Learning 來評分，請使用 Azure Machine Learning R 套件開放原始碼 (稱為 [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/)) 來將您的模型發佈為 Azure Web 服務。 為了方便起見，此套件已預先安裝在邊緣節點。 接下來，使用 Azure Machine Learning 中的功能建立 Web 服務的使用者介面，然後呼叫評分所需的 Web 服務。 接著必須將 ScaleR 模型物件轉換成對等的開放原始碼模型物件，才可搭配 Web 服務使用。 針對此轉換，您可以使用 ScaleR 強制型轉函數 (例如，適用於集成模型的 `as.randomForest()`) 來完成。

### <a name="score-on-premises"></a>內部部署評分

若要在建立模型之後進行內部部署評分，可以在 R 中將模型序列化、進行下載並還原序列化，然後用來進行新資料的評分。 您可以使用稍早在在 HDInsight 中評分中所述的方法，或使用 [Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)進行新資料的評分。

## <a name="maintain-the-cluster"></a>維護叢集

### <a name="install-and-maintain-r-packages"></a>安裝及維護 R 套件

由於 R 指令碼大多數的步驟會在邊緣節點上執行，因此在邊緣節點上需要有大多數您所使用的 R 套件。 若要在邊緣節點上安裝其他 R 套件，可以在 R 中使用 `install.packages()` 方法。

如果您只是使用 ScaleR 程式庫常式，通常不需要額外的 R 套件。 您可能需要其他套件，才可在資料節點上使用 **rxExec** 或 **RxDataStep** 執行。

您可以在建立叢集之後，使用指令碼動作來安裝其他套件。 如需詳細資訊，請參閱[管理 HDInsight 叢集中的 ML 服務](r-server-hdinsight-manage.md)。

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>變更 Apache Hadoop MapReduce 記憶體設定

在執行 MapReduce 作業時，可以修改 ML 服務的可用記憶體。 若要修改叢集，請針對您的叢集使用 Apache Ambari UI。 如需使用 Ambari UI 的指示，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari.md)。

您可以在 **RxHadoopMR** 呼叫中使用 Hadoop 切換來變更 ML 服務的可用記憶體：

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>調整叢集的大小

您可以透過入口網站，相應增加或相應減少 HDInsight 上的現有 ML 服務叢集。 您可以透過擴大的方式，為較大型的處理工作取得額外的容量。 閒置時，您可以將叢集縮小。 如需如何調整叢集規模的指示，請參閱[管理 HDInsight 叢集](../hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>維護系統

離峰期間，系統會在 HDInsight 叢集的基礎 Linux VM 上執行 OS 維護。 一般而言，維護作業會在每星期一和星期四上午 3:30 (VM 的本機時間) 進行。 更新不會一次影響超過四分之一的叢集。

執行中的作業可能會在維護期間變慢。 不過，應該都可執行完成。 除非發生需要重建叢集的嚴重失敗，否則您擁有的任何自訂軟體或本機資料，在這些維護事件中皆會保留。

## <a name="ide-options-for-ml-services-on-hdinsight"></a>HDInsight 上適用於 ML 服務的 IDE 選項

HDInsight 叢集的 Linux 邊緣節點，是 R 型分析的登陸區域。 HDInsight 最近的幾個版本，在邊緣節點上提供 RStudio Server 的瀏覽器型 IDE。 在開發和執行方面，RStudio Server 的生產力高於 R 主控台。

桌面 IDE 可以透過遠端 MapReduce 或 Spark 計算內容來存取叢集。 選項包括：Microsoft 的 [Visual Studio R 工具](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS)、RStudio 與 Walware 的 Eclipse 型 StatET。

在命令提示字元中輸入 **R**，存取邊緣節點上的 R 主控台。 使用主控台介面時，可方便地在文字編輯器中開發 R 指令碼， 然後可視需要將指令碼的區段剪下並貼到 R 主控台。

## <a name="pricing"></a>定價

與 ML 服務 HDInsight 叢集相關聯的定價結構，類似於其他 HDInsight 叢集類型， 都是以各種名稱、資料和邊緣節點的基礎 VM 大小為基準， 搭配核心小時增加費。 如需詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 叢集上的 ML 服務，請參閱下列文章：

* [使用 RStudio Server 對 Azure HDInsight 中的 ML 服務叢集執行 R 指令碼](machine-learning-services-quickstart-job-rstudio.md)
* [在 HDInsight 上計算 ML 服務叢集的內容選項](r-server-compute-contexts.md)
* [HDInsight 上適用於 ML 服務叢集的儲存體選項](r-server-storage.md)
