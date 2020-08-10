---
title: 教學課程 - 使用 Azure IoT Edge 將 Azure Machine Learning 部署至裝置
description: 在本教學課程中，您將建立一個 Azure Machine Learning 模型，然後將其當作模組部署至 Edge 裝置
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ae76fab6359675a87ad252a08ebb199bf724f129
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439374"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>教學課程：將 Azure Machine Learning 部署為 IoT Edge 模組 (預覽)

請使用 Azure Notebooks 來開發機器學習模組，並將模組部署至執行 Azure IoT Edge 的 Linux 裝置。
您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您部署 Azure Machine Learning 模組，以根據模擬機器的溫度資料來預測裝置何時會故障。 如需 IoT Edge 上 Azure Machine Learning 的詳細資訊，請參閱 [Azure Machine Learning 文件](../machine-learning/how-to-deploy-and-where.md)。

>[!NOTE]
>Azure IoT Edge 上的 Azure Machine Learning 模組目前為公開預覽版。

您在本教學課程中建立的 Azure Machine Learning 模組會讀取裝置所產生的環境資料，並將訊息標示為異常或非異常。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立 Azure Machine Learning 模組。
> * 將模組容器推送至 Azure Container Registry。
> * 將 Azure Machine Learning 模組部署至 IoT Edge 裝置。
> * 檢視產生的資料。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 快速入門中的步驟，使用 Azure 虛擬機器作為 IoT Edge 裝置。
* Azure Machine Learning 模組不支援 Windows 容器。
* Azure Machine Learning 模組不支援 ARM 處理器。

雲端資源：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* Azure Machine Learning 工作區。 請依照[透過 Azure 入口網站開始使用 Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) 中的指示來建立一個，並了解如何使用它。
  * 記下工作區名稱、資源群組和訂用帳戶識別碼。 這些值全都適用於 Azure 入口網站中的工作區概觀。 您會在本教學課程稍後使用這些值，來將 Azure 筆記本連線至您的工作區資源。

## <a name="create-and-deploy-azure-machine-learning-module"></a>建立並部署 Azure Machine Learning 模組

在此節中，您會將定型的機器學習模型檔案轉換成 Azure Machine Learning 容器。 Docker 映像所需的所有元件都在[適用於 Azure IoT Edge 的 AI 工具組 Git 存放庫](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)中。 請遵循下列步驟，將該存放庫上傳至 Microsoft Azure Notebooks 以建立容器，並將它推送至 Azure Container Registry。

