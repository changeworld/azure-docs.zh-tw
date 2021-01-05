---
title: 使用 AutoML & Azure Databricks 進行開發
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 和 Azure Databricks 中設定開發環境。 使用適用于 Databricks 的 Azure ML Sdk 和搭配 AutoML 的 Databricks。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 878e6f11645a6478c0d536e9d6d6dac4518c5349
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740958"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>在 Azure Machine Learning 中設定具有 Azure Databricks 和 AutoML 的開發環境 

瞭解如何在使用 Azure Databricks 和自動化 ML 的 Azure Machine Learning 中設定開發環境。

Azure Databricks 適合在 Azure 雲端中可調整的 Apache Spark 平臺上執行大規模的機器學習工作流程。 它提供以 CPU 或 GPU 為基礎的計算叢集的共同作業筆記本型環境。

如需其他機器學習開發環境的詳細資訊，請參閱 [設定 Python 開發環境](how-to-configure-environment.md)。


## <a name="prerequisite"></a>必要條件

Azure Machine Learning 工作區。 如果您沒有帳戶，可以透過 [Azure 入口網站](how-to-manage-workspace.md)、 [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)和 [Azure Resource Manager 範本](how-to-create-workspace-template.md)建立 Azure Machine Learning 工作區。


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>使用 Azure Machine Learning 和 AutoML Azure Databricks

Azure Databricks 與 Azure Machine Learning 及其 AutoML 功能整合。 

您可以使用 Azure Databricks：

+ 使用 Spark MLlib 來定型模型，並將模型部署到 ACI/AKS。
+ 使用 Azure ML SDK 的 [自動化機器學習](concept-automated-ml.md) 功能。
+ 作為來自 [Azure Machine Learning 管線](concept-ml-pipelines.md)的計算目標。

## <a name="set-up-a-databricks-cluster"></a>設定 Databricks 叢集

建立 [Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)叢集。 只有當您在 Databricks 上安裝適用于自動化機器學習的 SDK 時，才適用某些設定。

**建立叢集需要幾分鐘的時間。**

使用這些設定：

| 設定 |適用於| 值 |
|----|---|---|
| 叢集名稱 |always| yourclustername |
| Databricks Runtime 版本 |always| 執行時間 7.1 (scala 2.21、spark 3.0.0) -非 ML|
| Python 版本 |always| 3 |
| 背景工作類型 <br> (決定並行反覆運算的最大數目)  |自動化 ML<br>向| 建議使用已記憶體最佳化的 VM |
| 背景工作角色 |always| 2 個以上 |
| 啟用自動調整 |自動化 ML<br>向| 取消選取 |

請靜候至叢集運作，再繼續操作。

## <a name="add-the-azure-ml-sdk-to-databricks"></a>將 Azure ML SDK 新增至 Databricks

叢集執行之後，請 [建立程式庫](https://docs.databricks.com/user-guide/libraries.html#create-a-library) 以將適當的 Azure Machine Learning SDK 套件附加至您的叢集。 

若要使用自動化 ML，請跳至使用 [AutoML 新增 AZURE ML SDK](#add-the-azure-ml-sdk-with-automl-to-databricks)。


1. 以滑鼠右鍵按一下您要儲存程式庫的目前工作區資料夾。 選取 [**建立** 連結  >  **庫**]。
    
    > [!TIP]
    > 如果您有舊的 SDK 版本，請從叢集的已安裝程式庫中取消選取它，並移至垃圾桶。 安裝新版 SDK，並重新啟動叢集。 如果重新開機後發生問題，請卸離並重新連接您的叢集。

1. 選擇下列選項 (不支援其他 SDK 安裝) 

   |SDK &nbsp; 套件 &nbsp; 額外專案|來源|PyPi &nbsp; 名稱&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |針對 Databricks| 上傳 Python Egg 或 PyPI | azureml-sdk[databricks]|

   > [!WARNING]
   > 不能安裝任何其他 SDK 額外專案。 只選擇 [ `databricks` ] 選項。

   * 請勿選取 [ **自動附加到所有** 叢集]。
   * 選取叢集名稱旁邊的 [  **附加** ]。

1. 監視錯誤直到 **附加** 狀態變更為止，這可能需要幾分鐘的時間。  如果此步驟失敗：

   請嘗試重新開機您的叢集：
   1. 在左窗格中，選取 [叢集]。
   1. 請選取表格中您的叢集名稱。
   1. 在 [程式庫] 索引標籤上，選取 [重新啟動]。

   成功的安裝看起來如下所示： 

  ![適用于 Databricks 的 Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>將具有 AutoML 的 Azure ML SDK 新增至 Databricks
如果叢集是使用 Databricks Runtime 7.1 或更新版本 (*不* 是 ML) 所建立，請在筆記本的第一個資料格中執行下列命令，以安裝 AML SDK。

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
針對 Databricks Runtime 7.0 和更低版本，請使用 [init 腳本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)來安裝 Azure Machine Learning SDK。

### <a name="automl-config-settings"></a>AutoML config 設定

在 AutoML config 中，使用 Azure Databricks 新增下列參數：

- ```max_concurrent_iterations``` 以叢集中的背景工作節點數目為基礎。
- ```spark_context=sc``` 以預設 spark 內容為基礎。

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>使用 Azure Databricks 的 ML 筆記本

現在就試試看：
+ 雖然有許多範例筆記本可供使用，但 **只有 [這些範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) 可搭配 Azure Databricks 運作。**

+ 直接從您的工作區匯入這些範例。 請參閱以下內容：選取匯入匯 ![ ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ 入面板](./media/how-to-configure-environment/azure-db-import.png)

+ 瞭解如何 [使用 Databricks 建立管線作為定型計算](how-to-create-your-first-pipeline.md)。

## <a name="troubleshooting"></a>疑難排解

* **安裝套件時失敗**

    安裝更多套件時，Azure Databricks Azure Machine Learning SDK 安裝會失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤，請凍結程式庫版本來安裝套件。 此問題與 Databricks 相關，而不是 Azure Machine Learning SDK。 您也可能會遇到其他程式庫的這個問題。 範例：
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    或者，如果您持續遇到 Python 程式庫的安裝問題，您可以使用 init 腳本。 未正式支援此方法。 如需詳細資訊，請參閱叢集 [範圍的初始化腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

* 匯 **入錯誤：無法匯入名稱 `Timedelta` `pandas._libs.tslibs`**：若您在使用自動化機器學習時看到此錯誤，請在您的筆記本中執行下列兩行：
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* 匯 **入錯誤：沒有名為 ' pandas ' 的模組**：如果您在使用自動化機器學習時看到此錯誤：

    1. 執行此命令，在您的 Azure Databricks 叢集中安裝兩個套件：
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 中斷連結，然後將叢集重新附加至您的筆記本。
    
    如果這些步驟無法解決問題，請嘗試重新開機叢集。

* **FailToSendFeather**：如果您在 `FailToSendFeather` Azure Databricks 叢集上讀取資料時看到錯誤，請參閱下列解決方案：
    
    * 將 `azureml-sdk[automl]` 套件升級為最新版本。
    * 新增 `azureml-dataprep` 1.1.8 版或更新版本。
    * 新增 `pyarrow` 0.11 版或更新版本。
  

## <a name="next-steps"></a>後續步驟

- 使用 MNIST 資料集將 Azure Machine Learning 上的[模型定型](tutorial-train-models-with-aml.md)。
- 請參閱 [適用于 Python 的 AZURE MACHINE LEARNING SDK 參考](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)。
