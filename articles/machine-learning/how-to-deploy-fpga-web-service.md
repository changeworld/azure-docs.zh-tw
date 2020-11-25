---
title: 將 ML 模型部署至 Fpga
titleSuffix: Azure Machine Learning
description: 瞭解可現場程式化閘道陣列。 您可以在 FPGA 上，將 web 服務部署在具有超低延遲推斷 Azure Machine Learning。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, devx-track-python, deploy
ms.openlocfilehash: 9ec82dcd7578744dc7443d48dc28820413f14005
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996299"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>使用 Azure Machine Learning 將 ML 模型部署到可現場程式化閘道陣列 (Fpga)  

在本文中，您將瞭解 Fpga，以及如何使用[Azure Machine Learning](overview-what-is-azure-ml.md)中的[硬體加速模型 Python 套件](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py)，將 ML 模型部署至 Azure FPGA。

## <a name="what-are-fpgas"></a>什麼是 Fpga？
FPGA 包含可程式化邏輯區塊的陣列，以及可重新設定互連的階層。 互連可讓您在製造之後以不同方式設定這些區塊。 與其他晶片相比，FPGA 結合了可程式性和效能。 

Fpga 可讓您達到低延遲，以進行即時推斷 (或模型計分) 要求。 不需要非同步要求 (批次處理)。 批次處理可能會造成延遲 (因為需要處理更多資料)。 類神經處理單元的執行不需要批次處理;因此，相較于 CPU 和 GPU 處理器，延遲可能會比低許多倍。

您可以針對不同類型的機器學習模型重新設定 FPGA。 擁有此種彈性，就更易於依據最佳的數值有效位數和所用的記憶體模型來加速應用程式。 由於 FPGA 可重新設定，因此您可以與 AI 演算法快速變化的需求保持同步。

