---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302066"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>建立用於持續部署的發行管線

在本節中，您會建立發行管線，並將其設定為會在建置管線置放成品時自動執行，且會在 Azure Pipelines 中顯示部署記錄。

建立新的管線，並新增新的階段：

1. 在 [**管線**] 下的 [**發行**] 索引標籤中，選擇 [ **+ 新增管線**] 或者，如果您已經有發行管線，請選擇 [+ 新增]**** 按鈕，然後選取 [+ 新增發行管線]****。  

    ![使用 [+ 新增管線] 按鈕新增發行管線](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. 當系統提示您選取範本時，選擇以 [空白作業]**** 開始。

    ![從您的發行管線的空白作業開始](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. 您的新發行管線會以單一階段初始化，其名為**階段 1**。 將階段1重新命名為開發 **人員** ，並將其視為開發環境的持續部署管線。 持續部署管線通常會有多個階段，包括開發、**預備**和**生產****環境**。您可以使用不同的名稱，並根據您的 DevOps 實務建立更多。 重新命名後，請關閉階段詳細資料視窗。

   您也可以選取頂端的 [新增發行管線] 文字來重新命名您的發行管線。

4. 將版本連結到由建置管線發佈的組建成品。 在成品區域中按一下 [新增]****。

   ![在介面的構件區域中按一下 [新增]](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. 在 [ **新增**成品] 頁面上，選取 [ **組建** ] 作為 **來源類型**。 選擇專案和您建立的組建管線。 如果您想要的話，可以將 **來源別名** 變更為更具描述性的內容。 然後，選取 [ **新增**]。

   ![在 [新增成品] 頁面上，選取 [新增] 以建立構件](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. 開啟成品觸發程序，並選取切換以啟用持續部署觸發程序。 現在，每次有新的可用組建時，系統都會建立新的版本。

   ![開啟成品觸發程式並切換，以啟用持續部署觸發程式](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. **開發**階段已預先設定一項工作和零項工作。 從 [管線] 功能表選取 [工作] **，然後選擇** [ **開發** ] 階段。 選取 **代理程式作業** ，並將其 **顯示名稱** 變更為 [ **QA**]。 您可以設定代理程式作業的詳細資料，但部署工作不區分平臺，因此您可以在所選擇的**代理程式組件**區中使用任何**代理程式規格**。

   ![在 [工作] 索引標籤下，查看開發階段的工作](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. 在 QA 工作上，選取加號 (**+**) 新增兩個工作。 搜尋並新增 **Azure IoT Edge** 兩次。

9. 選取第一個 **Azure IoT Edge** 工作，並使用下列值進行設定：

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 當動作欄位變更時，顯示名稱會自動更新。 |
    | 動作 | 選取 `Generate deployment manifest`。 |
    | 檔案上的 .template.js | 指定路徑： `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` 。 路徑是從組建管線發佈的。 |
    | 預設平臺 | 根據您的目標 IoT Edge 裝置，為您的模組選取適當的作業系統。 |
    | 輸出路徑| 放置路徑 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` 。 此路徑是最終的 IoT Edge 部署資訊清單檔。 |

    這些設定可協助取代檔案中的模組映射 Url `deployment.template.json` 。 **產生部署資訊清單**也有助於將變數取代為您在檔案中所定義的確切值 `deployment.template.json` 。 在 VS/VS Code 中，您會在檔案中指定實際值 `.env` 。 在 Azure Pipelines 中，您可以設定 [ **發行管線變數** ] 索引標籤中的值。移至 [ **變數** ] 索引標籤，並依照下列方式設定名稱和值：

    * **ACR_ADDRESS**：您的 Azure Container Registry **登入伺服器** 值。 您可以在 Azure 入口網站中，從容器登錄的概觀頁面擷取登入伺服器。
    * **ACR_PASSWORD**：您的 Azure Container Registry 密碼。
    * **ACR_USER**：您的 Azure Container Registry 使用者名稱。

    如果您的專案中有其他變數，您可以在此索引標籤中指定名稱和值。 **產生部署資訊清單** 只能辨識變數在類別中 `${VARIABLE}` 。 請確定您在檔案中使用此類別 `*.template.json` 。

    ![在 [變數] 索引標籤中設定發行管線的變數](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. 選取第二個 **Azure IoT Edge** 工作，並使用下列值進行設定：

    | 參數 | 說明 |
    | --- | --- |
    | 顯示名稱 | 當動作欄位變更時，顯示名稱會自動更新。 |
    | 動作 | 選取 `Deploy to IoT Edge devices`。 |
    | 部署檔案 | 放置路徑 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` 。 此路徑是 IoT Edge 部署資訊清單檔的檔案。 |
    | Azure 訂用帳戶 | 選取包含 IoT 中樞的訂用帳戶。|
    | IoT 中樞名稱 | 選取您的 IoT 中樞。|
    | 選擇單一/多部裝置 | 選擇是否要將發行管線部署到一或多個裝置。 如果部署到單一裝置，請輸入 **IoT Edge 裝置識別碼**。 如果要部署到多個裝置，請指定裝置**目標條件**。 目標條件是一個篩選準則，可符合 IoT 中樞內的一組 IoT Edge 裝置。 如果您想要使用裝置標記作為條件，您必須使用 IoT 中樞裝置對應項來更新對應的裝置標記。 在進階設定中更新 [IoT Edge 部署識別碼]**** 和 [IoT Edge 部署優先順序]****。 如需為多個裝置建立部署的詳細資訊，請參閱[了解 IoT Edge 自動部署](../articles/iot-edge/module-deployment-monitoring.md)。 |
    | 裝置識別碼或目標條件 | 視先前的選取專案而定，指定要部署到多個裝置的裝置識別碼或 [目標條件](../articles/iot-edge/module-deployment-monitoring.md#target-condition) 。 |
    | 進階 | 針對 IoT Edge 部署識別碼，指定 `$(System.TeamProject)-$(Release.EnvironmentName)` 。 此變數會將專案和版本名稱對應至您的 IoT Edge 部署識別碼。 |

    ![為您的開發階段新增 Azure IoT Edge 工作](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. 選取 [儲存]**** 以將變更儲存至新的發行管線。 從功能表選取 [ **管線** ] 索引標籤，以返回管線視圖。
