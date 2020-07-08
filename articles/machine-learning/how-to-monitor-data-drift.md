---
title: 偵測 AKS 部署上的資料漂移
titleSuffix: Azure Machine Learning
description: 偵測 Azure Machine Learning 中 Azure Kubernetes Service 部署模型的資料漂移（預覽）。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430288"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>偵測部署到 Azure Kubernetes Service 的模型上的資料漂移（預覽）（AKS）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將瞭解如何監視訓練資料集與已部署模型的推斷資料之間的資料漂移。 在機器學習環境中，定型的機器學習模型可能會因為漂移而遇到降低的預測效能。 使用 Azure Machine Learning，您可以監視資料漂移，而當偵測到漂移時，服務可以傳送電子郵件警示給您。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

在機器學習環境中，資料漂移是模型輸入資料的變更，會導致模型效能降低。 這是模型精確度隨著時間而下降的其中一個主要原因，因此監視資料漂移有助於偵測模型效能問題。 

## <a name="what-can-i-monitor"></a>我可以監視什麼？

使用 Azure Machine Learning，您可以監視 AKS 上部署之模型的輸入，並將此資料與模型的訓練資料集進行比較。 在固定間隔，推斷資料會進行[快照和分析](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)，然後針對基準資料集進行計算，以產生資料漂移分析，如下所示： 

+ 測量資料漂移的大小，稱為漂移係數。
+ 測量資料漂移比重（依特徵），指出哪些功能造成資料漂移。
+ 測量距離度量。 目前會計算 Wasserstein 和能源距離。
+ 測量功能的分佈。 目前的核心密度估計和長條圖。
+ 透過電子郵件將警示傳送至資料漂移。

> [!Note]
> 這項服務處於（預覽），且限制在設定選項中。 如需詳細資料和更新，請參閱我們的[API 檔](https://docs.microsoft.com/python/api/azureml-datadrift/)和[版本](azure-machine-learning-release-notes.md)資訊。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>如何在 Azure Machine Learning 中監視資料漂移

使用 Azure Machine Learning，資料漂移會透過資料集或部署進行監視。 若要監視資料漂移，基準資料集-通常會指定模型的訓練資料集。 第二個資料集（通常是從部署收集的模型輸入資料）會針對基準資料集進行測試。 這兩個資料集都會進行分析，並輸入至資料漂移監視服務。 機器學習模型已定型，可偵測這兩個資料集之間的差異。 模型的效能會轉換成漂移係數，測量兩個資料集之間的漂移程度。 使用[模型 interpretability](how-to-machine-learning-interpretability.md)時，會計算構成漂移係數的功能。 從資料集設定檔中，會追蹤每項功能的統計資訊。 

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 安裝 Azure Machine Learning SDK for Python。 請依照 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的指示執行下列工作：

    - 建立 Miniconda 環境
    - 安裝適用於 Python 的 Azure Machine Learning SDK

- [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 工作區[設定檔](how-to-configure-environment.md#workspace)。

- 使用下列命令安裝資料漂移 SDK：

    ```shell
    pip install azureml-datadrift
    ```

- 從模型的定型資料建立[資料集](how-to-create-register-datasets.md)。

- 在[註冊](concept-model-management-and-deployment.md)模型時指定訓練資料集。 下列範例示範如何使用 `datasets` 參數來指定訓練資料集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [啟用模型資料收集](how-to-enable-data-collection.md)，以從模型的 AKS 部署收集資料，並確認正在 blob 容器中收集資料 `modeldata` 。

## <a name="configure-data-drift"></a>設定資料漂移
若要設定實驗的資料漂移，請匯入相依性，如下列 Python 範例所示。 

這個範例會示範如何設定 [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 物件：

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

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 執行

`DataDriftDetector`設定物件之後，您可以針對模型提交在指定日期[執行的資料漂移](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-)。 在執行過程中，藉由設定參數來啟用 DataDriftDetector 警示 `drift_threshold` 。 如果[datadrift_coefficient](#visualize-drift-metrics)高於指定的 `drift_threshold` ，則會傳送電子郵件。

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

## <a name="visualize-drift-metrics"></a>視覺化漂移計量

<a name="metrics"></a>

在您提交 DataDriftDetector 回合之後，您可以在資料漂移工作的每次執行反復專案中查看儲存的漂移計量：


|計量|描述|
--|--|
wasserstein_distance|為一維數值散發定義的統計距離。|
energy_distance|為一維數值散發定義的統計距離。|
datadrift_coefficient|其計算方式類似于 Matthew 的相互關聯係數，但此輸出是介於0到1之間的實際數位。 在漂移的內容中，0表示沒有漂移，而1表示最大漂移。|
datadrift_contribution|功能對漂移的重要性。|

有多種方式可查看漂移計量：

* 使用 `RunDetails` [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)。
* [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)在任何執行物件上使用函數 `datadrift` 。
* 在[Azure Machine Learning studio](https://ml.azure.com)中，從工作區的 [**模型**] 區段中查看計量。

下列 Python 範例示範如何繪製相關的資料漂移計量。 您可以使用傳回的計量來建立自訂視覺效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![請參閱偵測到的資料漂移 Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>排程資料漂移掃描 

當您啟用資料漂移偵測時，會以指定的排程頻率來執行 DataDriftDetector。 如果 datadrift_coefficient 到達指定的 `drift_threshold` ，則會隨著每個排程執行傳送電子郵件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

您可以在[Azure Machine Learning studio](https://ml.azure.com)工作區中 [**詳細**資料] 索引標籤的 [**模型**] 底下，看到資料漂移偵測器的設定。

[![Azure Machine Learning studio 資料漂移](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>在 Azure Machine Learning studio 中查看結果

若要在[Azure Machine Learning studio](https://ml.azure.com)中的工作區中查看結果，請流覽至 [模型] 頁面。 在模型的 [詳細資料] 索引標籤上，會顯示資料漂移設定。 [**資料漂移**] 索引標籤現在可以視覺化資料漂移計量。 

[![Azure Machine Learning studio 資料漂移](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>接收漂移警示

藉由設定漂移係數警示閾值，並提供電子郵件地址，每當漂移係數高於臨界值時，就會自動傳送[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)電子郵件警示。 

為了讓您設定自訂警示和動作，所有資料漂移標準都會儲存在與 Azure Machine Learning 工作區一起建立的[Application Insights](how-to-enable-app-insights.md)資源中。 您可以遵循電子郵件警示中的連結，Application Insights 查詢。

![資料漂移電子郵件警示](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>在漂移後重新定型您的模型

當資料漂移對您已部署模型的效能造成負面影響時，就是重新定型模型的時候了。 若要這麼做，請繼續進行下列步驟。

* 調查收集的資料並準備資料，以將新模型定型。
* 將它分割成定型/測試資料。
* 使用新的資料重新定型模型。
* 評估新產生之模型的效能。
* 如果效能優於生產環境模型，請部署新的模型。

## <a name="next-steps"></a>後續步驟

* 如需使用資料漂移的完整範例，請參閱[AZURE ML 資料漂移筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)。 此 Jupyter Notebook 示範如何使用[Azure 開放資料集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)來定型模型，以預測氣象、將其部署至 AKS，以及監視資料漂移。 

* 使用[資料集監視](how-to-monitor-datasets.md)偵測資料漂移。

* 當資料漂移移至正式運作時，我們會大幅感謝您的問題、意見或建議。 使用下方的 [產品意見反應] 按鈕！ 
