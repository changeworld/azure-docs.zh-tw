---
title: 收集生產環境模型的相關資料
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Blob 存儲中收集 Azure 機器學習輸入模型資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771388"
---
# <a name="collect-data-for-models-in-production"></a>在生產環境中收集模型資料

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Azure 機器學習監視 SDK 將很快停用。 SDK 仍然適用于當前使用 SDK 監視模型中的資料漂移的開發人員。 但對於新客戶，我們建議使用[帶有應用程式見解的簡化資料監控](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)。

本文演示如何從 Azure 機器學習中收集輸入模型資料。 它還演示如何將輸入資料部署到 Azure 庫伯奈斯服務 （AKS） 群集，並將輸出資料存儲在 Azure Blob 存儲中。

啟用收集後，您收集的資料可説明您：

* 當生產資料進入模型時[，監控資料漂移](how-to-monitor-data-drift.md)。

* 更好地決定何時重新訓練或優化模型。

* 使用收集的資料重新訓練模型。

## <a name="what-is-collected-and-where-it-goes"></a>收集的內容和去向

下列是可收集的資料：

* 類比部署在 AKS 群集中的 Web 服務的輸入資料。 *不會*收集語音音訊、圖像和視頻。
  
* 使用生產輸入資料的模型預測。

>[!NOTE]
> 此資料的預聚合和預計算當前不是收集服務的一部分。

輸出保存在 Blob 存儲中。 由於資料已添加到 Blob 存儲中，因此您可以選擇您最喜愛的工具來運行分析。

Blob 中輸出資料的路徑遵循此語法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 在版本 0.1.0a16 之前的 Python Azure 機器學習 SDK`designation`版本中，參數`identifier`名為 。 如果使用早期版本開發代碼，則需要相應地更新它。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree)。

- 必須安裝 Azure 機器學習工作區、包含腳本的本地目錄和用於 Python 的 Azure 機器學習 SDK。 要瞭解如何安裝它們，請參閱[如何配置開發環境](how-to-configure-environment.md)。

- 您需要將經過培訓的機器學習模型部署到 AKS。 如果沒有模型，請參閱[訓練圖像分類模型](tutorial-train-models-with-aml.md)教程。

- 您需要 AKS 群集。 有關如何創建一個並部署到它的資訊，請參閱[如何部署以及在哪裡](how-to-deploy-and-where.md)。

- [設置環境](how-to-configure-environment.md)並安裝 Azure[機器學習監視 SDK](https://aka.ms/aml-monitoring-sdk)。

## <a name="enable-data-collection"></a>啟用資料收集

無論通過 Azure 機器學習或其他工具部署的模型如何，都可以啟用資料收集。

要啟用資料收集，您需要：

1. 開啟評分檔案。

1. 在檔案開頭處新增[下列程式碼](https://aka.ms/aml-monitoring-sdk)：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. 在 `init` 函式中宣告資料收集變數：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *關聯 Id*是可選參數。 如果模型不需要，則無需使用它。 使用*關聯 Id*確實可以説明您更輕鬆地映射其他資料，如*貸款編號*或*客戶 Id*。
    
    *識別碼*參數稍後用於在 Blob 中生成資料夾結構。 您可以使用它區分原始資料和處理的資料。

1. 將下列程式碼行新增至 `run(input_df)` 函式：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. 在 AKS 中部署服務時，資料收集*不會*自動設置為**true。** 更新設定檔，如以下示例所示：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    您還可以通過更改此配置為服務監視啟用應用程式見解：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 要創建新映射並部署機器學習模型，請參閱[如何部署以及部署位置](how-to-deploy-and-where.md)。

如果您已有一個服務，該服務在環境檔和評分檔中安裝了依賴項，請按照以下步驟啟用資料收集：

1. 轉到[Azure 機器學習](https://ml.azure.com)。

1. 開啟工作區。

1. 選擇**部署** > **選擇服務** > **編輯**。

   ![編輯服務](././media/how-to-enable-data-collection/EditService.PNG)

1. 在 **"高級設置"** 中，選擇**啟用模型資料收集**。

    [![選擇資料收集](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   您還可以選擇啟用**AppInsights 診斷**以跟蹤服務的運行狀況。

1. 選擇 **"更新"** 以應用更改。

## <a name="disable-data-collection"></a>停用資料收集

您可以隨時停止收集資料。 使用 Python 代碼或 Azure 機器學習禁用資料收集。

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>選項 1 - 禁用 Azure 機器學習中的資料收集

1. 登錄到[Azure 機器學習](https://ml.azure.com)。

1. 開啟工作區。

1. 選擇**部署** > **選擇服務** > **編輯**。

   [![選擇"編輯"選項](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. 在**高級設置中**，清除**啟用模型資料收集**。

    [![清除資料收集核取方塊](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. 選取 [更新]**** 以套用變更。

您還可以在[Azure 機器學習](https://ml.azure.com)的工作區中訪問這些設置。

### <a name="option-2---use-python-to-disable-data-collection"></a>選項 2 - 使用 Python 禁用資料收集

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>驗證和分析資料

您可以選擇首選工具來分析在 Blob 存儲中收集的資料。

### <a name="quickly-access-your-blob-data"></a>快速訪問 Blob 資料

1. 登錄到[Azure 機器學習](https://ml.azure.com)。

1. 開啟工作區。

1. 選取 [**儲存體**]。

    [![選擇"存儲"選項](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 使用此語法遵循 Blob 輸出資料的路徑：

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>使用 Power BI 分析模型資料

1. 下載並打開[Power BI 桌面](https://www.powerbi.com)。

1. 選擇 **"獲取資料**並選擇[**Azure Blob 存儲**](https://docs.microsoft.com/power-bi/desktop-data-sources)"。

    [![電源 BI Blob 設置](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 新增您的儲存體帳戶名稱並輸入您的儲存體金鑰。 您可以通過在 Blob 中選擇 **"設置** > **訪問"鍵**來查找此資訊。

1. 選擇**模型資料**容器並選擇 **"編輯**"。

    [![電源 BI 導航器](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查詢編輯器中，按一下"**名稱**"列並添加存儲帳戶。

1. 將模型路徑輸入篩選器。 如果只想查看特定年份或月份的檔，只需展開篩選器路徑。 例如，要僅查看 3 月資料，請使用此篩選器路徑：

   /模型資料/\<訂閱 id\<>/資源組名稱\<>/工作區名稱\<>/web 服務\<名稱>/型號\<名稱>/模型\<版本>/\<指定>/年份>/3

1. 根據**Name**值篩選與您相關的資料。 如果存儲預測和輸入，則需要為每個預測和輸入創建查詢。

1. 選擇 **"內容**"列標題旁邊的向下雙箭頭以合併檔。

    [![電源 BI 內容](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 選取 [確定]****。 資料預載入。

    [![電源 BI 合併檔](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 選擇 **"關閉並應用**"。

1. 如果添加輸入和預測，則表將自動按**RequestId**值排序。

1. 開始建置模型資料的自訂報告。

### <a name="analyze-model-data-using-azure-databricks"></a>使用 Azure 資料塊分析模型資料

1. 創建[Azure 資料塊工作區](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

1. 移至 Databricks 工作區。

1. 在資料磚塊工作區中，選擇 **"上傳資料**"。

    [![選擇資料磚塊上傳資料選項](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 選擇 **"創建新表"** 並選擇"**其他資料來源** > **Azure Blob 存儲** > **在筆記本中創建表**"。

    [![資料磚表創建](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新資料的位置。 範例如下：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![資料磚設置](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 按照範本上的步驟查看和分析資料。
