---
title: 教學課程：設定 Azure Machine Learning Visual Studio Code 擴充功能
titleSuffix: Azure Machine Learning
description: 了解如何設定 Visual Studio Code Azure Machine Learning 擴充功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157459"
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
- 安裝 [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)，這是輕量型、跨平台的程式碼編輯器。 

## <a name="install-the-extension"></a>安裝延伸模組

1. 開啟 Visual Studio Code。
1. 從 [活動列]  中選取 [擴充功能]  圖示，以開啟 [擴充功能] 檢視。
1. 在 [擴充功能] 檢視中，搜尋「Azure Machine Learning」。
1. 選取 [安裝]  。

> [!NOTE]
> 或者，您可以[直接下載安裝程式](https://aka.ms/vscodetoolsforai)，以透過 Visual Studio Marketplace 安裝 Azure Machine Learning 擴充功能。 

本教學課程中的其餘步驟都已使用 **0.6.8 版**的擴充功能進行測試。

## <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶

若要在 Azure 上佈建資源及執行工作負載，您必須使用您的 Azure 帳戶認證登入。 為了協助管理帳戶，Azure Machine Learning 會自動安裝 Azure 帳戶擴充功能。 請造訪下列網站，[深入了解 Azure 帳戶擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。

1. 從功能表列中選取 [檢視] > [命令選擇區]  ，以開啟命令選擇區。 
1. 在文字方塊中輸入命令 "Azure:Sign In" 以開始登入程序。

## <a name="run-a-script-in-azure"></a>在 Azure 中執行指令碼

既然您已使用帳戶憑證登入 Azure，請使用本節中的步驟來了解如何使用此擴充功能來定型機器學習模型。

1. 下載 [VS Code Tools for AI 存放庫](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)，並將其解壓縮到您電腦上的任何位置。
1. 在 Visual Studio Code 中開啟 `mnist-vscode-docs-sample` 目錄。
1. 選取活動列中的 **Azure** 圖示。
1. 選取 Azure Machine Learning 檢視頂端的 [執行實驗]  圖示。

    > [!div class="mx-imgBorder"]
    > ![執行實驗](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. 當命令選擇區展開時，請遵循提示。

    1. 選取 Azure 訂用帳戶。
    1. 選取 [建立新的 Azure ML 工作區]  。
    1. 選取 [TensorFlow 單一節點訓練]  作業類型。
    1. 輸入 `train.py` 作為要訓練的指令碼。 這個檔案包含機器學習模型的程式碼，可用於將手寫數字的影像分類。
    1. 將下列套件指定為要執行的需求。

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. 此時，文字編輯器中會顯示類似下面的組態檔。 組態包含執行訓練作業所需的資訊，例如檔案，其中包含用來定型模型的程式碼，以及在上一個步驟中指定的任何 Python 相依性。

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. 選取 [提交實驗]  以在 Azure 中執行您的實驗。 這會將 `train.py` 和組態檔傳送至您的 Azure Machine Learning 工作區。 然後會在 Azure 中的計算資源上啟動訓練作業。
1. 幾分鐘後，就會在本機建立名為 `output` 的目錄，其中包含定型的 TensorFlow 模型。

## <a name="next-steps"></a>後續步驟

* [教學課程：使用 Azure Machine Learning Visual Studio Code 擴充功能來定型和部署影像分類 TensorFlow 模型](tutorial-train-deploy-image-classification-model-vscode.md)。
