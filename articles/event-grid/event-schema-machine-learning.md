---
title: Azure 機器學習作為事件網格源
description: 描述為使用 Azure 事件網格為機器學習工作區事件事件提供的屬性
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7d9af420c7e2b47d2aeb4a8bf42ee138a605b305
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393267"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Azure 機器學習作為事件網格源

本文提供了機器學習工作區事件的屬性和架構。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure 機器學習發出以下事件類型:

| 事件類型 | 描述 |
| ---------- | ----------- |
| 微軟.機器學習服務.模型註冊 | 成功註冊新的模型或模型版本時引發。 |
| 微軟.機器學習服務.模型部署 | 在模型成功部署到終結點時引發。 |
| 微軟.機器學習服務.運行完成 | 成功完成運行後引發。 |
| 微軟.機器學習服務.數據集漂移檢測 | 當數據集漂移監視器檢測到漂移時引發。 |
| 微軟.機器學習服務.運行狀態已更改 | 當運行狀態更改為"失敗"時引發。 |

### <a name="the-contents-of-an-event-response"></a>事件回應的內容

觸發事件時,事件網格服務會將有關該事件的數據發送到訂閱終結點。

本節包含該數據在每個事件的外觀示例。

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>微軟.機器學習服務.模型註冊事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>微軟.機器學習服務.模型部署事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>微軟.機器學習服務.運行完成事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>微軟.機器學習服務.數據集漂移檢測到的事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>微軟.機器學習服務.執行狀態更改事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有以下每個事件類型的屬性:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>微軟.機器學習服務.模型註冊

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| ModelName | 字串 | 已註冊的模型的名稱。 |
| 模型版本 | 字串 | 已註冊的模型的版本。 |
| 模型標記 | 物件 (object) | 已註冊的模型的標記。 |
| 模型屬性 | 物件 (object) | 已註冊的模型的屬性。 |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>微軟.機器學習服務.模型部署

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| ServiceName | 字串 | 已部署的服務的名稱。 |
| 服務計算類型 | 字串 | 已部署服務的計算類型(例如 ACI、AKS)。 |
  | 模型 Id | 字串 | 模型指示的逗號分隔清單。 在服務中部署的模型的代號。 |
| 服務標籤 | 物件 (object) | 已部署服務的標記。 |
| 服務屬性 | 物件 (object) | 已部署服務的屬性。 |

### <a name="microsoftmachinelearningservicesruncompleted"></a>微軟.機器學習服務.運行完成

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 實驗Id | 字串 | 運行所屬的實驗的 ID。 |
| 實驗名稱 | 字串 | 運行所屬的實驗的名稱。 |
| RunId | 字串 | 已完成的運行識別碼。 |
| 執行類型 | 字串 | 已完成運行的運行類型。 |
| 執行標記 | 物件 (object) | 已完成的"運行"的標記。 |
| 執行屬性 | 物件 (object) | 已完成的"運行"的屬性。 |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>微軟.機器學習服務.數據集漂移檢測

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 資料漂移代碼 | 字串 | 觸發事件的資料漂移監視器的 ID。 |
| 資料漂移名稱 | 字串 | 觸發事件的數據漂移監視器的名稱。 |
| RunId | 字串 | 檢測到資料漂移的 Run 的 ID。 |
| 基本資料集 Id | 字串 | 用於檢測漂移的基礎資料集的 ID。 |
| 目標資料集 Id | 字串 | 用於檢測漂移的目標資料集的 ID。 |
| 漂移係數 | double | 觸發事件的係數結果。 |
| StartTime | Datetime | 導致漂移檢測的目標數據集時間序列的開始時間。  |
| EndTime | Datetime | 導致漂移檢測的目標數據集時間序列的結束時間。 |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>微軟.機器學習服務.運行狀態已更改

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 實驗Id | 字串 | 運行所屬的實驗的 ID。 |
| 實驗名稱 | 字串 | 運行所屬的實驗的名稱。 |
| RunId | 字串 | 已完成的運行識別碼。 |
| 執行類型 | 字串 | 已完成運行的運行類型。 |
| 執行標記 | 物件 (object) | 已完成的"運行"的標記。 |
| 執行屬性 | 物件 (object) | 已完成的"運行"的屬性。 |
| 執行狀態 | 字串 | 運行的狀態。 |

## <a name="tutorials-and-how-tos"></a>教學和如何
| Title | 描述 |
| ----- | ----- |
| [使用 Azure 機器學習事件](../machine-learning/concept-event-grid-integration.md) | 將 Azure 機器學習與事件網格集成的概述。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 有關創建 Azure 事件網格訂閱的詳細資訊,請參閱[事件網格訂閱架構](subscription-creation-schema.md)
* 有關將 Azure 事件網格與 Azure 機器學習一起使用的簡介,請參閱[使用 Azure 機器學習事件](/azure/machine-learning/service/concept-event-grid-integration)
* 有關將 Azure 事件網格與 Azure 機器學習一起使用的範例,請參閱[創建事件驅動的機器學習工作流](/azure/machine-learning/service/how-to-use-event-grid)
