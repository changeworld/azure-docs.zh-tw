---
title: 建立和管理 VS Code 延伸模組的資源（預覽）
titleSuffix: Azure Machine Learning
description: 使用 VS Code 延伸模組來建立和管理資源
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 07/09/2020
ms.openlocfilehash: 5ec6af894f434f8d1f7df34b2ed0c2514ab88cc4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87306140"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>使用 VS Code 延伸模組來管理 Azure Machine Learning 資源（預覽）

瞭解如何使用 VS Code 延伸模組來管理 Azure Machine Learning 資源。

![Azure Machine Learning VS Code 延伸模組](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有訂用帳戶，請註冊以[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
- Visual Studio Code。 如果沒有 Visual Studio Code，請[安裝](https://code.visualstudio.com/docs/setup/setup-overview)。
- VS Code Azure Machine Learning 延伸模組。 遵循[Azure Machine Learning VS Code 延伸模組安裝指南](tutorial-setup-vscode-extension.md#install-the-extension)來安裝延伸模組。

下列所有程式都假設您位於 Visual Studio Code 的 [Azure Machine Learning] 視圖中。 若要啟動擴充功能，請選取 [VS Code] 活動列中的 [ **Azure** ] 圖示。

## <a name="workspaces"></a>工作區

如需詳細資訊，請參閱[工作區](concept-workspace.md)。

### <a name="create-a-workspace"></a>建立工作區

1. 在 Azure Machine Learning 視圖中，以滑鼠右鍵按一下您的訂用帳戶節點，然後選取 [**建立工作區**]。
1. 在提示字元中：
    1. 提供工作區的名稱
    1. 選擇 Azure 訂用帳戶
    1. 選擇或建立新的資源群組，以在其中布建工作區
    1. 選取要布建工作區的位置。
    1. 選擇 [*基本*] 和 [*企業*版]。 深入瞭解不同的[Azure Machine Learning 版本](concept-editions.md)。

建立工作區的替代方法包括：

- 按一下 `+` [Azure Machine Learning] 視圖頂端的圖示。
- 當系統提示您在布建其他資源期間選取工作區時，請建立新的工作區。

### <a name="remove-a-workspace"></a>移除工作區

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 以滑鼠右鍵按一下您要移除的工作區。
1. 選取您是否要移除：
    - *只有工作區*：此選項**只**會刪除工作區 Azure 資源。 資源群組、儲存體帳戶和工作區所連接的任何其他資源仍在 Azure 中。
    - *使用相關聯的資源*：此選項會刪除工作區**和**所有與其相關聯的資源。

## <a name="datastores"></a>資料存放區

VS Code 延伸模組目前支援下列類型的資料存放區：

- Azure 檔案共用
- Azure Blob 儲存體

當您建立工作區時，系統會為每個類型建立資料存放區。

如需詳細資訊，請參閱[資料存放區](concept-data.md#datastores)。

### <a name="create-a-datastore"></a>建立資料存放區

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您要在其下建立資料存放區的工作區節點。
1. 以滑鼠右鍵按一下 [**資料存放區**] 節點，然後選取 [**註冊資料**存放區]。
1. 在提示字元中：
    1. 提供資料存放區的名稱。
    1. 選擇資料存放區類型。
    1. 選取您的儲存體資源。 您可以選擇與您的工作區相關聯的儲存體資源，或從 Azure 訂用帳戶中的任何有效儲存體資源中選取。
    1. 選擇您的資料位於先前選取的儲存體資源內的容器。
1. 設定檔會出現在 VS Code 中。 如果您對設定檔感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="manage-a-datastore"></a>管理資料存放區

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 展開工作區中的 [**資料存放區**] 節點。
1. 選取您想要的資料存放區：
    - *設定為預設值*。 每當您執行實驗時，這就是將使用的資料存放區。
    - *檢查唯讀設定*。
    - *修改*。 變更驗證類型和認證。 支援的驗證類型包含帳戶金鑰和 SAS 權杖。

## <a name="datasets"></a>資料集

延伸模組目前支援下列資料集類型：

- *表格式*：可讓您將資料具體化為數據框架（Pandas 或 PySpark）。
- *File*：檔案或檔案的集合。 可讓您將檔案下載或掛接至您的計算。

如需詳細資訊，請參閱[資料集](concept-data.md#datasets)

### <a name="create-dataset"></a>建立資料集

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您要在其下建立資料存放區的工作區節點。
1. 以滑鼠右鍵按一下 [**資料集**] 節點，然後選取 [**建立資料集**]。
1. 在提示字元中：
    1. 選擇資料集類型
    1. 定義資料是否位於您的電腦或網路上
    1. 提供資料的位置。 這可以是單一檔案或包含資料檔案的目錄。
    1. 選擇您想要上傳資料的資料存放區。
    1. 提供可協助您在資料存放區中識別資料集的前置詞。

### <a name="version-datasets"></a>版本資料集

建立機器學習模型時，當資料變更時，您可能會想要將資料集設為版本。 若要在 VS Code 延伸模組中這麼做：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 展開 [**資料集**] 節點。
1. 以滑鼠右鍵按一下您想要版本的資料集，然後選取 [**建立新版本**]。
1. 在提示字元中：
    1. 選取資料集類型
    1. 定義資料是否位於您的電腦或網路上。
    1. 提供資料的位置。 這可以是單一檔案或包含資料檔案的目錄。
    1. 選擇您想要上傳資料的資料存放區。
    1. 提供可協助您在資料存放區中識別資料集的前置詞。

### <a name="view-dataset-properties"></a>查看資料集屬性

此選項可讓您查看與特定資料集相關聯的中繼資料。 若要在 VS Code 延伸模組中這麼做：

1. 展開您的工作區節點。
1. 展開 [**資料集**] 節點。
1. 以滑鼠右鍵按一下您要檢查的資料集，然後選取 [**查看資料集屬性**]。 這會顯示具有最新資料集版本屬性的設定檔。

> [!NOTE]
> 如果您有多個版本的資料集，您可以選擇只查看特定版本的資料集屬性，方法是展開 [資料集] 節點，並執行本節中所述版本的相同步驟。

### <a name="unregister-datasets"></a>取消註冊資料集

若要移除資料集及其所有版本，請將其取消註冊。 若要在 VS Code 延伸模組中這麼做：

1. 展開您的工作區節點。
1. 展開 [**資料集**] 節點。
1. 以滑鼠右鍵按一下您要取消註冊的資料集，然後選取 [**取消註冊資料集**]。

## <a name="environments"></a>環境

如需詳細資訊，請參閱[環境](concept-environments.md)。

### <a name="create-environment"></a>建立環境

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您要在其下建立資料存放區的工作區節點。
1. 以滑鼠右鍵按一下 [**環境**] 節點，然後選取 [**建立環境**]。
1. 在提示字元中：
    1. 為您的環境提供名稱
    1. 定義您的環境設定：
        - *策劃環境*： Azure Machine Learning 中預先設定的環境。 您可以藉由修改 JSON 檔案中的屬性，進一步自訂環境 `dependencies` 。 深入瞭解[策劃環境](resource-curated-environments.md)。
        - *Conda*相依性檔案：針對 Anaconda 環境，可以提供包含您環境定義的檔案。
        - *Pip 需求*檔案：對於 pip 環境，可以提供包含您環境定義的檔案。
        - *現有的 Conda 環境*：此選項會在您的本機電腦中尋找 Conda 環境，並嘗試從選取的環境中建立環境。
        - *自訂*：定義您自己的通道和相依性
    1. 隨即會在編輯器中開啟設定檔。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="view-environment-configurations"></a>查看環境設定

若要在擴充功能中查看特定環境的相依性和設定：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 展開 [**環境**] 節點。
1. 以滑鼠右鍵按一下您想要查看的環境，然後選取 [ **View 環境**]。

### <a name="edit-environment-configurations"></a>編輯環境設定

若要編輯擴充功能中特定環境的相依性和設定：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**環境**] 節點。
1. 以滑鼠右鍵按一下您想要查看的環境，然後選取 [**編輯環境**]。
1. 進行修改後，如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 [VS Code 命令選擇區] （**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

## <a name="experiments"></a>實驗

如需詳細資訊，請參閱[實驗](concept-azure-machine-learning-architecture.md#experiments)。

### <a name="create-experiment"></a>建立實驗

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 以滑鼠右鍵按一下工作區中的 [**實驗**] 節點，然後選取 [**建立實驗**]。
1. 在提示中，提供您的實驗名稱。

### <a name="run-experiment"></a>執行實驗

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**實驗**] 節點。
1. 以滑鼠右鍵按一下您想要執行的實驗。
1. 選取活動列中的 [**執行實驗**] 圖示。
1. 選擇您的訂用帳戶。
1. 選擇要在其下執行實驗的 Azure ML 工作區。
1. 選擇您的實驗。
1. 選擇或建立要在其上執行實驗的計算。
1. 選擇或建立實驗的執行設定。

或者，您也可以選取擴充功能頂端的 [**執行實驗**] 按鈕，並在提示中設定您的實驗執行。

### <a name="view-experiment"></a>查看實驗

若要在 Azure Machine Learning Studio 中查看您的實驗：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**實驗**] 節點。
1. 以滑鼠右鍵按一下您想要查看的實驗，然後選取 [ **View 實驗**]。 
1. 此時會出現提示，要求您在 Azure Machine Learning studio 中開啟實驗 URL。 選取 [開啟]。

### <a name="track-run-progress"></a>追蹤執行進度

當您執行實驗時，您可能會想要查看其進度。 若要從擴充功能追蹤在 Azure Machine Learning studio 中執行的進度：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**實驗**] 節點。
1. 展開您想要追蹤進度的實驗節點。
1. 在**Azure 入口網站中**，以滑鼠右鍵按一下 [執行]，然後選取 [執行]。
1. 此時會出現提示，要求您在 Azure Machine Learning studio 中開啟執行 URL。 選取 [開啟]。

### <a name="download-run-logs--outputs"></a>下載執行記錄 & 輸出

執行完成之後，您可能會想要下載記錄和資產，例如在實驗執行過程中產生的模型。

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**實驗**] 節點。
1. 展開您想要追蹤進度的實驗節點。
1. 以滑鼠右鍵按一下執行：
    - 若要下載輸出，請選取 [**下載輸出**]。
    - 若要下載記錄，請選取 [**下載記錄**]。

### <a name="view-run-metadata"></a>視圖執行中繼資料

在擴充功能中，您可以檢查中繼資料，例如用於執行的回合設定，以及執行詳細資料。

## <a name="compute-clusters"></a>計算叢集

此延伸模組支援下列計算類型：

- Azure Machine Learning 計算叢集
- Azure Kubernetes Service

如需詳細資訊，請參閱[計算目標](concept-compute-target.md#train)。

### <a name="create-compute"></a>建立計算

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您要在其下建立計算叢集的工作區節點。
1. 以滑鼠右鍵按一下 [**計算**叢集] 節點，然後選取 [**建立計算**]。
1. 在提示字元中：
    1. 選擇計算類型
    1. 選擇 VM 大小。 深入瞭解[VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。
    1. 提供計算的名稱。

### <a name="view-compute-configuration"></a>檢視計算設定

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**計算**叢集] 節點。
1. 以滑鼠右鍵按一下您想要查看的計算，然後選取 [視圖] [**計算屬性**]。

### <a name="edit-compute-scale-settings"></a>編輯計算調整規模設定

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**計算**叢集] 節點。
1. 以滑鼠右鍵按一下您想要編輯的計算，然後選取 [**編輯計算**]。
1. 您的計算的設定檔會在編輯器中開啟。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="delete-compute"></a>刪除計算

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**計算**叢集] 節點。
1. 以滑鼠右鍵按一下您想要刪除的計算，然後選取 [**刪除計算**]。

### <a name="create-run-configuration"></a>建立回合組態

若要在擴充功能中建立執行設定：

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**計算**叢集] 節點。
1. 以滑鼠右鍵按一下您想要在其上建立執行設定的計算目標，然後選取 [**建立**回合設定]。
1. 在提示字元中：
    1. 提供計算目標的名稱
    1. 選擇或建立新的環境。
    1. 輸入您要執行之腳本的名稱，或在本機電腦上按**enter**以流覽腳本。
    1. 選擇性選擇您是否要建立定型回合的資料參考。 這麼做會提示您在執行設定中定義資料集。
        1. 選取其中一個已註冊的資料集，以連結至執行設定，並在編輯器中開啟您的資料集的設定檔。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。
    1. 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="edit-run-configuration"></a>編輯執行設定

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在工作區的 [**計算**叢集] 節點中，展開您的 [計算叢集] 節點。
1. 以滑鼠右鍵按一下您想要編輯的回合設定，然後選取 [編輯] [**執行**設定]。
1. 執行設定的設定檔會在編輯器中開啟。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="delete-run-configuration"></a>刪除執行設定

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 在 [**計算**叢集] 節點中，展開相關的 [計算叢集] 節點。
1. 以滑鼠右鍵按一下您想要編輯的回合設定，然後選取 [刪除] [**執行**設定]。

