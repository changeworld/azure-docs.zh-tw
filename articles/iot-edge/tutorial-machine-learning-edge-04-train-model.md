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
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236037"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教學課程：定型和部署 Azure Machine Learning 模型

> [!NOTE]
> 此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 如果您是被直接引導至此文章，我們建議您先從本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)開始，以取得最佳成效。

在本文中，我們會進行下列工作：

* 使用 Azure Notebooks 來定型機器學習模型。
* 將定型的模型封裝為容器映像。
* 將容器映像部署為 Azure IoT Edge 模組。

Azure Notebooks 能運用 Azure Machine Learning 工作區，其為用來對機器學習模型進行實驗、定型及部署的基本區塊。

此文章中的步驟通常是由資料科學家執行的。

## <a name="set-up-azure-notebooks"></a>設定 Azure Notebooks

我們會使用 Azure Notebooks 來裝載兩個 Jupyter 筆記本和支援檔案。 在這裡，我們會建立並設定 Azure Notebooks 專案。 如果您尚未用過 Jupyter 和/或 Azure Notebooks，以下是一些入門的文件：

* **快速入門：** [建立及共用筆記本](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **教學課程：** [使用 Python 建立和執行 Jupyter Notebook](../notebooks/tutorial-create-run-jupyter-notebook.md)

使用 Azure 筆記本可確保練習所在的環境保持一致。

> [!NOTE]
> 設定之後，便可以從任何電腦存取 Azure Notebooks 服務。 您在設定期間應使用開發 VM，其具備您所需的所有檔案。

### <a name="create-an-azure-notebooks-account"></a>建立 Azure Notebooks 帳戶

若要使用 Azure Notebooks，您必須建立一個帳戶。 Azure Notebooks 帳戶是獨立於 Azure 訂用帳戶。

1. 瀏覽至 [Azure Notebooks](https://notebooks.azure.com) \(英文\)。

1. 按一下頁面右上角的 [登入]  。

1. 使用您的公司或學校帳戶 (Azure Active Directory) 或個人帳戶 (Microsoft 帳戶) 來登入。

1. 如果您從未使用過 Azure Notebooks，系統將會提示您為 Azure Notebooks 應用程式授與權限。

1. 建立適用於 Azure Notebooks 的使用者識別碼。

### <a name="upload-jupyter-notebook-files"></a>上傳 Jupyter 筆記本檔案

我們會將範例筆記本檔案上傳到新的 Azure Notebooks 專案。

1. 從新帳戶的 [使用者] 頁面，選取頂端功能表列中的 [我的專案]  。

1. 選取 **+** 按鈕以新增專案。

1. 在 [建立新專案]  對話方塊中，提供 [專案名稱]  。 

1. 因為不需要公開專案或具有讀我檔案，請讓 [公用]  和 [讀我檔案]  保持未核取狀態。

1. 選取 [建立]  。

1. 選取 [上傳]  (向上箭號圖示)，然後選擇 [從電腦]  。

1. 選取 [選擇檔案]  。

1. 瀏覽至 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 選取清單中的所有檔案，然後按一下 [開啟]  。

1. 勾選 [我信任這些檔案的內容]  方塊。

1. 選取 [上傳]  以開始上傳，然後在程序完成時選取 [完成]  。

### <a name="azure-notebook-files"></a>Azure 筆記本檔案

請檢閱您上傳到 Azure Notebooks 專案中的檔案。 本教學課程這個部分的活動橫跨兩個使用一些支援檔案的筆記本檔案。

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

## <a name="run-azure-notebooks"></a>執行 Azure Notebooks

您現已建立好專案，因此可以接著執行筆記本。 

1. 從專案頁面選取 **01-turbofan\_regression.ipynb**。

    ![選取要執行的第一個筆記本](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

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

1. 在 Azure Notebooks 的專案頁面上，選取 [顯示隱藏項目]  ，讓以句點開頭的項目名稱出現。

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

## <a name="next-steps"></a>後續步驟

在此文章中，我們已使用在 Azure Notebooks 中執行的兩個 Jupyter 筆記本，來使用渦輪風扇裝置中的檔案對剩餘使用年限 (RUL) 分類器進行定型、將分類器儲存為模型、建立容器映像，以及將映像測試及部署為 Web 服務。

請前往下一篇文章以建立 IoT Edge 裝置。

> [!div class="nextstepaction"]
> [設定 IoT Edge 裝置](tutorial-machine-learning-edge-05-configure-edge-device.md)