1. 巡覽至您的 Azure Notebooks 專案。 您可以從 [Azure 入口網站](https://portal.azure.com)的 Azure Machine Learning 工作區進入，或使用您的 Azure 帳戶登入至 [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects)。

2. 選取 [上傳 GitHub 存放庫]  。

3. 提供下列 GitHub 存放庫名稱：`Azure/ai-toolkit-iot-edge`。 如果您想要讓專案保持私人狀態，請取消核取 [公用]  方塊。 選取 [匯入]  。

4. 匯入完成之後，巡覽至新的 **ai-toolkit-iot-edge** 專案，然後開啟 **IoT Edge anomaly detection tutorial** 資料夾。

5. 確認您的專案正在執行。 若未執行，請選取 [在免費 Compute 上執行]  。

   ![在免費 Compute 上執行](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. 開啟 **aml_config/config.json** 檔案。

7. 編輯設定檔，以包含您 Azure 訂用帳戶識別碼、訂用帳戶中的資源群組，以及您的 Azure Machine Learning 工作區名稱的值。 您可以在 Azure 中從工作區的 [概觀]  區段取得所有這些值。

8. 儲存組態檔。

9. 開啟 **00-anomaly-detection-tutorial.ipynb** 檔案。

10. 出現提示時，選取 **Python 3.6** 核心，然後選取 [設定核心]  。

11. 根據註解中的指示來編輯筆記本中的第一個資料格。 使用您新增至設定檔的相同資源群組、訂用帳戶識別碼及工作區名稱。

12. 藉由在筆記本中選取儲存格，然後選取 [執行]  或按 `Shift + Enter` 來執行它們。

    >[!TIP]
    >異常偵測教學課程筆記本中的某些資料格是選擇性的，因為它們會建立某些使用者或許具備的資源，例如 IoT 中樞。 如果您將現有的資源資訊放置於第一個資料格，當您執行建立新資源的儲存格時，將會收到錯誤，因為 Azure 將不會建立重複的資源。 這很正常，而您可以忽略錯誤，或完全略過那些選擇性區段。

完成筆記本中的所有步驟之後，您就已將異常偵測模型定型、建置它作為 Docker 容器映像，並將該映像推送至 Azure Container Registry。 接著，測試了模型，最後將它部署到您的 IoT Edge 裝置。

## <a name="view-container-repository"></a>檢視容器存放庫

檢查容器映像是否已成功建立，並儲存於與您機器學習環境相關聯的 Azure Container Registry 中。 您在上一節中使用的筆記本會自動將容器映像及登錄認證提供給您的 IoT Edge 裝置，但您應該知道它們的儲存位置，讓您稍後可自行找到該資訊。

1. 在 [Azure 入口網站](https://portal.azure.com)中，巡覽至您的 Machine Learning 服務工作區。

2. [概觀]  區段會列出工作區詳細資料及其相關聯的資源。 選取 [登錄]  值，此值應該是您工作區名稱，後面接著隨機數字。

3. 在容器登錄中，選取 [服務] 下的 [存放庫]。 您應該會看到稱為 **tempanomalydetection** 的存放庫，此存放庫是您在上一節中執行的筆記本所建立的。

4. 選取 [tempanomalydetection]  。 您應該會看到存放庫有一個標記：**1**。

   既然您已經知道登錄名稱、存放庫名稱和標記，您就會知道容器的完整映像路徑。 映像路徑看起來會類似 **\<registry_name\>.azurecr.io/tempanomalydetection:1**。 您可以使用映像路徑來將此容器部署到 IoT Edge 裝置。

5. 在容器登錄中，選取 [設定] 下的 [存取金鑰]。 您應該會看到數個存取認證，包括**登入伺服器**，以及管理使用者的**使用者名稱**和**密碼**。

   這些認證可以包含於部署資訊清單中，為您的 IoT Edge 裝置提供存取權限來從登錄中提取容器映像。

您現在知道 Machine Learning 容器映像的儲存位置。 下一節將逐步解說如何查看在 IoT Edge 裝置上做為模組執行的容器。

## <a name="view-the-generated-data"></a>檢視所產生的資料

您可以檢視每個 IoT Edge 模組所會產生的訊息，也可檢視傳遞至 IoT 中樞的訊息。

### <a name="view-data-on-your-iot-edge-device"></a>檢視 IoT Edge 裝置上的資料

在 IoT Edge 裝置上，您可以檢視會從每個模組傳來的訊息。

您可能需要針對提升的權限使用 `sudo`，才能執行 `iotedge` 命令。 登出然後再次登入至您的裝置，即會自動更新您的權限。

1. 在 IoT Edge 裝置上檢視所有模組。

   ```cmd/sh
   iotedge list
   ```

2. 檢視會從特定裝置傳來的訊息。 使用前一個命令輸出中的模組名稱。

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>檢視抵達 IoT 中樞的資料

您可以使用[適用於 Visual Studio Code 的 Azure IoT 中樞擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)，檢視 IoT 中樞接收的裝置到雲端訊息。

下列步驟說明如何設定 Visual Studio Code 來監視抵達 IoT 中樞的裝置到雲端訊息。

1. 在 Visual Studio Code 總管中，展開 [Azure IoT 中樞] 區段下的 [裝置] 來查看您的 IoT 裝置清單。

2. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [開始監視內建事件端點]。

3. 每隔五秒觀察來自 tempSensor 的訊息。 訊息內文包含名為 **anomaly** 的屬性，machinelearningmodule 會提供 true 或 false 值。 如果模型已成功執行，**AzureMLResponse** 屬性即會包含 "OK" 值。

   ![訊息本文中的 Azure Machine Learning 回應](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您可以部署受到 Azure Machine Learning 支援的 IoT Edge 模組。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [以自訂視覺服務分類影像](tutorial-deploy-custom-vision.md)
