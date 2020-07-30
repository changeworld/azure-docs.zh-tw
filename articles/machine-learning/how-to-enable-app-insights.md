---
title: 從 Machine Learning web 服務端點監視及收集資料
titleSuffix: Azure Machine Learning
description: 使用 Azure 應用程式 Insights 監視以 Azure Machine Learning 部署的 web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 88a122a9af4a5edac45a3189df5ffb78fb2ce271
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423808"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>從 ML Web 服務端點監視及收集資料
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何藉由啟用 Azure 應用程式的深入解析，從部署到 Azure Kubernetes Service （AKS）或 Azure 容器實例（ACI）中 web 服務端點的資料，以及監視這些模型。 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning studio](#studio)https://ml.azure.com

除了收集端點的輸出資料和回應之外，您還可以監視：

* 要求速率、回應時間和失敗率
* 相依性速率、回應時間和失敗率
* 例外狀況

[深入瞭解 Azure 應用程式 Insights](../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)

* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 若要瞭解如何取得這些必要條件，請參閱[如何設定開發環境](how-to-configure-environment.md)

* 要部署至 Azure Kubernetes Service (AKS) 或 Azure Container 執行個體 (ACI) 的已訓練機器學習模型。 如果您沒有，請參閱[訓練影像分類模型](tutorial-train-models-with-aml.md)教學課程

## <a name="web-service-metadata-and-response-data"></a>Web 服務中繼資料和回應資料

> [!IMPORTANT]
> Azure 應用程式 Insights 只會記錄最多64kb 的承載。 若達到此限制，您可能會看到錯誤，例如記憶體不足，或無法記錄任何資訊。

若要記錄 web 服務要求的資訊，請將 `print` 語句加入至 score.py 檔案。 每個語句都會在 `print` 訊息底下 Application Insights 的追蹤資料表中產生一個專案 `STDOUT` 。 語句的內容 `print` 將包含在 `customDimensions` `Contents` 追蹤資料表中的底下。 如果您列印 JSON 字串，它會在底下的追蹤輸出中產生階層式資料結構 `Contents` 。

您可以直接查詢 Azure 應用程式深入解析以存取此資料，或設定[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)至儲存體帳戶，以延長保留期或進一步處理。 然後，可以在 Azure Machine Learning 中使用模型資料來設定標籤、重新定型、可解釋性、資料分析或其他用途。 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 設定 

### <a name="update-a-deployed-service"></a>更新已部署的服務

1. 識別您工作區中的服務。 的值 `ws` 是您工作區的名稱

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

1. 若要在推斷期間將資料傳送至 Application Insights，請藉由加入 print 語句來更新評分檔案。 若要記錄較複雜的資訊，例如要求資料和回應，我們是 JSON 結構。 下列範例 score.py 檔會記錄模型初始化的時間、推斷期間的輸入和輸出，以及發生任何錯誤的時間：

    > [!IMPORTANT]
    > Azure 應用程式 Insights 只會記錄最多64kb 的承載。 若達到此限制，您可能會看到像是記憶體不足之類的錯誤，或者不會記錄任何資訊。 如果您想要記錄的資料大於64kb，您應該改為使用在[生產環境中收集模型的資料](how-to-enable-data-collection.md)中的資訊，將它儲存至 blob 儲存體。
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. 更新服務設定
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 建置映像並將其部署在 [AKS 或 ACI](how-to-deploy-and-where.md) 上。

如需記錄和資料收集的詳細資訊，請參閱[在 Azure Machine Learning 中啟用記錄](how-to-enable-logging.md)和[從生產環境中的模型收集資料](how-to-enable-data-collection.md)。

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Azure 應用程式 Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>使用 Azure Machine Learning studio 設定

當您準備好使用這些步驟來部署您的模型時，也可以從 Azure Machine Learning studio 啟用 Azure 應用程式深入解析。

1. 登入您的工作區，網址為https://ml.azure.com/
1. 移至 [**模型**]，然後選取您想要部署的模型
1. 選取 [ **+ 部署**]
1. 填入 [**部署模型**] 表單
1. 展開 [ **Advanced** ] 功能表

    ![部署表單](./media/how-to-enable-app-insights/deploy-form.png)
1. 選取 [**啟用 Application Insights 診斷和資料收集**]

    ![啟用 App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>查看計量和記錄

您的服務資料會儲存在您的 Azure 應用程式 Insights 帳戶中，與 Azure Machine Learning 相同的資源群組中。
若要檢視：

1. 移至您在[studio](https://ml.azure.com/)中的 Azure Machine Learning 工作區。
1. 選取 [端點]  。
1. 選取您已部署的服務。
1. 向下滾動以尋找**Application Insights url** ，然後按一下連結。

    [![找出 Application Insights url](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. 在 Application Insights 中，從左側清單的 [**總覽**] 索引標籤或 [__監視__] 區段中，選取 [__記錄__]。

    [![監視的 [總覽] 索引標籤](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要查看從 score.py 檔案記錄的資訊，請查看 [__追蹤__] 資料表。 下列查詢會搜尋已記錄__輸入__值的記錄：

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![追蹤資料](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

若要深入瞭解如何使用 Azure 應用程式深入解析，請參閱[什麼是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>匯出資料以進行進一步的處理和較長的保留

>[!Important]
> Azure 應用程式 Insights 僅支援將匯出至 blob 儲存體。 此匯出功能的其他限制列在[來自 App Insights 的匯出遙測](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)。

您可以使用 Azure 應用程式 Insights 的[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)，將訊息傳送至支援的儲存體帳戶，其中可以設定較長的保留期。 資料會以 JSON 格式儲存，而且可以輕鬆地剖析以解壓縮模型資料。 

Azure Data Factory、Azure ML 管線或其他資料處理工具可以視需要用來轉換資料。 當您轉換資料之後，您可以使用 Azure Machine Learning 工作區將它註冊為資料集。 若要這麼做，請參閱[如何建立及註冊資料集](how-to-create-register-datasets.md)。

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="連續匯出":::


## <a name="example-notebook"></a>範例筆記本

Ipynb 筆記本中的「[啟用-應用程式深入](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb)解析」會示範本文中的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* 瞭解[如何將模型部署到 Azure Kubernetes Service](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)叢集，或如何將模型部署到[Azure 容器實例](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance)，以將您的模型部署至 web 服務端點，並啟用 Azure 應用程式 Insights 以利用資料收集和端點監視
* 請參閱[MLOps：使用 Azure Machine Learning 管理、部署及監視模型](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)，以深入瞭解如何運用在生產環境中從模型收集的資料。 這類資料可協助您持續改善機器學習程式