## <a name="models"></a>模型

如需詳細資訊，請參閱[模型](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>註冊模型

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 以滑鼠右鍵按一下 [**模型**] 節點，然後選取 [**註冊模型**]。
1. 在提示字元中：
    1. 提供模型的名稱
    1. 選擇您的模型是否為檔案或資料夾。
    1. 在您的本機電腦中尋找模型。
    1. 編輯器中模型的設定檔。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

### <a name="view-model-properties"></a>視圖模型屬性

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**模型**] 節點。
1. 以滑鼠右鍵按一下您想要查看其屬性的模型，然後選取 [**視圖模型屬性**]。 檔案會在包含模型屬性的編輯器中開啟。

### <a name="download-model"></a>下載模型

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**模型**] 節點。
1. 以滑鼠右鍵按一下您想要下載的模型，然後選取 [**下載模型**檔案]。

### <a name="delete-a-model"></a>刪除模型

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 在您的工作區中展開 [**模型**] 節點。
1. 以滑鼠右鍵按一下您想要刪除的模型，然後選取 [**移除模型**]。

## <a name="endpoints"></a>端點

VS Code 延伸模組支援下列部署目標：

- Azure Container Instances
- Azure Kubernetes Service

如需詳細資訊，請參閱[web 服務端點](concept-azure-machine-learning-architecture.md#web-service-endpoint)。

### <a name="create-deployments"></a>建立部署

> [!NOTE]
> 部署建立目前僅適用于 Conda 環境。

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開您的工作區節點。
1. 以滑鼠右鍵按一下 [**端點**] 節點，然後選取 [**部署服務**]。
1. 在提示字元中：
    1. 選擇您是否要使用已註冊的模型或本機模型檔案。
    1. 選取您的模型
    1. 選擇您想要將模型部署到其中的部署目標。
    1. 提供模型的 [名稱]。
    1. 提供要在評分模型時執行的腳本。
    1. 提供 Conda 相依性檔案。
    1. 您的部署的設定檔會出現在編輯器中。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。

> [!NOTE]
> 或者，您可以在 [*模型*] 節點中，以滑鼠右鍵按一下已註冊的模型，然後選取 [**從已註冊的模型部署服務**]。

### <a name="delete-deployments"></a>刪除部署

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**端點**] 節點。
1. 以滑鼠右鍵按一下您想要移除的部署，然後選取 [**移除服務**]。
1. 此時會出現提示，確認您想要移除此服務。 選取 [確定]。

### <a name="manage-deployments"></a>管理部署

除了建立和刪除部署之外，您還可以查看和編輯與部署相關聯的設定。

1. 展開包含您工作區的 [訂用帳戶] 節點。
1. 展開工作區中的 [**端點**] 節點。
1. 以滑鼠右鍵按一下您想要管理的部署：
    - 若要編輯設定，請選取 [**編輯服務**]。
        - 您的部署的設定檔會出現在編輯器中。 如果您對設定感到滿意，請選取 [**儲存並繼續**]，或開啟 VS Code 命令選擇區（**View > 命令**選擇區），然後輸入**Azure ML： [儲存並繼續**]。
    - 若要查看部署設定，請選取 [視圖] [**服務屬性**]。

## <a name="next-steps"></a>後續步驟

使用 VS Code 延伸[模組來定型影像分類模型](tutorial-train-deploy-image-classification-model-vscode.md)。