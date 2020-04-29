---
title: 匯入資料
titleSuffix: Azure Machine Learning
description: 瞭解如何將資料從各種資料來源匯入 Azure Machine Learning 設計工具中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546744"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>將資料匯入 Azure Machine Learning 設計工具（預覽）

在本文中，您將瞭解如何在設計工具中匯入您自己的資料，以建立自訂的解決方案。 有兩種方式可以將資料匯入設計工具中： 

* **Azure Machine Learning 資料集**-在 Azure Machine Learning 中註冊[資料集](concept-data.md#datasets)，以啟用可協助您管理資料的先進功能。
* 匯**入資料模組**-使用 [匯[入資料](algorithm-module-reference/import-data.md)] 模組可直接從線上資料來源存取資料。

## <a name="use-azure-machine-learning-datasets"></a>使用 Azure Machine Learning 資料集

我們建議您使用資料[集](concept-data.md#datasets)將資料匯入設計工具中。 當您註冊資料集時，您可以充分利用先進的資料功能，例如[版本控制和追蹤](how-to-version-track-datasets.md)和[資料監視](how-to-monitor-datasets.md)。

### <a name="register-a-dataset"></a>註冊資料集

您可以[在 Azure Machine Learning studio 中以視覺化](how-to-create-register-datasets.md#use-the-ui)方式向 SDK 註冊現有[的](how-to-create-register-datasets.md#use-the-sdk)資料集。

您也可以將任何設計工具模組的輸出註冊為資料集。

1. 選取用來輸出您要註冊之資料的模組。

1. 在 [屬性] 窗格中，選取 [**輸出** > ] [**註冊資料集**]。

    ![顯示如何流覽至 [註冊資料集] 選項的螢幕擷取畫面](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>使用資料集

您已註冊的資料集可以在模組選擇區的 [**資料集** > ] [**我的資料集**] 下找到。 若要使用資料集，請將其拖放到管線畫布上。 然後，將資料集的輸出埠連接到元件中的其他模組。

![顯示設計工具選擇區中已儲存資料集位置的螢幕擷取畫面](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> 設計工具目前僅支援處理[表格式資料集](how-to-create-register-datasets.md#dataset-types)。 如果您想要使用檔案[資料集](how-to-create-register-datasets.md#dataset-types)，請使用適用于 Python 和 R 的 Azure Machine Learning SDK。

## <a name="import-data-using-the-import-data-module"></a>使用匯入資料模組匯入資料

雖然建議您使用資料集匯入資料，但您也可以使用 [匯[入資料](algorithm-module-reference/import-data.md)] 模組。 匯入資料模組會略過在 Azure Machine Learning 中註冊您的資料集，並直接[從資料存放區或 HTTP](concept-data.md#datastores) URL 匯入資料。

如需如何使用匯入資料模組的詳細資訊，請參閱匯[入資料參考頁面](algorithm-module-reference/import-data.md)。

> [!NOTE]
> 如果您的資料集有太多資料行，您可能會遇到下列錯誤：「驗證因為大小限制而失敗」。 若要避免這種情況，請[在資料集介面中註冊資料集](how-to-create-register-datasets.md#use-the-ui)。

## <a name="supported-sources"></a>支援的來源

此區段會列出設計工具支援的資料來源。 資料會從資料存放區或[表格式資料集](how-to-create-register-datasets.md#dataset-types)進入設計工具。

### <a name="datastore-sources"></a>資料存放區來源
如需支援的資料存放區來源清單，請參閱[存取 Azure 儲存體服務中的資料](how-to-access-data.md#supported-data-storage-service-types)。

### <a name="tabular-dataset-sources"></a>表格式資料集來源

設計工具支援從下列來源建立的表格式資料集：
 * 符號分隔檔案
 * JSON 檔案
 * Parquet 檔案
 * SQL 查詢

## <a name="data-types"></a>資料類型

設計工具會在內部辨識下列資料類型：

* 字串
* 整數
* Decimal
* Boolean
* Date

設計工具會使用內部資料類型，在模組之間傳遞資料。 您可以使用 [[轉換成資料集](algorithm-module-reference/convert-to-dataset.md)] 模組，將您的資料明確轉換成資料表格式。 接受內部格式以外之格式的任何模組，將會以無訊息模式轉換資料，然後再將其傳遞至下一個模組。

## <a name="data-constraints"></a>資料條件約束

設計工具中的模組受限於計算目標的大小。 對於較大的資料集，您應該使用較大的 Azure Machine Learning 計算資源。 如需 Azure Machine Learning 計算的詳細資訊，請參閱[什麼是 Azure Machine Learning 中的計算目標？](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>後續步驟

學習設計工具的基本概念[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)。
