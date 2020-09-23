---
title: 教學課程：從 Jupyter Notebook 開始 (Python)
titleSuffix: Azure Machine Learning
description: Jupyter Notebook 教學課程的設定。  建立 Azure Machine Learning 工作區、將 Jupyter Notebook 複製到工作區，然後建立用來執行筆記本的計算執行個體。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896908"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>教學課程：在 Jupyter Notebook 中開始使用 Azure Machine Learning

在本教學課程中，您會在[受控雲端式工作站 (計算執行個體)](concept-compute-instance.md) 上，完成透過 Jupyter Notebook 開始使用 Azure Machine Learning 的步驟。 本教學課程是所有其他 Jupyter Notebook 教學課程的先驅。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 [Azure Machine Learning 工作區](concept-workspace.md)，以在其他 Jupyter Notebook 教學課程中使用。
> * 將教學課程 Notebook 複製到工作區中的資料夾。
> * 建立已安裝並預先設定 Azure Machine Learning Python SDK 的雲端式計算執行個體。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> 記下您的**工作區**和**訂用帳戶**。 您會需要這些項目，以確保您在正確位置建立實驗。 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>在您的工作區中執行 Notebook

Azure Machine Learning 會在您的工作區中包含雲端 Notebook 伺服器，讓您擁有免安裝和預先設定的體驗。 如果您想要控制您的環境、套件和相依性，[請使用您自己的環境](tutorial-1st-experiment-sdk-setup-local.md)。

 請依照這段影片的說明，或使用下列詳細步驟，從您的工作區複製並執行教學課程 Notebook。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>複製 Notebook 資料夾

您在 Azure Machine Learning Studio 中完成下列實驗設定及執行步驟，該頁面是統一的介面，為所有技能等級的資料科學從業人員，提供執行資料科學情節的機器學習工具。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取左側的 [筆記本]。

1. 選取頂端的 [範例] 索引標籤。

1. 開啟 **Python** 資料夾。

1. 開啟其上有版本號碼的資料夾。  此號碼代表 Python SDK 的目前版本。

1. 選取 **tutorials** 資料夾右側的 **"..."** ，然後選取 [複製]。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="複製教學課程資料夾":::

1. 資料夾清單隨即顯示，其中顯示每位存取工作區的使用者。  選取您的資料夾，以將 **tutorials** 資料夾複製到該處。

### <a name="open-the-cloned-notebook"></a><a name="open"></a>開啟複製的筆記本

1. 在 [使用者檔案] 區段中開啟剛才關閉的 **tutorials** 資料夾。

    > [!IMPORTANT]
    > 您可以查看 **samples** 資料夾中的 Notebook，但無法從該處執行 Notebook。  若要執行 Notebook，請務必在 [使用者檔案] 區段中開啟複製的 Notebook 版本。
    
1. 在您的 **tutorials/image-classification-mnist-data** 資料夾中選取 **tutorial-1st-experiment-sdk-train.ipynb** 檔案。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="開啟教學課程資料夾":::

1. 在頂端列上，選取要用來執行筆記本的計算執行個體。 這些 VM 已預先設定[執行 Azure Machine Learning 所需的一切](concept-compute-instance.md#contents)。

1. 如果找不到任何 VM，請選取 [+ 新增] 以建立計算執行個體 VM。 

    1. 當您建立 VM 時，請遵循下列規則：  
        + 名稱是必要的，而且不可以空白。
        + 在該工作區/計算執行個體的 Azure 區域中，名稱在其中所有現有計算執行個體中必須是唯一的 (以不區分大小寫的方式)。 如果您選擇的名稱不是唯一的，您將會收到警示。
        + 有效字元是大寫和小寫字母、數字 (0 到 9) 和虛線字元 (-)。
        + 名稱長度必須介於 3 到 24 個字元之間。
        + 名稱應以字母開頭 (不是數字或虛線字元)。
        + 如果使用虛線字元，則虛線後面必須至少接續一個字母。 範例：Test-、test-0、test-01 為無效名稱，而 test-a0、test-0a 才是有效執行個體。

    1.  從可用的選項中選取虛擬機器大小。 在教學課程中，預設 VM 是不錯的選擇。

    1. 然後選取 [建立]。 設定 VM 大約需要 5 分鐘的時間。

1. VM 可供使用後，會顯示在頂端工具列中。  您現在可以使用工具列中的 [全部執行] 來執行 Notebook，或在 Notebook 的程式碼資料格中使用 **Shift+Enter** 來執行。

如果您有自訂小工具，或是您偏好使用 Jupyter/JupyterLab，請選取最右側的 [Jupyter] 下拉式清單，然後選取 [Jupyter] 或 [JupyterLab]。 新的瀏覽器視窗隨即開啟。

## <a name="next-steps"></a>後續步驟

現在您已設定了開發環境，接著請繼續在 Jupyter Notebook 中訓練模型：

> [!div class="nextstepaction"]
> [教學課程：使用 MNIST 資料和 scikit-learn 將影像分類模型定型](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a>如果您不打算現在進行其他教學課程，請在未使用時停止雲端 Notebook 伺服器 VM 來降低成本：

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
