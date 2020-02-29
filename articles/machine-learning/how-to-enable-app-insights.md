---
title: 從 Machine Learning web 服務端點監視及收集資料
titleSuffix: Azure Machine Learning
description: 使用 Azure 應用程式 Insights 監視以 Azure Machine Learning 部署的 web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 621188b3901bdea1a7ae50ac49c0e6f625a3e79a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915753"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>從 ML web 服務端點監視及收集資料
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何藉由啟用 Azure 應用程式 Insights，從 Azure Kubernetes Service （AKS）或 Azure 容器實例（ACI）中部署至 web 服務端點的模型收集資料並加以監視。 除了收集端點的輸入資料和回應之外，您還可以監視：

* 要求速率、回應時間和失敗率
* 相依性速率、回應時間和失敗率
* 例外狀況

[深入瞭解 Azure 應用程式 Insights](../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)

* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 若要瞭解如何取得這些必要條件，請參閱[如何設定開發環境](how-to-configure-environment.md)
* 要部署至 Azure Kubernetes Service (AKS) 或 Azure Container 執行個體 (ACI) 的已訓練機器學習模型。 如果您沒有，請參閱[訓練影像分類模型](tutorial-train-models-with-aml.md)教學課程

## <a name="web-service-metadata-and-response-data"></a>Web 服務中繼資料和回應資料

>[!Important]
> Azure 應用程式 Insights 只會記錄最多64kb 的承載。 如果達到此限制，則只會記錄模型的最新輸出。 

對應至 web 服務中繼資料和模型預測之服務的中繼資料和回應，會記錄到訊息 `"model_data_collection"`下的 Azure 應用程式 Insights 追蹤。 您可以直接查詢 Azure 應用程式深入解析以存取此資料，或設定[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)至儲存體帳戶，以延長保留期或進一步處理。 然後，可以在 Azure Machine Learning 中使用模型資料來設定標籤、重新定型、可解釋性、資料分析或其他用途。 

## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 設定 

### <a name="update-a-deployed-service"></a>更新已部署的服務

1. 識別您工作區中的服務。 `ws` 的值是您工作區的名稱

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新您的服務並啟用 Azure 應用程式 Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤

如果您想要記錄自訂追蹤，請依循[部署方式及位置](how-to-deploy-and-where.md)文件中的 AKS 或 ACI 標準部署程序。 然後使用下列步驟：

1. 藉由加入 print 語句來更新評分檔案
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服務設定
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 建立映射並將它部署在[AKS 或 ACI](how-to-deploy-and-where.md)上。

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Azure 應用程式 Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>評估資料
您的服務資料會儲存在您的 Azure 應用程式 Insights 帳戶中，與 Azure Machine Learning 相同的資源群組中。
若要檢視：

1. 前往[Azure Machine Learning studio](https://ml.azure.com)中的 Azure Machine Learning 工作區，然後按一下 [Application Insights 連結]

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 選取 [**總覽**] 索引標籤，以查看您服務的一組基本計量

   [![概觀](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要查看您的 web 服務要求中繼資料和回應，請選取 [**記錄（分析）** ] 區段中的 [**要求**] 資料表，然後選取 [**執行**] 以查看要求

   [![模型資料](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 若要查看您的自訂追蹤，請選取 [**分析**]
4. 在 [結構描述] 區段中，選取 [追蹤]。 然後選取 [執行] 來執行查詢。 資料應以資料表格式出現，且應該對應至您評分檔案中的自訂呼叫

   [![自訂追蹤](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

若要深入瞭解如何使用 Azure 應用程式深入解析，請參閱[什麼是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>匯出資料以進行進一步的處理和較長的保留

>[!Important]
> Azure 應用程式 Insights 僅支援將匯出至 blob 儲存體。 此匯出功能的其他限制列在[來自 App Insights 的匯出遙測](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)。

您可以使用 Azure 應用程式 Insights 的[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)，將訊息傳送至支援的儲存體帳戶，其中可以設定較長的保留期。 `"model_data_collection"` 的訊息會以 JSON 格式儲存，而且可以輕鬆地剖析以解壓縮模型資料。 

Azure Data Factory、Azure ML 管線或其他資料處理工具可以視需要用來轉換資料。 當您轉換資料之後，您可以使用 Azure Machine Learning 工作區將它註冊為資料集。 若要這麼做，請參閱[如何建立及註冊資料集](how-to-create-register-datasets.md)。

   [![連續匯出](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>範例筆記本

Ipynb 筆記本中的「[啟用-應用程式深入](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb)解析」會示範本文中的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* 瞭解[如何將模型部署到 Azure Kubernetes Service](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)叢集，或如何將模型部署到[Azure 容器實例](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance)，以將您的模型部署至 web 服務端點，並啟用 Azure 應用程式 Insights 以利用資料收集和端點監視
* 請參閱[MLOps：使用 Azure Machine Learning 管理、部署及監視模型](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)，以深入瞭解如何運用在生產環境中從模型收集的資料。 這類資料可協助您持續改善機器學習程式
