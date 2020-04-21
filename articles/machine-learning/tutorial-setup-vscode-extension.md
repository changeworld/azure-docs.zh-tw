---
title: 教學課程：設定 Visual Studio Code 擴充功能
titleSuffix: Azure Machine Learning
description: 了解如何設定 Visual Studio Code Azure Machine Learning 擴充功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272899"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>設定 Azure Machine Learning Visual Studio Code 擴充功能

了解如何使用 Azure Machine Learning Visual Studio Code 擴充功能來安裝和執行指令碼。

在本教學課程中，您會了解下列工作：

> [!div class="checklist"]
> * 安裝 Azure Machine Learning Visual Studio Code 擴充功能
> * 從 Visual Studio Code 登入您的 Azure 帳戶
> * 使用 Azure Machine Learning 擴充功能來執行範例指令碼

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有訂用帳戶，請註冊以[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
- Visual Studio Code。 如果沒有 Visual Studio Code，請[安裝](https://code.visualstudio.com/docs/setup/setup-overview)。
- [Python 3](https://www.python.org/downloads/) \(英文\)

## <a name="install-the-extension"></a>安裝延伸模組

1. 開啟 Visual Studio Code。
1. 從 [活動列]  中選取 [擴充功能]  圖示，以開啟 [擴充功能] 檢視。
1. 在 [擴充功能] 檢視中，搜尋「Azure Machine Learning」。
1. 選取 [安裝]  。

    > [!div class="mx-imgBorder"]
    > ![安裝 Azure Machine Learning VS Code 擴充功能](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> 或者，您可以[直接下載安裝程式](https://aka.ms/vscodetoolsforai)，以透過 Visual Studio Marketplace 安裝 Azure Machine Learning 擴充功能。 

本教學課程中的其餘步驟都已使用 **0.6.8 版**的擴充功能進行測試。

## <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

若要在 Azure 上佈建資源及執行工作負載，您必須使用您的 Azure 帳戶認證登入。 為了協助管理帳戶，Azure Machine Learning 會自動安裝 Azure 帳戶擴充功能。 請造訪下列網站，[深入了解 Azure 帳戶擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。

1. 從功能表列中選取 [檢視] > [命令選擇區]  ，以開啟命令選擇區。 
1. 在文字方塊中輸入命令 "Azure:Sign In" 以開始登入流程。

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>在 Azure 中執行機器學習模型訓練指令碼

既然您已使用帳戶憑證登入 Azure，請使用本節中的步驟來了解如何使用此擴充功能來定型機器學習模型。

1. 下載 [VS Code Tools for AI 存放庫](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)，並將其解壓縮到您電腦上的任何位置。
1. 在 Visual Studio Code 中開啟 `mnist-vscode-docs-sample` 目錄。
1. 選取活動列中的 **Azure** 圖示。
1. 選取 Azure Machine Learning 檢視頂端的 [執行實驗]  圖示。

    > [!div class="mx-imgBorder"]
    > ![執行實驗](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. 當命令選擇區展開時，請遵循提示。

    1. 選取 Azure 訂用帳戶。
    1. 從環境清單中選取 [Conda 相依性檔案]  。
    1. 按 [輸入]  以瀏覽 Conda 相依性檔案。 此檔案包含執行指令碼所需的相依性。 在此情況下，相依性檔案是 `mnist-vscode-docs-sample` 目錄中的 `env.yml` 檔案。
    1. 按 [輸入]  以瀏覽訓練指令檔。 這個檔案包含機器學習模型的程式碼，可用於將手寫數字的影像分類。 在此情況下，用來定型模型的指令碼是 `mnist-vscode-docs-sample` 目錄內的 `train.py` 檔案。

1. 此時，文字編輯器中會顯示類似下面的組態檔。 組態包含執行訓練作業所需的資訊，例如檔案，其中包含用來定型模型的程式碼，以及在上一個步驟中指定的任何 Python 相依性。

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. 對組態感到滿意後，請開啟命令選擇區並輸入下列命令，以提交您的實驗：

    ```text
    Azure ML: Submit Experiment
    ```

    這會將 `train.py` 和組態檔傳送至您的 Azure Machine Learning 工作區。 然後會在 Azure 中的計算資源上啟動訓練作業。

### <a name="track-the-progress-of-the-training-script"></a>追蹤定型指令碼的進度

指令碼可能需要幾分鐘的時間來執行。 若要追蹤進度：

1. 選取活動列中的 **Azure** 圖示。
1. 展開您的訂用帳戶節點。
1. 展開目前執行的實驗節點。 這位在 `{workspace}/Experiments/{experiment}` 節點中，其中您的工作區和實驗的值與組態檔中所定義的屬性相同。
1. 此處會列出實驗的所有執行，以及其狀態。 若要取得最近的狀態，請按一下 Azure Machine Learning 檢視頂端的 [重新整理] 圖示。

    > [!div class="mx-imgBorder"]
    > ![追蹤實驗進度](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>下載已定型的模型

實驗執行完成時，會輸出已定型的模型。 若要在本機下載輸出：

1. 以滑鼠右鍵按一下最近的執行，然後選取 [下載輸出]  。

    > [!div class="mx-imgBorder"]
    > ![下載已定型的模型](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. 選取要儲存輸出的位置。
1. 系統會在本機下載含有您的執行名稱的資料夾。 瀏覽到該頁面。
1. 模型檔案位於 `outputs/outputs/model` 目錄內。

## <a name="next-steps"></a>後續步驟

* [教學課程：使用 Azure Machine Learning Visual Studio Code 擴充功能來定型和部署影像分類 TensorFlow 模型](tutorial-train-deploy-image-classification-model-vscode.md)。
