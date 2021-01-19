---
title: 教學課程：對模型進行定型和部署 - Azure IoT Edge 上的 Machine Learning
description: 在本教學課程中，您將使用 Azure Machine Learning 對機器學習模型進行定型，然後將該模型封裝成可部署為 Azure IoT Edge 模組的容器映像。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121123"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教學課程：定型和部署 Azure Machine Learning 模型

在本文中，我們會進行下列工作：

* 使用 Azure Machine Learning Studio 定型機器學習模型。
* 將定型的模型封裝為容器映像。
* 將容器映像部署為 Azure IoT Edge 模組。

您能運用 Azure Machine Learning Studio 進行實驗、定型及部署機器學習模型的基本區塊。

此文章中的步驟通常是由資料科學家執行的。

在教學課程的這一節中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure Machine Learning 工作區中建立 Jupyter Notebook 來定型機器學習模型。
> * 容器化訓練過的機器學習模型。
> * 從容器化機器學習模型建立 Azure IoT Edge 模組。

## <a name="prerequisites"></a>必要條件

此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 本系列中的每篇文章皆以先前文章中的工作為基礎。 如果您是被直接引導至此文章，請參閱本系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="set-up-azure-machine-learning"></a>設定 Azure Machine Learning 

我們會使用 Azure Machine Learning Studio 來裝載兩個 Jupyter 筆記本和支援檔案。 在這裡，我們會建立並設定 Azure Machine Learning 專案。 如果您尚未用過 Jupyter 和/或 Azure Machine Learning Studio，以下是一些入門的文件：

