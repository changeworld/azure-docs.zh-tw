---
title: 在設計工具中轉換資料（預覽）
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具（預覽）中轉換資料，以建立您自己的資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 05a21ce10db2822c963f1b375842e9a7233e0816
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87457816"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>在 Azure Machine Learning 設計工具中轉換資料 (預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將會了解如何在 Azure Machine Learning 設計工具中轉換和儲存資料集，如此即可為機器學習服務準備資料。

您將會使用範例 [Adult Census Income Binary Classification](sample-designer-datasets.md) (成人人口普查收入二元分類) 資料集來準備兩個資料集：其中一個資料集僅包含美國的成人人口普查資訊，另一個資料集則包含非美國成人的人口普查資訊。

在本文中，您將學會如何：

1. 轉換資料集以準備進行定型。
1. 將產生的資料集匯出到資料存放區。
1. 檢視結果。

本操作指南是[如何重新定型設計工具模型](how-to-retrain-designer.md)一文章先決條件。 在本文中，您將會了解如何使用轉換後的資料集，以搭配管線參數來定型多個模型。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>轉換資料集

在本節中，您將會了解如何匯入範例資料集，並將資料分成美國和非美國資料集。 如需如何將資料匯入設計工具的詳細資訊，請參閱[如何匯入資料](how-to-designer-import-data.md)。

### <a name="import-data"></a>匯入資料

請使用下列步驟來匯入範例資料集。

1. 登入 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，並選取您要使用的工作區。

1. 前往設計工具。 選取 Easy-to-use-prebuild modules \(易於使用的預建模組\) 來建立新管線。

1. 選取要執行管線的預設計算目標。

1. 管線畫布左側是資料集和模組的選擇區。 選取 [資料集]。 然後檢視 [範例] 區段。

1. 將 **Adult Census Income Binary classification** (成人人口普查收入二元分類) 資料集拖放到畫布。

1. 選取 **Adult Census Income** (成人人口普查收入) 資料集模組。

1. 於出現在畫布右側的詳細資料窗格中，選取 [輸出]。

1. 選取視覺化圖示 ![視覺化圖示](media/how-to-designer-transform-data/visualize-icon.png)。

1. 使用資料預覽視窗來探索資料集。 請特別注意 "native-country" 資料行的值。

### <a name="split-the-data"></a>分割資料

在本節中，您會使用[分割資料模組](algorithm-module-reference/split-data.md)來識別和分割在 "native-country" 資料行中包含 "United-States" 的資料列。 

1. 在畫布左側的模組選擇區中，展開 [資料轉換] 區段，然後尋找**分割資料**模組。

1. 將**分割資料**模組拖曳到畫布上，然後將模組放在資料集模組的下方。

1. 將資料集模組連線到**分割資料**模組。

1. 選取**分割資料**模組。

1. 在畫布右側的模組詳細資料窗格中，將 [分割模式] 設為 [規則運算式]。

1. 輸入**規則運算式**：`\"native-country" United-States`。

    **規則運算式**模式會測試單一資料行的值。 如需分割資料模組的詳細資訊，請參閱相關的[演算法模組參考頁面](algorithm-module-reference/split-data.md)。

您的管線應會顯示如下：

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="顯示如何設定管線和分割資料模組的螢幕擷取畫面":::


## <a name="save-the-datasets"></a>儲存資料集

現在管線已設為分割資料，您需要指定保存資料集的位置。 針對此範例，請使用**匯出資料**模組來將資料集儲存到資料存放區。 如需資料存放區的詳細資訊，請參閱[連線到 Azure 儲存體服務](how-to-access-data.md)

1. 在畫布左側的模組選擇區中，展開 [資料輸入和輸出] 區段，然後尋找**匯出資料**模組。

1. 將兩個**匯出資料**模組拖放到**分割資料**模組下方。

