---
title: 轉換資料
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計師中轉換資料，以建立您自己的資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842873"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>在 Azure Machine Learning 設計工具中轉換資料（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure Machine Learning 的設計工具中轉換和儲存資料集，讓您可以準備自己的資料以進行機器學習。

您將使用範例[成人人口普查收入二進位分類](sample-designer-datasets.md)資料集來準備兩個資料集：一個資料集，其中包含來自美國的成人人口普查資訊，以及另一個包含非美國成人人口普查資訊的資料集。

在本文中，您將學會如何：

1. 轉換資料集以準備進行定型。
1. 將產生的資料集匯出至資料存放區。
1. 查看結果。

本操作說明是如何重新定型設計工具[模型](how-to-retrain-designer.md)一文的必要條件。 在該文章中，您將瞭解如何使用已轉換的資料集，以管線參數來定型多個模型。

## <a name="transform-a-dataset"></a>轉換資料集

在本節中，您將瞭解如何匯入範例資料集，並將資料分割成 US 和非 US 資料集。 如需如何將您自己的資料匯入設計工具的詳細資訊，請參閱[如何匯入資料](how-to-designer-import-data.md)。

### <a name="import-data"></a>匯入資料

使用下列步驟匯入範例資料集。

1. 登入 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，並選取您要使用的工作區。

1. 移至設計工具。 選取**便於使用的建置前模組**，以建立新的管線。

1. 選取要執行管線的預設計算目標。

1. 管線畫布左側是資料集和模組的選擇區。 選取 [**資料集**]。 然後，查看 [**範例**] 區段。

1. 將**成人人口普查收入二進位分類**資料集拖放到畫布上。

1. 選取 [**成人人口普查收入**資料集] 模組。

1. 在畫布右側的詳細資料窗格中，選取 [**輸出**]。

1. 選取 [視覺化] 圖示 ![視覺化圖示](media/how-to-designer-transform-data/visualize-icon.png).

1. 使用 [資料預覽] 視窗來流覽資料集。 請特別注意「原生國家/地區」資料行的值。

### <a name="split-the-data"></a>分割資料

在本節中，您會使用「[分割資料」模組](algorithm-module-reference/split-data.md)來識別並分割「原生國家/地區」資料行中包含「美國」的資料列。 

1. 在畫布左側的模組選擇區中，展開 [**資料轉換**] 區段，然後尋找 [**分割資料**] 模組。

1. 將 [**分割資料**] 模組拖曳至畫布上，並將模組放在資料集模組下方。

1. 將資料集模組連接到「**分割資料**」模組。

1. 選取**分割資料**模組。

1. 在畫布右側的 [模組詳細資料] 窗格中，將 [**分割模式**] 設定為 [**正則運算式**]。

1. 輸入**正則運算式**： `\"native-country" United-States`。

    **正則運算式**模式會測試單一資料行的值。 如需分割資料模組的詳細資訊，請參閱相關的[演算法模組參考頁面](algorithm-module-reference/split-data.md)。

您的管線應會顯示如下：

![顯示如何設定管線和分割資料模組的螢幕擷取畫面](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>儲存資料集

現在，您的管線已設定為分割資料，您必須指定要保存資料集的位置。 在此範例中，使用 [**匯出資料**] 模組，將資料集儲存至資料存放區。 如需資料存放區的詳細資訊，請參閱[連接到 Azure 儲存體服務](how-to-access-data.md)

1. 在畫布左側的模組選擇區中，展開 [**資料輸入和輸出**] 區段，然後尋找 [**匯出資料**] 模組。

1. 將兩個**匯出資料**模組拖放到 [**分割資料**] 模組底下。

1. 將「**分割資料**」模組的每個輸出埠連接到不同的「**匯出資料**」模組。

    您的管線看起來應該像這樣：

    ![顯示如何連接匯出資料模組的螢幕擷取畫面](media/how-to-designer-transform-data/export-data-pipeline.png).

1. 選取已連接至 [**分割資料**] 模組最*左側*埠的 [**匯出資料**] 模組。

    [**分割資料**] 模組的輸出埠重要順序。 第一個輸出埠包含正則運算式為 true 的資料列。 在此情況下，第一個埠包含以美國為基礎之收入的資料列，而第二個埠包含以非美國收入為基礎的資料列。

1. 在畫布右側的 [模組詳細資料] 窗格中，設定下列選項：
    
    **資料**存放區類型： Azure Blob 儲存體

    **資料**存放區：選取現有的資料存放區，或選取 [新資料存放區] 立即建立一個。

    **路徑**：`/data/us-income`

    **檔案格式**： csv

    > [!NOTE]
    > 本文假設您可以存取向目前的 Azure Machine Learning 工作區註冊的資料存放區。 如需有關如何設定資料存放區的指示，請參閱[連接到 Azure 儲存體服務](how-to-access-data.md#azure-machine-learning-studio)。

    如果您沒有資料存放區，可以立即建立一個。 基於範例目的，本文會將資料集儲存至與工作區相關聯的預設 blob 儲存體帳戶。 它會將資料集儲存到`azureml`名`data`為的新資料夾中的容器中。

1.  選取已連接至 [**分割資料**] 模組最*右邊*埠的 [**匯出資料**] 模組。

1. 在畫布右側的 [模組詳細資料] 窗格中，設定下列選項：
    
    **資料**存放區類型： Azure Blob 儲存體

    **資料**存放區：選取與上述相同的資料存放區

    **路徑**：`/data/non-us-income`

    **檔案格式**： csv

1. 確認連接到**分割資料**之左側埠的 [**匯出資料**] 模組具有**路徑** `/data/us-income`。

1. 確認連接至正確埠的**匯出資料**模組具有**路徑** `/data/non-us-income`。

    您的管線和設定看起來應該像這樣：
    
    ![顯示如何設定匯出資料模組的螢幕擷取畫面](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>提交執行

既然您的管線已設定為分割和匯出資料，請提交管線執行。

1. 在畫布頂端，選取 [提交]  。

1. 在 [**設定管線執行**] 對話方塊中，選取 [**建立新**的] 來建立實驗。

    實驗會以邏輯方式將相關的管線執行群組在一起。 如果您在未來執行此管線，您應該使用相同的實驗進行記錄和追蹤。

1. 提供描述性的實驗名稱，例如「分割人口普查-資料」。

1. 選取 [提交]  。

## <a name="view-results"></a>檢視結果

在管線完成執行之後，您可以流覽至 Azure 入口網站中的 blob 儲存體來查看您的結果。 您也可以查看「**分割資料**」模組的中繼結果，以確認資料已正確分割。

1. 選取**分割資料**模組。

1. 在畫布右側的 [模組詳細資料] 窗格中，選取 [輸出 + 記錄]  。 

1. 選取 [**結果 dataset1**] 旁](media/how-to-designer-transform-data/visualize-icon.png)的 [視覺化] 圖示![視覺化圖示。 

1. 確認「原生國家/地區」資料行只包含「美國」值。

1. 選取 [**結果 dataset2**] 旁](media/how-to-designer-transform-data/visualize-icon.png)的 [視覺化] 圖示![視覺化圖示。 

1. 確認「原生國家/地區」資料行未包含「美國」值。

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行這一節的第2部分，請略過本節，[使用 Azure Machine Learning 的設計](how-to-retrain-designer.md)工具重新定型模型。

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何轉換資料集，並將它儲存至已註冊的資料存放區。

繼續進行本使用方法系列的下一個部分，並使用[Azure Machine Learning 設計](how-to-retrain-designer.md)工具重新定型模型，以使用您已轉換的資料集和管線參數來訓練機器學習模型。
