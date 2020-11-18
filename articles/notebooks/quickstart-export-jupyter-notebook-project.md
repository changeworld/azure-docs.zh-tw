---
title: 從 Azure Notebooks 預覽版匯出 Jupyter Notebook 專案
description: 快速匯出 Jupyter Notebook 專案。
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ea16bd61b542217fb6f2d5ba1d926a0bc19d4cce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844415"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>在 Azure Notebooks 預覽版中匯出 Jupyter Notebook 專案

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

在本快速入門中，您將下載 Azure Notebooks 專案來用於其他 Jupyter Notebook 解決方案。 

## <a name="prerequisites"></a>先決條件：

現有的 Azure Notebooks 專案。

## <a name="export-an-azure-notebooks-project"></a>匯出 Azure Notebooks 專案

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 如需詳細資訊，請參閱[快速入門 - 登入 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)。

1. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]：

    ![瀏覽器視窗頂端的 [我的專案] 連結](media/quickstarts/my-projects-link.png)

1. 選取專案。
1. 按一下 [下載] 按鈕以觸發 zip 檔案下載，該檔案中包含您所有的專案檔。
1. 或者，從特定專案頁面按一下 [下載專案] 按鈕，以下載指定專案的所有檔案。

下載您的專案檔之後，您可以將其與其他 Jupyter Notebook 解決方案搭配使用。 下列各節所述的一些選項包括： 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure 實驗室服務](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>建立筆記本的環境

如果您想要建立符合 Azure Notebooks 預覽版的環境，您可以使用 GitHub 中提供的指令檔。

1. 瀏覽至 Azure Notebooks [GitHub 存放庫](https://github.com/microsoft/AzureNotebooks)，或[直接存取環境資料夾](https://aka.ms/aznbrequirementstxt)。
1. 從命令提示字元中，瀏覽至您要用於專案的目錄。
1. 下載環境資料夾內容，並遵循「讀我檔案」的指示來安裝 Azure Notebooks 套件相依性。


## <a name="use-notebooks-in-visual-studio-code"></a>在 Visual Studio Code 中使用 Notebook

[VS Code](https://code.visualstudio.com/) 是免費的程式碼編輯器，您可以在本機使用或將其連線到遠端計算。 其結合了 Python 延伸模組，可提供完整的 Python 開發環境，包括使用 Jupyter Notebook 的豐富原生體驗。 

![VS Code Jupyter Notebook 支援](media/vs-code-jupyter-notebook.png)

[下載](#export-an-azure-notebooks-project)您的專案檔之後，您就可以將其與 VS Code 搭配使用。 如需搭配 Jupyter Notebook 使用 VS Code 的指引，請參閱教學課程：[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support) 和 [Visual Studio Code 中的資料科學](https://code.visualstudio.com/docs/python/data-science-tutorial)。

您也可以搭配 Visual Studio Code 使用 [Azure Notebooks 環境指令碼](#create-an-environment-for-notebooks)來建立符合 Azure Notebooks 預覽版的環境。

## <a name="use-notebooks-in-github-codespaces"></a>在 GitHub Codespaces 中使用筆記本

GitHub Codespaces 提供雲端託管環境，您可以在其中使用 Visual Studio Code 或網頁瀏覽器來編輯筆記本。 其提供與 VS Code 相同的絕佳 Jupyter 體驗，但無須在您的裝置上安裝任何項目。 如果您不想要設定本機環境，而且偏好使用雲端支援的解決方案，則建立程式碼空間是很好的選擇。 開始進行之前：
1. [下載](#export-an-azure-notebooks-project)您的專案檔。
1. [建立用來儲存筆記本的 GitHub 存放庫](https://help.github.com/github/getting-started-with-github/create-a-repo)。   
1. [將您的檔案新增](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)至存放庫。
1. [要求存取 GitHub Codespaces 預覽版](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>搭配 Azure Machine Learning 使用筆記本

Azure Machine Learning 提供端對端機器學習平台，讓使用者能夠在 Azure 上更快速地建置和部署模型。 Azure ML 可讓您在 VM 或共用叢集計算環境上執行 Jupyter Notebook。 如果您需要雲端式解決方案來處理實驗追蹤和資料集管理等 ML 工作負載，建議您使用 Azure Machine Learning。 若要開始使用 Azure ML：

1. [下載](#export-an-azure-notebooks-project)您的專案檔。
1. 在 Azure 入口網站中[建立工作區](../machine-learning/how-to-manage-workspace.md)。

   ![建立工作區](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. 開啟 [Azure Studio (預覽)](https://ml.azure.com/)。
1. 使用左側導覽列，選取 [筆記本]。
1. 按一下 [上傳檔案] 按鈕，然後上傳您從 Azure Notebooks 下載的專案檔。

如需 Azure ML 和執行 Jupyter Notebook 的詳細資訊，您可以參閱[此文件](../machine-learning/how-to-run-jupyter-notebooks.md)，或嘗試進行 Microsoft Learn 上的[機器學習簡介](/learn/modules/intro-to-azure-machine-learning-service/)課程模組。


## <a name="use-azure-lab-services"></a>使用 Azure 實驗室服務

[Azure 實驗室服務](https://azure.microsoft.com/services/lab-services/)可讓教育者輕鬆地進行設定，並且為整個教室提供預設 VM 的隨選存取權。 如果您想在身打造的教室環境中使用 Jupyter Notebook 和雲端計算，實驗室服務是很好的選擇。

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 [下載](#export-an-azure-notebooks-project)您的專案檔之後，您就可以將其與 Azure 實驗室服務搭配使用。 如需設定實驗室的相關指引，請參閱[設定實驗室以使用 Python 和 Jupyter Notebook 來教授資料科學](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>使用 GitHub

GitHub 提供免費的原始檔控制支援方式，可用來儲存筆記本 (和其他檔案)、與其他人共用您的筆記本並共同作業。 如果您要與他人共用專案並共同作業，GitHub 是一個絕佳的選擇，並且可以結合 [GitHub Codespaces](#use-notebooks-in-github-codespaces) 來獲得絕佳的開發體驗。 若要開始使用 GitHub

1. [下載](#export-an-azure-notebooks-project)您的專案檔。
1. [建立用來儲存筆記本的 GitHub 存放庫](https://help.github.com/github/getting-started-with-github/create-a-repo)。 
1. [將您的檔案新增](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)至存放庫。

## <a name="next-steps"></a>後續步驟

- [了解 Visual Studio Code 中的 Python](https://code.visualstudio.com/docs/python/python-tutorial)
- [了解 Azure Machine Learning 和 Jupyter Notebook](../machine-learning/how-to-run-jupyter-notebooks.md)
- [了解 GitHub Codespaces](https://github.com/features/codespaces)
- [了解 Azure 實驗室服務](https://azure.microsoft.com/services/lab-services/)
- [了解 GitHub](https://help.github.com/github/getting-started-with-github/)