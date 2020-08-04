---
title: 部署用於認知搜尋的模型
titleSuffix: Azure Machine Learning
description: 本文會教您如何使用 Azure Machine Learning 來部署要搭配 Azure 認知搜尋使用的模型。 認知搜尋可以使用 Azure Machine Learning 所部署的模型做為自訂技能，以豐富搜尋體驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: larryfr
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.openlocfilehash: 59671a0520d665d594356a2e6aee46116a8de5d3
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541765"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>部署用於認知搜尋的模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文會教您如何使用 Azure Machine Learning 來部署要搭配[Azure 認知搜尋](../search/search-what-is-azure-search.md)使用的模型。

認知搜尋會對不同的內容執行內容處理，使其可供人類或應用程式進行查詢。 您可以使用從 Azure Machine Learning 部署的模型來增強此程式。

Azure Machine Learning 可以將定型的模型部署為 web 服務。 接著，會將 web 服務內嵌在認知搜尋_技能_中，這會成為處理管線的一部分。

> [!IMPORTANT]
> 本文中的資訊是模型部署專用的。 它提供支援的部署設定的相關資訊，這些設定可讓認知搜尋使用模型。
>
> 如需有關如何設定認知搜尋以使用已部署模型的詳細資訊，請參閱使用 Azure Machine Learning 教學課程來[建立和部署自訂技能](../search/cognitive-search-tutorial-aml-custom-skill.md)。
>
> 如需教學課程所依據的範例，請參閱 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) 。

當部署模型以與 Azure 認知搜尋搭配使用時，部署必須符合下列需求：

* 使用 Azure Kubernetes Service 來裝載用於推斷的模型。
* 啟用 Azure Kubernetes Service 的傳輸層安全性（TLS）。 TLS 是用來保護認知搜尋與已部署模型之間的 HTTPS 通訊。
* 輸入腳本必須使用 `inference_schema` 封裝來產生服務的 OpenAPI （Swagger）架構。
* 輸入腳本也必須接受 JSON 資料做為輸入，並產生 JSON 做為輸出。


## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 已安裝 Azure Machine Learning SDK 的 Python 開發環境。 如需詳細資訊，請參閱[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* 已註冊的模型。 如果您沒有模型，請使用中的範例筆記本 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) 。

* 大致瞭解[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="connect-to-your-workspace"></a>連線到您的工作區

Azure Machine Learning 工作區提供一個集中的位置，可處理您在使用 Azure Machine Learning 時所建立的所有成品。 工作區會保留所有定型執行的歷程記錄，包括記錄、計量、輸出，以及腳本的快照集。

若要連接到現有的工作區，請使用下列程式碼：

> [!IMPORTANT]
> 此程式碼片段預期工作區組態會儲存在目前目錄或其父系目錄中。 如需詳細資訊，請參閱[建立和管理 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 如需將組態儲存至檔案的詳細資訊，請參閱[建立工作區組態檔](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>建立 Kubernetes 叢集

**估計時間**：約20分鐘。

Kubernetes 叢集是一組用來執行容器化應用程式的虛擬機器實例（稱為節點）。

當您將模型從 Azure Machine Learning 部署至 Azure Kubernetes Service 時，會將模型以及將其裝載為 web 服務所需的所有資產封裝到 Docker 容器中。 然後，此容器會部署到叢集上。

下列程式碼示範如何為您的工作區建立新的 Azure Kubernetes Service （AKS）叢集：

> [!TIP]
> 您也可以將現有的 Azure Kubernetes Service 附加至 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[如何將模型部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

> [!IMPORTANT]
> 請注意，程式碼會使用 `enable_ssl()` 方法來啟用叢集的傳輸層安全性（TLS）。 當您計畫從認知服務使用已部署的模型時，這是必要的。

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 只要 AKS 叢集存在，Azure 就會向您收取費用。 當您完成 AKS 叢集時，請務必將其刪除。

如需有關使用 AKS 搭配 Azure Machine Learning 的詳細資訊，請參閱[如何部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>撰寫專案腳本

輸入腳本會接收提交至 web 服務的資料，將其傳遞至模型，並傳回評分結果。 下列腳本會在啟動時載入模型，然後使用模型來對資料進行計分。 有時會呼叫這個檔案 `score.py` 。

> [!TIP]
> 這是模型特定的輸入指令碼。 例如，腳本必須知道要與您的模型、資料格式等搭配使用的架構。

> [!IMPORTANT]
> 當您計畫使用從 Azure 認知服務部署的模型時，您必須使用 `inference_schema` 套件來啟用部署的架構產生。 此套件提供的裝飾專案可讓您定義 web 服務的輸入和輸出資料格式，以使用模型執行推斷。

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

如需有關輸入腳本的詳細資訊，請參閱[如何和部署位置](how-to-deploy-and-where.md)。

## <a name="define-the-software-environment"></a>定義軟體環境

環境類別是用來定義服務的 Python 相依性。 它包含模型和專案腳本所需的相依性。 在此範例中，它會從一般 pypi 索引，以及從 GitHub 存放庫安裝套件。 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

如需環境的詳細資訊，請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。

## <a name="define-the-deployment-configuration"></a>定義部署設定

部署設定會定義用來執行 web 服務的 Azure Kubernetes Service 主控環境。

> [!TIP]
> 如果您不確定部署的記憶體、CPU 或 GPU 需求，可以流量分析來瞭解這些。 如需詳細資訊，請參閱[如何和在何處部署模型](how-to-deploy-and-where.md)。

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

如需詳細資訊，請參閱[Deploy_configuration AksService](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)的參考檔。

## <a name="define-the-inference-configuration"></a>定義推斷設定

推斷設定會指向輸入腳本和環境物件：

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

如需詳細資訊，請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)的參考檔。

## <a name="deploy-the-model"></a>部署模型

將模型部署到您的 AKS 叢集，並等候它建立您的服務。 在此範例中，會從登錄載入兩個已註冊的模型，並將其部署至 AKS。 部署之後，部署中的檔案會 `score.py` 載入這些模型，並使用它們來執行推斷。

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

如需詳細資訊，請參閱[Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的參考檔。

## <a name="issue-a-sample-query-to-your-service"></a>對您的服務發出範例查詢

下列範例會使用 `aks_service` 先前的程式碼區段所儲存在變數中的部署資訊。 它會使用此變數來抓取與服務通訊所需的評分 URL 和驗證權杖：

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

從服務傳回的結果與下列 JSON 類似：

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>連接到認知搜尋

如需從認知搜尋使用此模型的詳細資訊，請參閱使用 Azure Machine Learning 教學課程來[建立和部署自訂技能](../search/cognitive-search-tutorial-aml-custom-skill.md)。

## <a name="clean-up-the-resources"></a>清除資源

如果您特別針對此範例建立了 AKS 叢集，請在使用認知搜尋完成測試之後，刪除您的資源。

> [!IMPORTANT]
> Azure 會根據 AKS 叢集的部署時間來計費。 完成使用之後，請務必將它清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [使用 Azure Machine Learning 建立及部署自訂技能](../search/cognitive-search-tutorial-aml-custom-skill.md)
