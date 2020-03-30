---
title: 什麼是 FPGA - 如何部署
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習在 FPGA 上運行模型來部署 Web 服務，以便進行超低延遲推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472353"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>什麼是現場可程式設計閘陣列 （FPGA） 以及如何部署
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介紹了現場可程式設計閘陣列 （FPGA） 的介紹，並介紹了如何使用 Azure 機器學習將模型部署到 Azure FPGA。

FPGA 包含可程式化邏輯區塊的陣列，以及可重新設定互連的階層。 互連可讓您在製造之後以不同方式設定這些區塊。 與其他晶片相比，FPGA 結合了可程式性和效能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 與 CPU、GPU 和 ASIC

下列圖表示範 FPGA 與其他處理器的比較方式。

![Azure 機器學習 FPGA 比較圖](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|處理器||描述|
|---|:-------:|------|
|應用程式特定的積體電路|ASIC|自訂電路 (例如，Google 的 TensorFlow 處理器 (TPU)) 可提供最高效率。 它們無法隨著您需求的變更加以重新設定。|
|現場可程式化閘陣列|FPGA|FPGA (例如 Azure 上所提供的那些陣列) 可提供接近 ASIC 的效能。 它們也會隨著時間而具有彈性且可重新設定，以實作新邏輯。|
|圖形處理器|GPU|這是頗受歡迎的 AI 計算選擇。 由於 GPU 能提供平行處理功能，因而使得它在轉譯影像的速度會比 CPU 還快。|
|中央處理器|CPU|一般用途的處理器，其效能不適合圖形和影片處理。|

Azure 上的 FPGA 基於英特爾的 FPGA 設備，資料科學家和開發人員使用這些設備來加速即時 AI 計算。 這個具有 FPGA 功能的架構可提供效能、彈性和擴展能力，並且可在 Azure 上使用。

FPGA 使即時推理（或模型評分）請求實現低延遲成為可能。 不需要非同步要求 (批次處理)。 批次處理可能會造成延遲 (因為需要處理更多資料)。 神經處理單元的實現不需要批次處理;因此，與 CPU 和 GPU 處理器相比，延遲可能低很多倍。

### <a name="reconfigurable-power"></a>可重新設定的電源
您可以針對不同類型的機器學習模型重新設定 FPGA。 擁有此種彈性，就更易於依據最佳的數值有效位數和所用的記憶體模型來加速應用程式。 由於 FPGA 可重新設定，因此您可以與 AI 演算法快速變化的需求保持同步。

## <a name="whats-supported-on-azure"></a>Azure 上所支援的項目
Microsoft Azure 是 FPGA 全球最大的雲端投資。 使用這個具有 FPGA 功能的硬體架構，已定型的類神經網路執行速度會更快，延遲也較低。 Azure 可以跨 FPGA 並行化預先訓練的深層神經網路 （DNN）， 以擴展服務。 DNN 可以預先定型為深度 Featurizer 以傳輸學習，也可以使用更新過的加權來微調。

Azure 上的 FPGA 支援：

+ 影像分類和辨識案例
+ TensorFlow 部署
+ Intel FPGA 硬體

這些 DNN 型號當前可用：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA 在這些 Azure 區域中可用：
  - 美國東部
  - 東南亞
  - 西歐
  - 美國西部 2

> [!IMPORTANT]
> 為了優化延遲和輸送量，向 FPGA 模型發送資料的用戶端應位於上述區域之一（將模型部署到的區域）。

**PBS Azure VM 系列**包含英特爾 Arria 10 FPGA。 當您檢查 Azure 配額分配時，它將顯示為"標準 PBS 系列 vCPU"。 PB6 VM 具有 6 個 vCPU 和一個 FPGA，Azure ML 將自動預配它，作為將模型部署到 FPGA 的一部分。 它僅與 Azure ML 一起使用，並且不能運行任意位元流。 例如，您將無法使用位流快閃記憶體 FPGA 以執行加密、編碼等操作。

### <a name="scenarios-and-applications"></a>案例和應用程式

Azure FPGA 與 Azure 機器學習集成。 Microsoft 使用 FPGA 來評估 DNN、Bing 搜尋順位排序，以及軟體定義網路 (SDN) 加速來減少延遲，同時釋放 CPU 來執行其他工作。

以下方案使用 FPGA：
+ [自動光學檢測系統](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [土地覆蓋圖](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>示例：在 FPGA 上部署模型

您可以使用 Azure 機器學習硬體加速模型在 FPGA 上將模型作為 Web 服務進行部署。 使用 FPGA 可提供超低延遲推理，即使具有單個批次處理大小也是如此。 推理或模型評分是部署模型用於預測的階段，最常見的是生產資料。

### <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。  如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- FPGA 配額。 使用 Azure CLI 檢查是否有配額：

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 其他可能的位置是``southeastasia``和``westeurope``。 ``westus2``

    該命令返回類似于以下內容的文本：

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    確保在 __"當前值"__ 下至少有 6 個 vCPU。

    如果沒有配額，請在 上[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)提交請求。

- 已安裝 Azure Machine Learning 工作區與適用於 Python 的 Azure Machine Learning SDK。 有關詳細資訊，請參閱[創建工作區](how-to-manage-workspace.md)。
 
- 用於硬體加速模型的 Python SDK：

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. 創建模型並將其容器化

本文檔將介紹如何創建 TensorFlow 圖形以預處理輸入圖像，使其成為使用 FPGA 上的 ResNet 50 的功勳，然後通過在 ImageNet 資料集上訓練的分類器運行要素。

依照指示執行以：

* 定義 TensorFlow 模型
* 轉換模型
* 部署模型
* 取用已部署的模型
* 刪除已部署的服務

使用[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 來建立服務定義。 服務定義是根據 TensorFlow 來描述圖形管線 (輸入、功能化器和分類器) 的檔案。 部署命令會自動將定義和圖表壓縮為 ZIP 檔案，然後將 ZIP 檔案上傳到 Azure Blob 儲存體。 DNN 已部署到 FPGA 上運行。

### <a name="load-azure-machine-learning-workspace"></a>載入 Azure 機器學習工作區

載入 Azure 機器學習工作區。

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>預先處理映像

對 Web 服務的輸入是 JPEG 圖像。  第一步是解碼 JPEG 映射並對其進行預處理。  JPEG 圖像被視為字串，結果是將輸入到 ResNet 50 模型的張子。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>負載推土機

初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。  您可以通過導入其他深層神經網路，將下面程式碼片段中的"量化 Resnet50"替換為：

- 量化再網152
- 量化Vgg16
- 密集網121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>添加分類器

此分類器已在 ImageNet 資料集上定型。  轉移學習和培訓自訂重量的示例可在示例[筆記本](https://aka.ms/aml-notebooks)集中。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>儲存模型

現在，預處理器、ResNet 50 featurizer 和分類器已載入，因此將圖形和相關變數保存為模型。

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

### <a name="save-input-and-output-tensors"></a>節省輸入和輸出張量
模型轉換和推理需要在預處理和分類器步驟期間創建的輸入和輸出張量。

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 保存輸入和輸出張量，因為需要它們來進行模型轉換和推理請求。

可用模型和相應的預設分類器輸出拉伸器如下，如果使用預設分類器，則使用此參數進行推理。

+ Resnet50， 量化 Resnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152， 量化Resnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ 密集網121，量化密集網121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16， 量化Vgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg， 量化SsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>註冊模型

使用 SDK 與 Azure Blob 存儲中的 ZIP 檔一起[註冊](concept-model-management-and-deployment.md)模型。 添加有關模型的標記和其他中繼資料可説明您跟蹤已訓練的模型。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

如果您已經註冊了模型並希望載入它，則可以檢索它。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>轉換模型

將 TensorFlow 圖形轉換為開放神經網路交換格式 （[ONNX](https://onnx.ai/)）。  您需要提供輸入和輸出張量的名稱，並且這些名稱將由用戶端在使用 Web 服務時使用。

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

轉換後的模型和所有依賴項將添加到 Docker 映射。  然後可以部署和具現化此 Docker 映射。  支援的部署目標包括在雲中的 AKS 或邊緣設備（如[Azure 資料框邊緣](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)）。  您還可以為註冊的 Docker 映射委任標記和說明。

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

按標記列出映射，並獲取任何調試的詳細日誌。

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. 部署到雲或邊緣

### <a name="deploy-to-the-cloud"></a>部署到雲

若要將模型部署為大規模生產 Web 服務，請使用 Azure Kubernetes Service (AKS)。 可以使用 Azure 機器學習 SDK、CLI 或[Azure 機器學習工作室](https://ml.azure.com)創建新的。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 部署可能需要大約 15 分鐘。  檢查部署是否成功。

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

將容器部署到 AKS 群集。
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

#### <a name="test-the-cloud-service"></a>測試雲服務
Docker 映射支援 gRPC 和 TensorFlow 服務"預測"API。  使用示例用戶端調用 Docker 映射從模型獲取預測。  示例用戶端代碼可用：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果要使用 TensorFlow 服務，可以[下載示例用戶端](https://www.tensorflow.org/serving/setup)。

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

由於此分類器在[ImageNet](http://www.image-net.org/)資料集上進行了培訓，因此將類映射到人類可讀標籤。

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
刪除 Web 服務、映射和模型（必須按此順序完成，因為有依賴項）。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>部署到本地邊緣伺服器

所有[Azure 資料框邊緣設備](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含用於運行模型的 FPGA。  一次只能在 FPGA 上運行一個模型。  要運行其他模型，只需部署一個新容器。 有關說明和示例代碼可以[在此 Azure 示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)中找到。

## <a name="secure-fpga-web-services"></a>保護 FPGA Web 服務

要保護您的 FPGA Web 服務，請參閱[安全 Web 服務](how-to-secure-web-service.md)文檔。

## <a name="next-steps"></a>後續步驟

查看以下筆記本、視頻和博客：

+ 幾個[示例筆記本](https://aka.ms/aml-accel-models-notebooks)

+ [超大規模硬體：在 Azure 和 FPGA 之上的放大 ML：構建 2018（視頻）](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [深入了解 Microsoft FPGA 架構的可設定雲端 (英文影片)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [適用於即時 AI 的 Project Brainwave：專案首頁](https://www.microsoft.com/research/project/project-brainwave/)