* **Jupyter Notebook：** [在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning：** [在 Jupyter Notebook 中開始使用 Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> 設定之後，便可以從任何電腦存取 Azure Machine Learning 服務。 您在設定期間應使用開發 VM，其具備您所需的所有檔案。

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>安裝 Azure Machine Learning Visual Studio Code 擴充功能
開發 VM 上的 VS Code 應已安裝此延伸模組。 如果您是在不同的執行個體上執行，請如[這裡](../machine-learning/tutorial-setup-vscode-extension.md)所述重新安裝延伸模組。

### <a name="create-an-azure-machine-learning-account"></a>建立 Azure Machine Learning 帳戶  
若要在 Azure 上佈建資源及執行工作負載，您必須使用您的 Azure 帳戶認證登入。

1. 在 Visual Studio Code 中，選取功能表列中的 [檢視] > [命令選擇區]，以開啟命令選擇區。 

1. 在命令選擇區中輸入命令 `Azure: Sign In` ，以開始登入流程。 依照指示完成登入登入。 

1. 建立 Azure ML Compute 執行個體以執行您的工作負載。 使用命令選擇區，輸入命令 `Azure ML: Create Compute`。 
1. 選取您的 Azure 訂用帳戶
1. 選取 [+ 建立新的 Azure ML 工作區] 再輸入名稱 `turbofandemo`。
1. 選取您在本示範中使用的資源群組。
1. 您應該能夠在 VS Code 視窗的右下角查看工作區建立的進度：**建立工作區：turobofandemo** (可能需要一或兩分鐘)。 
1. 請等候工作區建立成功。 畫面應該會顯示 **已建立 Azure ML 工作區 turbofandemo**。


### <a name="upload-jupyter-notebook-files"></a>上傳 Jupyter Notebook 檔案

我們會將範例筆記本檔案上傳到新的 Azure ML 工作區。

1. 瀏覽至 ml.azure.com 並登入。
1. 選取您的 Microsoft 目錄、Azure 訂用帳戶和新建立的 Azure ML 工作區。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="選取您的 Azure ML 工作區。" :::

1. 登入您的 Azure ML 工作區後，請使用左側功能表瀏覽至 **Notebooks** 區段。
1. 選取 **我的檔案** 索引標籤。

1. 選取 [上傳] (向上箭號圖示) 


1. 瀏覽至 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 選取清單中的所有檔案，然後按一下 [開啟]  。

1. 勾選 [我信任這些檔案的內容]  方塊。

1. 選取 [上傳]  以開始上傳，然後在程序完成時選取 [完成]  。

### <a name="jupyter-notebook-files"></a>Jupyter Notebook 檔案

請檢閱您上傳到 Azure ML 工作區中的檔案。 本教學課程這個部分的活動橫跨兩個使用一些支援檔案的筆記本檔案。

* **01-turbofan\_regression.ipynb：** 此筆記本使用 Machine Learning 服務工作區來建立及執行機器學習實驗。 此筆記本大致會執行下列步驟：

  1. 從裝置控管所產生的 Azure 儲存體帳戶下載資料。
  1. 探索並準備資料，然後使用資料來定型分類器模型。
  1. 使用測試資料集 (Test\_FD003.txt) 從實驗評估模型。
  1. 將最佳分類器模型發佈至 Machine Learning 服務工作區。

* **02-turbofan\_deploy\_model.ipynb：** 此筆記本會採用在上一個筆記本中所建立的模型，並用它來建立已準備好部署至 Azure IoT Edge 裝置的容器映像。 此筆記本會執行下列步驟：

  1. 為模型建立評分指令碼。
  1. 使用儲存在 Machine Learning 服務工作區中的分類器模型來產生容器映像。
  1. 在 Azure 容器執行個體上將該映像部署為 Web 服務。
  1. 使用 Web 服務來驗證模型及映像是否能如預期般運作。 已驗證的映像將會在此教學課程的[建立和部署自訂 IoT Edge 模組](tutorial-machine-learning-edge-06-custom-modules.md)部分中，部署至我們的 IoT Edge 裝置。

* **Test\_FD003.txt：** 此檔案包含我們將在驗證已定型分類器時作為測試集使用的資料。 為求簡化，我們選擇使用針對原始競賽所提供的測試資料作為測試集。

* **RUL\_FD003.txt：** 此檔案包含 Test\_FD003.txt 檔案中每個裝置的最後一個週期的剩餘使用年限 (RUL)。 請參閱 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 readme.txt 和 Damage Propagation Modeling.pdf 檔案，以取得該資料的詳細說明。

* **Utils.py：** 包含用來處理資料的 Python 公用程式函式。 第一個筆記本包含這些函式的詳細說明。

* **README.md：** 描述筆記本用途的讀我檔案。  

## <a name="run-jupyter-notebooks"></a>執行 Jupyter Notebook

您現已建立好工作區，因此可以接著執行筆記本。 

1. 從 **我的檔案** 頁面選取 **01-turbofan\_regression.ipynb**。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="選取要執行的第一個筆記本。":::

1. 如果該筆記本被列為 [不受信任]  ，請按一下位於筆記本右上方的 [不受信任]  小工具。 出現對話方塊時，請選取 [信任]  。

1. 為了獲得最佳結果，請參閱每個資料格的文件，並個別加以執行。 選取工具列上的 [執行]  。 之後，您會發現執行多個資料格較為方便。 您可以忽略升級和淘汰的警告。

    資料格在執行時會顯示以方括號括住的星號 ([\*])。 當資料格的作業完成時，星號會以數字取代，並可能會出現相關輸出。 筆記本中的資料格會循序建置，而且一次只能執行一個。

    您也可以從 [資料格]  功能表來使用執行選項、使用 `Ctrl` + `Enter` 來執行資料格，以及使用 `Shift` + `Enter` 來執行資料格並前進到下一個資料格。

    > [!TIP]
    > 為了讓資料格作業保持一致，請避免在瀏覽器中從多個索引標籤執行相同的筆記本。

1. 在 [設定全域屬性]  指示後面的資料格中，寫入您的 Azure 訂用帳戶、設定和資源的值。 然後，執行資料格。

    ![在筆記本中設定全域屬性](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. 在 [工作區詳細資料]  前面的資料格中，在資料格執行後，尋找指示您登入以進行驗證的連結：

    ![裝置驗證的登入提示](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    開啟連結並輸入指定驗證碼。 此登入程序會使用 Microsoft Azure 跨平台命令列介面，驗證 Jupyter 筆記本以存取 Azure 資源。  

    ![在裝置確認時驗證應用程式](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. 在 [瀏覽結果]  前面的資料格中，複製執行識別碼中的值，然後將其貼到 [重新組成執行]  後面的資料格中包含的執行識別碼。

   ![在資料格之間複製執行識別碼](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. 執行筆記本中其餘的資料格。

1. 儲存筆記本並返回您的專案頁面。

1. 開啟 **02-turbofan\_deploy\_model.ipynb**，並執行每個資料格。 您必須先登入，以在 [設定工作區]  後面的資料格中進行驗證。

1. 儲存筆記本並返回您的專案頁面。

### <a name="verify-success"></a>確認是否成功

若要確認筆記本是否已成功完成，請確認其中是否已建立幾個項目。

1. 在 Azure ML Notebooks **我的檔案** 索引標籤上，選取 [重新整理]。

1. 確認是否已建立下列檔案：

    | 檔案 | 描述 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | 用來建立 Azure Machine Learning 工作區的設定檔。 |
    | ./aml_config/model_config.json | 我們需要在 Azure 中的 **turbofanDemo** Machine Learning 工作區中部署模型的設定檔。 |
    | myenv.yml| 提供所部署 Machine Learning 模型相依性的相關資訊。|

1. 確認下列 Azure 資源已建立。 某些資源名稱會附加隨機字元。

    | Azure 資源 | 名稱 |
    | --- | --- |
    | 機器學習服務工作區 | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | 儲存體 | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>偵錯

您可以將 Python 陳述式插入至筆記本以進行偵錯，例如使用 `print()` 命令來顯示值。 如果您看到未定義的變數或物件，請執行其首次宣告或具現化時所在的資料格。

如果需要重做筆記本，您可能必須刪除先前建立的檔案和 Azure 資源。

## <a name="clean-up-resources"></a>清除資源

本教學課程是集合的一部分，其中每篇文章都會以上一篇文章中所完成的工作為基礎。 請等到您完成最後一個教學課程後，再清除任何資源。

## <a name="next-steps"></a>後續步驟

在此文章中，我們已使用在 Azure ML Studio 中執行的兩個 Jupyter 筆記本，來使用渦輪風扇裝置中的檔案對剩餘使用年限 (RUL) 分類器進行定型、將分類器儲存為模型、建立容器映像，以及將映像測試及部署為 Web 服務。

請前往下一篇文章以建立 IoT Edge 裝置。

> [!div class="nextstepaction"]
> [設定 IoT Edge 裝置](tutorial-machine-learning-edge-05-configure-edge-device.md)