![Azure Machine Learning FPGA 比較的圖表](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|處理器| 縮寫 |描述|
|---|:-------:|------|
|應用程式特定的積體電路|ASIC|自訂電路 (例如，Google 的 TensorFlow 處理器 (TPU)) 可提供最高效率。 它們無法隨著您需求的變更加以重新設定。|
|現場可程式化閘陣列|FPGA|FPGA (例如 Azure 上所提供的那些陣列) 可提供接近 ASIC 的效能。 它們也會隨著時間而具有彈性且可重新設定，以實作新邏輯。|
|圖形處理器|GPU|這是頗受歡迎的 AI 計算選擇。 由於 GPU 能提供平行處理功能，因而使得它在轉譯影像的速度會比 CPU 還快。|
|中央處理器|CPU|一般用途的處理器，其效能不適合圖形和影片處理。|

## <a name="fpga-support-in-azure"></a>Azure 中的 FPGA 支援

Microsoft Azure 是 FPGA 全球最大的雲端投資。 Microsoft 會將 Fpga 用於深度類神經網路， (DNN) 評估、Bing 搜尋排名和軟體定義網路 (SDN) 加速來減少延遲，同時釋放 Cpu 來進行其他工作。

Azure 上的 Fpga 是以 Intel 的 FPGA 裝置為基礎，而資料科學家和開發人員會使用這些裝置來加速即時 AI 計算。 這個具有 FPGA 功能的架構可提供效能、彈性和擴展能力，並且可在 Azure 上使用。

Azure Fpga 會與 Azure Machine Learning 整合。 Azure 可以跨 Fpga 平行處理預先定型的 DNN，以擴充您的服務。 DNN 可以預先定型為深度 Featurizer 以傳輸學習，也可以使用更新過的加權來微調。

|Azure 上的案例 & 設定|支援的 DNN 模型|區域支援|
|--------------------------|--------------------|----------------|
|+ 影像分類和辨識案例<br/>+ TensorFlow 部署 (需要 Tensorflow 1.x) <br/>+ Intel FPGA 硬體|-ResNet 50<br/>-ResNet 152<br/>-Densenet-121-121<br/>-VGG-16-16<br/>-SSD-VGG-16|-美國東部<br/>-東南亞<br/> - 西歐<br/>-美國西部2|

若要將延遲和輸送量優化，您將資料傳送至 FPGA 模型的用戶端應該位於上述其中一個區域 (您部署模型所) 的其中一個區域。

**Azure vm 的 PBS 系列** 包含 Intel Arria 10 fpga。 當您檢查 Azure 配額配置時，它會顯示為「標準的 PBS 系列個 vcpu」。 PB6 VM 有六個個 vcpu 和一個 FPGA。 PB6 VM 會在模型部署期間自動由 Azure Machine Learning 布建至 FPGA。 它只會與 Azure ML 搭配使用，而且無法執行任意 bitstreams。 例如，您將無法使用 bitstreams 來快閃 FPGA，以進行加密、編碼等等。

## <a name="deploy-models-on-fpgas"></a>在 FPGA 上部署模型

您可以使用 [Azure Machine Learning 硬體加速模型](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py)，在 fpga 上將模型部署為 web 服務。 使用 Fpga 可提供超低延遲推斷，即使是單一批次大小也是如此。 

在此範例中，您會建立 TensorFlow 圖來預先處理輸入影像、使其成為在 FPGA 上使用 ResNet 50 的 featurizer，然後透過針對 ImageNet 資料集定型的分類器來執行這些功能。 然後，此模型會部署至 AKS 叢集。

### <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有帳戶，請建立 [隨用隨付](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) 帳戶 (免費的 Azure 帳戶不符合 FPGA 配額) 資格。

- 已安裝 Azure Machine Learning 工作區和適用于 Python 的 Azure Machine Learning SDK，如「 [建立工作區](how-to-manage-workspace.md)」中所述。
 
- 硬體加速模型套件：  `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- FPGA 配額。 提交 [配額的要求](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)，或執行此 CLI 命令以檢查配額： 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   請確定您在傳回的 __CurrentValue__ 下至少有6個個 vcpu。  

### <a name="define-the-tensorflow-model"></a>定義 TensorFlow 模型

首先，使用 [適用于 Python 的 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 來建立服務定義。 服務定義是根據 TensorFlow 來描述圖形管線 (輸入、功能化器和分類器) 的檔案。 部署命令會將定義和圖形壓縮成 ZIP 檔案，並將 ZIP 上傳至 Azure Blob 儲存體。 DNN 已部署為在 FPGA 上執行。

1. 載入 Azure Machine Learning 工作區

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. 前置處理映射。 Web 服務的輸入為 JPEG 影像。  第一個步驟是將 JPEG 影像解碼並進行前置處理。  JPEG 影像會視為字串，而結果會是張量，這會是 ResNet 50 模型的輸入。

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Load featurizer。 初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。  取代程式碼片段中的 "QuantizedResnet50"，以匯入其他深度類神經網路：

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

1. 加入分類器。 此分類器已在 ImageNet 資料集上定型。

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. 儲存模型。 現在已載入預處理器、ResNet 50 featurizer 和分類器，請將圖形和相關聯的變數儲存為模型。

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

1. 儲存輸入和輸出張量， **因為您將使用它們來進行模型轉換和推斷要求**。 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   如果您使用預設分類器，則可以使用下列模型，以及它們的分類輸出張量進行推斷。

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

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>將模型轉換為 Open Neural Network Exchange 格式 (ONNX) 

在您可以部署至 Fpga 之前，請先將模型轉換成 [ONNX](https://onnx.ai/) 格式。

1. 使用 SDK 搭配 Azure Blob 儲存體中的 ZIP 檔案來[註冊](concept-model-management-and-deployment.md)模型。 加入標籤和模型的其他中繼資料，可協助您追蹤已定型的模型。

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   如果您已註冊模型，並想要將它載入，則可加以取出。

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. 將 TensorFlow 圖形轉換成 ONNX 格式。  您必須提供輸入和輸出張量的名稱，讓您的用戶端可以在取用 web 服務時使用這些名稱。

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

### <a name="containerize-and-deploy-the-model"></a>將和部署模型

接下來，從轉換後的模型和所有相依性建立 Docker 映射。  然後可以部署和具現化此 Docker 映射。  支援的部署目標包括雲端中的 Azure Kubernetes Service (AKS) 或邊緣裝置，例如 [Azure 資料箱 edge](../databox-online/azure-stack-edge-overview.md)。  您也可以新增已註冊 Docker 映射的標記和描述。

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

   依標記列出影像，並取得任何偵錯工具的詳細記錄。

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes Service 叢集

1. 若要將您的模型部署為高規模的生產 web 服務，請使用 AKS。 您可以使用 Azure Machine Learning SDK、CLI 或 [Azure Machine Learning studio](https://ml.azure.com)建立一個新的。

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

    AKS 部署可能需要大約15分鐘的時間。  查看部署是否成功。

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. 將容器部署至 AKS 叢集。

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

#### <a name="deploy-to-a-local-edge-server"></a>部署到本機 edge server

所有 [Azure 資料箱 Edge 裝置](../databox-online/azure-stack-edge-overview.md
) 都包含用來執行模型的 FPGA。  FPGA 一次只能在一個模型上執行。  若要執行不同的模型，只需部署新的容器。 您可以在 [此 Azure 範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)中找到指示和範例程式碼。

### <a name="consume-the-deployed-model"></a>取用已部署的模型

最後，使用範例用戶端來呼叫 Docker 映射，以從模型取得預測。  可用的範例用戶端程式代碼：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker 映射支援 gRPC 和提供「預測」 API 的 TensorFlow。

您也可以下載適用于 TensorFlow 服務的範例用戶端。

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

因為此分類器是在 [ImageNet](http://www.image-net.org/) 資料集上定型，所以會將類別對應至人們可讀取的標籤。

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

### <a name="clean-up-resources"></a>清除資源

若要避免不必要的成本，請依下列 **順序** 清除您的資源： web 服務、影像，以及模型。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>後續步驟

+ 瞭解如何 [保護您的 web 服務](how-to-secure-web-service.md) 檔。

+ 瞭解 FPGA 和 [Azure Machine Learning 定價和成本](https://azure.microsoft.com/pricing/details/machine-learning/)。

+ [超大規模硬體：在 Azure 上大規模的 ML、FPGA：組建 2018 (影片) ](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [以 Microsoft FPGA 為基礎的可設定雲端 (影片) ](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [適用于即時 AI 的專案 Brainwave](https://www.microsoft.com/research/project/project-brainwave/)

+ [自動光學檢查系統](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)