---
title: 檢測 AKS 部署上的資料漂移
titleSuffix: Azure Machine Learning
description: 在 Azure 電腦機器學習中檢測 Azure 庫伯奈斯服務部署模型上的資料漂移（預覽）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537002"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>檢測部署到 Azure 庫伯奈斯服務 （AKS） 的模型上的資料漂移（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將瞭解如何監視訓練資料集和已部署模型的推理資料之間的資料漂移。 在機器學習環境中，經過訓練的機器學習模型可能會因為漂移而降低預測性能。 使用 Azure 機器學習，可以監視資料漂移，並且服務可以在檢測到漂移時向您發送電子郵件警報。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

在機器學習上下文中，資料漂移是模型輸入資料的變化，導致模型性能下降。 這是模型精度隨時間而降低的主要原因之一，因此監視資料漂移有助於檢測模型性能問題。 

## <a name="what-can-i-monitor"></a>我可以監控什麼？

使用 Azure 機器學習，可以監視部署在 AKS 上的模型的輸入，並將此資料與模型的訓練資料集進行比較。 每隔一段時間，對推理資料[進行快照和剖析](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)，然後根據基線資料集進行計算，以生成資料漂移分析，該分析： 

+ 測量資料漂移的大小，稱為漂移係數。
+ 按要素測量資料漂移貢獻，指示哪些要素導致資料漂移。
+ 測量距離指標。 目前，瓦瑟斯坦和能量距離的計算。
+ 測量要素的分佈。 目前內核密度估計和長條圖。
+ 通過電子郵件向資料漂移發送警報。

> [!Note]
> 此服務處於（預覽）狀態，並且在配置選項中受到限制。 有關詳細資訊和更新，請參閱我們的[API 文檔](https://docs.microsoft.com/python/api/azureml-datadrift/)和[版本資訊](azure-machine-learning-release-notes.md)。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>如何在 Azure 機器學習中監視資料漂移

使用 Azure 機器學習，資料漂移通過資料集或部署進行監視。 為了監視資料漂移，指定了基線資料集（通常是模型的訓練資料集）。 第二個資料集（通常是從部署中收集的模型輸入資料）根據基線資料集進行測試。 兩個資料集都進行剖查並輸入到資料漂移監視服務。 機器學習模型經過培訓，可檢測兩個資料集之間的差異。 模型的性能轉換為漂移係數，該係數測量兩個資料集之間的漂移量。 使用[模型可解釋性](how-to-machine-learning-interpretability.md)計算導致漂移係數的要素。 從資料集設定檔中，跟蹤有關每個要素的統計資訊。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果沒有，請先創建一個免費帳戶。" 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 安裝 Azure Machine Learning SDK for Python。 請依照 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的指示執行下列工作：

    - 建立 Miniconda 環境
    - 安裝適用於 Python 的 Azure Machine Learning SDK

- [Azure 機器學習工作區](how-to-manage-workspace.md)。

- 工作區[設定檔](how-to-configure-environment.md#workspace)。

- 使用以下命令安裝資料漂移 SDK：

    ```shell
    pip install azureml-datadrift
    ```

- 從模型的訓練資料創建[資料集](how-to-create-register-datasets.md)。

- [註冊](concept-model-management-and-deployment.md)模型時指定訓練資料集。 下面的示例演示使用 參數`datasets`指定訓練資料集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [啟用模型資料收集](how-to-enable-data-collection.md)，以便從模型的 AKS 部署收集資料，並確認正在`modeldata`Blob 容器中收集資料。

## <a name="configure-data-drift"></a>配置資料漂移
要配置實驗的資料漂移，請導入依賴項，如以下 Python 示例中所示。 

此示例演示配置[`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector)物件：

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>提交資料漂移檢測器運行

設定物件`DataDriftDetector`後，可以提交模型在給定日期[運行的資料漂移](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-)。 作為運行的一部分，通過設置`drift_threshold`參數啟用 DataDriftDetector 警報。 如果[datadrift_coefficient](#visualize-drift-metrics)高於給定`drift_threshold`的 ，則發送電子郵件。

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>視覺化漂移指標

<a name="metrics"></a>

提交 DataDriftDetector 運行後，您可以看到在資料漂移任務的每個運行反覆運算中保存的漂移指標：


|計量|描述|
--|--|
wasserstein_distance|為一維數值分佈定義的統計距離。|
energy_distance|為一維數值分佈定義的統計距離。|
datadrift_coefficient|計算方法與 Matthew 的相關係數相似，但此輸出是一個從 0 到 1 的實數。 在漂移上下文中，0 表示沒有漂移，1 表示最大漂移。|
datadrift_contribution|有助於漂移的要素的特徵重要性。|

查看漂移指標的方法有多種：

* `RunDetails`使用[朱皮特小部件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)。
* 在任何[`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)`datadrift`運行物件上使用函數。
* 在[Azure 機器學習工作室](https://ml.azure.com)中，從工作區的 **"模型"** 部分查看指標。

下面的 Python 示例演示如何繪製相關資料漂移指標。 您可以使用返回的指標生成自訂視覺化效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![查看 Azure 機器學習檢測到的資料漂移](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>計畫資料漂移掃描 

啟用資料漂移檢測時，資料漂移檢測器以指定的計畫頻率運行。 如果datadrift_coefficient到達給定`drift_threshold`的 ，則每次計畫運行都會發送電子郵件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

資料漂移檢測器的配置可以在[Azure 機器學習工作室](https://ml.azure.com)工作區中的 **"詳細資訊**"選項卡中的 **"模型"** 下看到。

[![Azure 機器學習工作室資料漂移](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>在 Azure 機器學習工作室中查看結果

要查看[Azure 機器學習工作室](https://ml.azure.com)工作區中的結果，請導航到模型頁面。 在模型的詳細資訊選項卡上，將顯示資料漂移配置。 **"資料漂移**"選項卡現在可用於視覺化資料漂移指標。 

[![Azure 機器學習工作室資料漂移](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>接收漂移警報

通過設置漂移係數警報閾值並提供電子郵件地址，每當漂移係數超過閾值時，Azure[監視器](https://docs.microsoft.com/azure/azure-monitor/overview)電子郵件警報將自動發送。 

為了設置自訂警報和操作，所有資料漂移指標都存儲在與 Azure 機器學習工作區一起創建[的應用程式見解](how-to-enable-app-insights.md)資源中。 您可以按照電子郵件警報中指向"應用程式見解"查詢的連結進行操作。

![資料漂移電子郵件警報](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>漂移後重新訓練模型

當資料漂移對已部署模型的性能產生負面影響時，是時候重新訓練模型了。 為此，請執行以下步驟。

* 調查收集的資料並準備資料以訓練新模型。
* 將其拆分為訓練/測試資料。
* 使用新資料再次訓練模型。
* 評估新生成的模型的性能。
* 如果性能優於生產模型，則部署新模型。

## <a name="next-steps"></a>後續步驟

* 有關使用資料漂移的完整示例，請參閱 Azure [ML 資料漂移筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)。 此 Jupyter 筆記本演示如何使用[Azure 開放資料集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)訓練模型以預測天氣、將其部署到 AKS 並監視資料漂移。 

* 使用[資料集監視器](how-to-monitor-datasets.md)檢測資料漂移。

* 當資料漂移轉向通用時，我們非常感謝您的問題、意見或建議。 使用下面的產品回饋按鈕！ 