1. 將每個**分割資料**模組的輸出連接埠連線到不同**匯出資料**模組。

    管線看起來應該會如下：

    ![顯示如何連線匯出資料模組的螢幕擷取畫面](media/how-to-designer-transform-data/export-data-pipeline.png).

1. 選取連線到**分割資料**模組「最左側」連接埠的**匯出資料**模組。

    輸出連接埠的順序會影響**分割資料**模組。 第一個輸出連接埠包含規則運算式為 true 的資料列。 在本案例中，第一個連接埠包含以美國為基礎的收入資料列，且第二個連接埠則包含不是以美國為基礎的收入資料列。

1. 在畫布右側的模組詳細資料窗格中，設定下列選項：
    
    **資料存放區類型**：Azure Blob 儲存體

    **資料存放區**：選取現有資料存放區，或選取 [New datastore] \(新增資料存放區\) 來立即建立。

    **路徑**：`/data/us-income`

    **檔案格式**：csv

    > [!NOTE]
    > 本文假設您可存取向目前 Azure Machine Learning 工作區註冊的資料存放區。 如需如何設定資料存放區的指示，請參閱[連線到 Azure 儲存體服務](how-to-access-data.md#studio)。

    如果您沒有資料存放區，則可在此建立一個。 基於範例目的，本文會將資料集儲存到與工作區建立關聯的預設 Blob 儲存體帳戶。 其會將資料集儲存到稱為 `data` 新資料夾中的 `azureml` 容器。

1.  選取連線到**分割資料**模組「最右側」連接埠的**匯出資料**模組。

1. 在畫布右側的模組詳細資料窗格中，設定下列選項：
    
    **資料存放區類型**：Azure Blob 儲存體

    **資料存放區**：選取與上方相同的資料存放區

    **路徑**：`/data/non-us-income`

    **檔案格式**：csv

1. 確認連線到**分割資料**左側連接埠的**匯出資料**模組具備**路徑** `/data/us-income`。

1. 確認連線到右側連接埠的**匯出資料**模組具備**路徑**`/data/non-us-income`。

    管線和設定應會如下：
    
    ![顯示如何設定匯出資料模組的螢幕擷取畫面](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>提交執行

現在管線已設為分割及匯出資料，您可提交管線執行。

1. 在畫布頂端，選取 [提交]。

1. 在 [Set up pipeline run] \(設定管線執行\) 對話方塊中，選取 [新建] 來建立實驗。

    實驗會依照邏輯將相關的管線執行分組。 若您在未來執行此管線，則建議針對記錄和追蹤使用相同的實驗。

1. 提供描述性的實驗名稱，例如 "split-census-data"。

1. 選取 [提交]。

## <a name="view-results"></a>檢視結果

在管線完成執行後，您可在 Azure 入口網站中巡覽至 Blob 儲存體來檢視結果。 您也可以檢視**分割資料**模組的中繼結果來確認資料已正確分割。

1. 選取**分割資料**模組。

1. 在畫布右側的 [模組詳細資料] 窗格中，選取 [輸出 + 記錄]。 

1. 選取位於**結果資料集 1** 旁邊的視覺化圖示![視覺化圖示](media/how-to-designer-transform-data/visualize-icon.png)。 

1. 驗證 "native-country" 資料行只包含 "United-States" 值。

1. 選取位於**結果資料集 2** 旁邊的視覺化圖示![視覺化圖示](media/how-to-designer-transform-data/visualize-icon.png)。 

1. 驗證 "native-country" 資料行不包含 "United-States" 值。

## <a name="clean-up-resources"></a>清除資源

若想要繼續本操作指南的第 2 部分，請跳過這一節，前往[使用 Azure Machine Learning 設計工具重新定型模型](how-to-retrain-designer.md)。

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本文中，您將會了解如何轉換資料集，並將其儲存至註冊的資料存放區。

繼續前往本操作指南系列的下一個部分：[使用 Azure Machine Learning 設計工具重新定型模型](how-to-retrain-designer.md)，以使用轉換後的資料集和管線參數來定型機器學習模型。
