---
title: '沒有程式碼部署 (預覽) '
titleSuffix: Azure Machine Learning
description: 無程式碼部署可讓您將模型部署為 web 服務，而不需要手動建立輸入腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324921"
---
# <a name="preview-no-code-model-deployment"></a> (Preview) 無程式碼模型部署

無程式碼模型部署目前為預覽狀態，並支援下列機器學習架構：

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel 格式
TensorFlow 模型必須以 **SavedModel 格式** 註冊，才能使用無程式碼模型部署。

如需有關如何建立 SavedModel 的詳細資訊，請參閱 [此連結](https://www.tensorflow.org/guide/saved_model) 。

我們支援在 [TensorFlow 服務 DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags)的 [標記] 底下列出的任何 TensorFlow 版本。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX 模型

任何 ONNX 推斷圖形都支援 ONNX 模型註冊和部署。 目前不支援前置處理和 postprocess 步驟。

以下是如何註冊和部署 MNIST ONNX 模型的範例：

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

若要對模型進行評分，請參閱 [使用部署為 web 服務的 Azure Machine Learning 模型](./how-to-consume-web-service.md)。 許多 ONNX 專案都會使用 protobuf 檔來簡潔地商店定型和驗證資料，而這可能會讓您難以知道服務所預期的資料格式。 作為模型開發人員，您應該為開發人員記錄：

*  (JSON 或二進位) 的輸入格式
* 輸入資料圖形和類型 (例如，圖形 [100100，3] 的浮動陣列 ) 
* 網域資訊 (例如，影像、色彩空間、元件順序，以及值是否正規化) 

如果您使用的是 Pytorch，則將 [模型從 Pytorch 匯出至 ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) ，會有轉換和限制的詳細資料。 

## <a name="scikit-learn-models"></a>Scikit-learn 模型

所有內建 scikit-learn 學習模型類型都不支援任何程式碼模型部署。

以下範例示範如何註冊及部署 sklearn 模型，而不需要額外的程式碼：

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> 支援 predict_proba 的模型預設會使用該方法。 若要覆寫此使用預測，您可以修改 POST 主體，如下所示：

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> 這些相依性包含在預先建立的 scikit-learn-學習推斷容器中：

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)