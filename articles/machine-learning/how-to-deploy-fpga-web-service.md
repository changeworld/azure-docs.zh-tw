---
title: 什麼是 FPGA-如何部署
titleSuffix: Azure Machine Learning
description: 瞭解如何使用在 FPGA 上執行的模型來部署 web 服務，以 Azure Machine Learning 進行 ultra 低延遲推斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: fd77c77abc8fe60eb18febde6fcc5bdffe6d7871
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538094"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>什麼是可現場程式化閘道陣列（FPGA）和如何部署
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文提供可現場程式化閘道陣列（FPGA）的簡介，並說明如何使用 Azure Machine Learning 將模型部署至 Azure FPGA。 

FPGA 包含可程式化邏輯區塊的陣列，以及可重新設定互連的階層。 互連可讓您在製造之後以不同方式設定這些區塊。 與其他晶片相比，FPGA 結合了可程式性和效能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Fpga 與 CPU、GPU 和 ASIC 的比較

下列圖表示範 FPGA 與其他處理器的比較方式。

![Azure Machine Learning FPGA 比較的圖表](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|處理器||說明|
|---|:-------:|------|
|應用程式特定的積體電路|ASIC|自訂電路 (例如，Google 的 TensorFlow 處理器 (TPU)) 可提供最高效率。 它們無法隨著您需求的變更加以重新設定。|
|現場可程式化閘陣列|FPGA|FPGA (例如 Azure 上所提供的那些陣列) 可提供接近 ASIC 的效能。 它們也會隨著時間而具有彈性且可重新設定，以實作新邏輯。|
|圖形處理器|GPU|這是頗受歡迎的 AI 計算選擇。 由於 GPU 能提供平行處理功能，因而使得它在轉譯影像的速度會比 CPU 還快。|
|中央處理器|CPU|一般用途的處理器，其效能不適合圖形和影片處理。|

Azure 上的 Fpga 是以 Intel 的 FPGA 裝置為基礎，而資料科學家和開發人員會使用它們來加速即時 AI 計算。 這個具有 FPGA 功能的架構可提供效能、彈性和擴展能力，並且可在 Azure 上使用。

Fpga 可以讓即時推斷（或模型評分）要求達到低延遲。 不需要非同步要求 (批次處理)。 批次處理可能會造成延遲 (因為需要處理更多資料)。 類神經處理單元的實現不需要批次處理;因此，相較于 CPU 和 GPU 處理器，延遲的時間可能會更低。

### <a name="reconfigurable-power"></a>可重新設定的電源
您可以針對不同類型的機器學習模型重新設定 FPGA。 擁有此種彈性，就更易於依據最佳的數值有效位數和所用的記憶體模型來加速應用程式。 由於 FPGA 可重新設定，因此您可以與 AI 演算法快速變化的需求保持同步。

## <a name="whats-supported-on-azure"></a>Azure 上所支援的項目
Microsoft Azure 是 FPGA 全球最大的雲端投資。 使用這個具有 FPGA 功能的硬體架構，已定型的類神經網路執行速度會更快，延遲也較低。 Azure 可以跨 Fpga 平行處理預先定型的深度類神經網路（DNN），以相應放大您的服務。 DNN 可以預先定型為深度 Featurizer 以傳輸學習，也可以使用更新過的加權來微調。

Azure 上的 Fpga 支援：

+ 影像分類和辨識案例
+ TensorFlow 部署
+ Intel FPGA 硬體 

這些 DNN 模型目前可供使用：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG-16

Fpga 可在下列 Azure 區域中使用：
  - 美國東部
  - 東南亞
  - 歐洲西部
  - 美國西部 2

> [!IMPORTANT]
> 若要將延遲和輸送量優化，將資料傳送至 FPGA 模型的用戶端應該位於上述其中一個區域（您已將模型部署至其中一個）。

**Azure vm 的 PBS 系列**包含 Intel Arria 10 fpga。 當您檢查 Azure 配額配置時，它會顯示為「標準的 PBS 系列個 vcpu」。 PB6 VM 有六個個 vcpu 和一個 FPGA，而且它會在將模型部署至 FPGA 時，自動由 Azure ML 布建。 此檔案僅適用于 Azure ML，而且無法執行任意 bitstreams。 例如，您將無法使用 bitstreams 來快閃 FPGA 來執行加密、編碼等動作。

### <a name="scenarios-and-applications"></a>案例和應用程式

Azure Fpga 會與 Azure Machine Learning 整合。 Microsoft 使用 FPGA 來評估 DNN、Bing 搜尋順位排序，以及軟體定義網路 (SDN) 加速來減少延遲，同時釋放 CPU 來執行其他工作。

下列案例使用 Fpga：
+ [自動化光學檢查系統](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [地表覆蓋對應](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>範例：在 Fpga 上部署模型 

您可以使用 Azure Machine Learning 硬體加速模型，在 Fpga 上將模型部署為 web 服務。 使用 Fpga 可提供超低延遲推斷，即使使用單一批次大小也一樣。 推斷（或模型計分）是已部署的模型用於預測的階段，最常見的是生產資料。


### <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。  如果您沒有，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- FPGA 配額。 使用 Azure CLI 檢查您是否有配額：

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 其他可能的位置是 ``southeastasia``、``westeurope``和 ``westus2``。

    命令會傳回類似下面的文字：

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    請確定您在__CurrentValue__下至少有6個個 vcpu。

    如果您沒有配額，請在[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)提交要求。

- 已安裝 Azure Machine Learning 工作區與適用於 Python 的 Azure Machine Learning SDK。 如需詳細資訊，請參閱[建立工作區](how-to-manage-workspace.md)。
 
- 適用于硬體加速模型的 Python SDK：

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. 建立和容器化模型

本檔將說明如何建立 TensorFlow 圖形來預先處理輸入影像、將它設為在 FPGA 上使用 ResNet 50 的 featurizer，然後透過針對 ImageNet 資料集定型的分類器來執行功能。

依照指示執行以：

* 定義 TensorFlow 模型
* 轉換模型
* 部署模型
* 取用已部署的模型
* 刪除已部署的服務

使用[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 來建立服務定義。 服務定義是根據 TensorFlow 來描述圖形管線 (輸入、功能化器和分類器) 的檔案。 部署命令會自動將定義和圖表壓縮為 ZIP 檔案，然後將 ZIP 檔案上傳到 Azure Blob 儲存體。 已部署 DNN 以在 FPGA 上執行。

### <a name="load-azure-machine-learning-workspace"></a>載入 Azure Machine Learning 工作區

載入您的 Azure Machine Learning 工作區。

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>預先處理映像

Web 服務的輸入是 JPEG 影像。  第一個步驟是將 JPEG 影像解碼並進行前置處理。  JPEG 影像會視為字串，而結果會 tensors，而這會是 ResNet 50 模型的輸入。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Load featurizer

初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。  您可以藉由匯入其他深度類神經網路，取代下列程式碼片段中的 "QuantizedResnet50"：

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>加入分類器

此分類器已在 ImageNet 資料集上定型。  您可以在[範例筆記本](https://aka.ms/aml-notebooks)集合中取得轉移學習和訓練自訂權數的範例。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>儲存模型

現在已載入預處理器、ResNet 50 featurizer 和分類器，請將圖形和關聯的變數儲存為模型。

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>儲存輸入和輸出 tensors
在前置處理和分類器步驟期間建立的輸入和輸出 tensors，將需要進行模型轉換和推斷。

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 儲存輸入和輸出 tensors，因為您需要它們才能進行模型轉換和推斷要求。

可用的模型和對應的預設分類器輸出 tensors 如下所示，如果您使用預設分類器，這就是您要用於推斷的結果。

+ Resnet50、QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>註冊模型

使用 SDK 搭配 Azure Blob 儲存體中的 ZIP 檔案來[註冊](concept-model-management-and-deployment.md)模型。 加入標記和與模型相關的其他中繼資料，可協助您追蹤已定型的模型。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

如果您已經註冊模型，而且想要將它載入，您可以將它取出。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>轉換模型

將 TensorFlow 圖形轉換成 Open Neural Network Exchange 格式（[ONNX](https://onnx.ai/)）。  您將需要提供輸入和輸出 tensors 的名稱，而您的用戶端將會在取用 web 服務時使用這些名稱。

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>建立 Docker 映像

已轉換的模型和所有相依性都會新增至 Docker 映射。  然後可以部署並具現化此 Docker 映射。  支援的部署目標包括雲端中的 AKS 或邊緣裝置（例如[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)）。  您也可以為已註冊的 Docker 映射新增標記和描述。

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

依標記列出影像，並取得詳細的記錄以進行任何偵錯工具。

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. 部署至雲端或邊緣

### <a name="deploy-to-the-cloud"></a>部署到雲端

若要將模型部署為大規模生產 Web 服務，請使用 Azure Kubernetes Service (AKS)。 您可以使用 Azure Machine Learning SDK、CLI 或[Azure Machine Learning studio](https://ml.azure.com)來建立新的。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2”. If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 部署可能需要大約15分鐘的時間。  查看部署是否成功。

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

將容器部署至 AKS 叢集。
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>測試雲端服務
Docker 映射支援 gRPC 和 TensorFlow 服務「預測」 API。  使用範例用戶端呼叫 Docker 映射，以從模型取得預測。  可用的範例用戶端程式代碼：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果您想要使用 TensorFlow 服務，您可以[下載範例用戶端](https://www.tensorflow.org/serving/setup)。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

因為此分類器已在[ImageNet](http://www.image-net.org/)資料集上定型，所以將類別對應到人們可讀取的標籤。

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>清理服務
刪除您的 web 服務、映射和模型（必須依照此順序完成，因為有相依性）。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>部署到本機邊緣伺服器

所有[Azure Data Box Edge 裝置](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含執行模型的 FPGA。  FPGA 一次只能執行一個模型。  若要執行不同的模型，請直接部署新的容器。 您可以在[此 Azure 範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)中找到指示和範例程式碼。

## <a name="secure-fpga-web-services"></a>保護 FPGA Web 服務

若要保護您的 FPGA web 服務，請參閱[保護 web 服務](how-to-secure-web-service.md)檔。

## <a name="next-steps"></a>後續步驟

查看這些筆記本、影片和 blog：

+ 數個[範例筆記本](https://aka.ms/aml-accel-models-notebooks)

+ [超大規模資料庫硬體：大規模在 Azure + FPGA 上的 ML：組建2018（影片）](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [深入了解 Microsoft FPGA 架構的可設定雲端 (英文影片)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [適用於即時 AI 的 Project Brainwave：專案首頁](https://www.microsoft.com/research/project/project-brainwave/)
