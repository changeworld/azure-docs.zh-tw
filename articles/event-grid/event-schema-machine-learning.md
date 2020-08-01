---
title: 做為事件方格來源 Azure Machine Learning
description: 說明使用 Azure 事件方格為 Machine Learning 工作區事件提供的屬性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fb8cd76829622962b642580bbda7f2a655604c2f
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87458037"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>做為事件方格來源 Azure Machine Learning

本文提供機器學習服務工作區事件的屬性和架構。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure Machine Learning 會發出下列事件種類：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.machinelearningservices. ModelRegistered | 已成功註冊新的模型或模型版本時引發。 |
| Microsoft.machinelearningservices. ModelDeployed | 當模型已成功部署至端點時引發。 |
| Microsoft.machinelearningservices. RunCompleted | 成功完成執行時引發。 |
| Microsoft.machinelearningservices. DatasetDriftDetected | 資料集漂移監視器偵測到漂移時引發。 |
| Microsoft.machinelearningservices. RunStatusChanged | 當執行狀態變更時引發。 |

### <a name="the-contents-of-an-event-response"></a>事件回應的內容

觸發事件時，事件方格服務會將該事件的相關資料傳送至訂閱端點。

本節包含每個事件的資料外觀範例。

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.machinelearningservices. ModelRegistered 事件

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.machinelearningservices. ModelDeployed 事件

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.machinelearningservices. RunCompleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.machinelearningservices. DatasetDriftDetected 事件

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.machinelearningservices. RunStatusChanged 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunStatusChanged",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
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
   "runStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主旨的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 「事件方格」會定義最上層屬性的結構描述。 「事件方格」提供此值。 |

針對每個事件種類，資料物件都有下列屬性：

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.machinelearningservices. ModelRegistered

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| ModelName | 字串 | 已註冊的模型名稱。 |
| ModelVersion | 字串 | 已註冊的模型版本。 |
| ModelTags | object | 已註冊的模型標記。 |
| ModelProperties | object | 已註冊之模型的屬性。 |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.machinelearningservices. ModelDeployed

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| ServiceName | 字串 | 已部署之服務的名稱。 |
| ServiceComputeType | 字串 | 已部署之服務的計算類型（例如 ACI、AKS）。 |
  | ModelIds | 字串 | 以逗號分隔的模型識別碼清單。 部署在服務中的模型識別碼。 |
| ServiceTags | object | 已部署之服務的標記。 |
| ServiceProperties | object | 已部署之服務的屬性。 |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.machinelearningservices. RunCompleted

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| experimentId | 字串 | 執行所屬之實驗的識別碼。 |
| experimentName | 字串 | 執行所屬的實驗名稱。 |
| runId | 字串 | 已完成的執行識別碼。 |
| runType | 字串 | 已完成執行的執行類型。 |
| runTags | object | 已完成執行的標記。 |
| runProperties | object | 已完成執行的屬性。 |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.machinelearningservices. DatasetDriftDetected

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| DataDriftId | 字串 | 觸發事件的資料漂移監視器識別碼。 |
| DataDriftName | 字串 | 觸發事件的資料漂移監視名稱。 |
| RunId | 字串 | 偵測到資料漂移的執行識別碼。 |
| BaseDatasetId | 字串 | 用來偵測漂移的基底資料集識別碼。 |
| TargetDatasetId | 字串 | 用來偵測漂移的目標資料集識別碼。 |
| DriftCoefficient | double | 觸發事件的係數。 |
| StartTime | Datetime | 導致漂移偵測的目標資料集時間序列的開始時間。  |
| EndTime | Datetime | 導致漂移偵測的目標資料集時間序列的結束時間。 |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.machinelearningservices. RunStatusChanged

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| experimentId | 字串 | 執行所屬之實驗的識別碼。 |
| experimentName | 字串 | 執行所屬的實驗名稱。 |
| runId | 字串 | 已完成的執行識別碼。 |
| runType | 字串 | 已完成執行的執行類型。 |
| runTags | object | 已完成執行的標記。 |
| runProperties | object | 已完成執行的屬性。 |
| runStatus | 字串 | 執行的狀態。 |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
| 標題 | 說明 |
| ----- | ----- |
| [取用 Azure Machine Learning 事件](../machine-learning/how-to-use-event-grid.md) | 整合 Azure Machine Learning 與事件方格的總覽。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 如需有關建立 Azure 事件方格訂用帳戶的詳細資訊，請參閱[Event grid 訂](subscription-creation-schema.md)用帳戶架構
* 如需搭配使用 Azure 事件方格與 Azure Machine Learning 的簡介，請參閱取用[Azure Machine Learning 事件](../machine-learning/how-to-use-event-grid.md)
* 如需搭配使用 Azure 事件方格與 Azure Machine Learning 的範例，請參閱[建立事件驅動機器學習工作流程](../machine-learning/how-to-use-event-grid.md)
