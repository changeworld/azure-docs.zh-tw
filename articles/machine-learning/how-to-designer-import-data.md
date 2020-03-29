---
title: 匯入資料
titleSuffix: Azure Machine Learning
description: 瞭解如何從各種資料來源將資料導入 Azure 機器學習設計器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: bd1c2ca182ae8be8425246f691dca805bf38637b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064024"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>將資料導入 Azure 機器學習設計器（預覽版）

在本文中，您將瞭解如何在設計器中導入自己的資料以創建自訂解決方案。 有兩種方法可以將資料導入設計器： 

* **Azure 機器學習資料集**- 在 Azure 機器學習中註冊[資料集](concept-data.md#datasets)，以啟用可説明您管理資料的高級功能。
* **導入資料模組**- 使用[導入資料](algorithm-module-reference/import-data.md)模組直接從連線資料來源訪問資料。

## <a name="use-azure-machine-learning-datasets"></a>使用 Azure 機器學習資料集

我們建議您使用[資料集](concept-data.md#datasets)將資料導入設計器。 註冊資料集時，可以充分利用高級資料功能，如[版本控制、跟蹤](how-to-version-track-datasets.md)和[資料監視](how-to-monitor-datasets.md)。

### <a name="register-a-dataset"></a>註冊資料集

您可以[通過 SDK 或](how-to-create-register-datasets.md#use-the-sdk)在 Azure[機器學習工作室直觀地](how-to-create-register-datasets.md#use-the-ui)註冊現有資料集。

您還可以將任何設計器模組的輸出註冊為資料集。

1. 選擇輸出要註冊資料的模組。

1. 在屬性窗格中，選擇 **"輸出** > **註冊"資料集**。

    ![顯示如何導航到"註冊資料集"選項的螢幕截圖](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>使用資料集

可以在"**資料集** > **我的資料集**"下，在模組調色板中找到已註冊的資料集。 要使用資料集，請將其拖放到管道畫布上。 然後，將資料集的輸出埠連接到調色板中的其他模組。

![顯示設計器調色板中已保存資料集位置的螢幕截圖](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> 設計器目前僅支援處理[表格資料集](how-to-create-register-datasets.md#dataset-types)。 如果要使用[檔資料集](how-to-create-register-datasets.md#dataset-types)，請使用適用于 Python 和 R 的 Azure 機器學習 SDK。

## <a name="import-data-using-the-import-data-module"></a>使用導入資料模組導入資料

雖然我們建議您使用資料集導入資料，但也可以使用["導入資料"](algorithm-module-reference/import-data.md)模組。 導入資料模組跳過在 Azure 機器學習中註冊資料集，並直接從[資料存儲](concept-data.md#datastores)或 HTTP URL 導入資料。

有關如何使用導入資料模組的詳細資訊，請參閱[導入資料參考頁](algorithm-module-reference/import-data.md)。

> [!NOTE]
> 如果資料集的列太多，則可能會遇到以下錯誤："由於大小限制，驗證失敗"。 為了避免這種情況，[請在資料集介面中註冊資料集](how-to-create-register-datasets.md#use-the-ui)。

## <a name="supported-sources"></a>支援的來源

本節列出設計器支援的資料來源。 資料從資料存儲或[表格資料集](how-to-create-register-datasets.md#dataset-types)進入設計器。

### <a name="datastore-sources"></a>資料存儲源
有關受支援的資料存儲源的清單，請參閱[Azure 存儲服務中的訪問資料](how-to-access-data.md#supported-data-storage-service-types)。

### <a name="tabular-dataset-sources"></a>表格資料集源

設計器支援從以下源創建的表格資料集：
 * 符號分隔檔案
 * JSON 檔案
 * Parquet 檔案
 * SQL 查詢

## <a name="data-types"></a>資料類型

設計人員內部識別以下資料類型：

* String
* 整數 
* Decimal
* Boolean
* Date

設計器使用內部資料類型在模組之間傳遞資料。 您可以使用["轉換為資料集"](algorithm-module-reference/convert-to-dataset.md)模組顯式將資料轉換為數據表格式。 任何接受內部格式以外的格式的模組都將在將資料傳遞給下一個模組之前靜默轉換資料。

## <a name="data-constraints"></a>資料約束

設計器中的模組受計算目標大小的限制。 對於較大的資料集，應使用較大的 Azure 機器學習計算資源。 有關 Azure 機器學習計算的詳細資訊，請參閱[Azure 機器學習中的計算目標是什麼？](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>後續步驟

通過教程瞭解設計師的基礎知識[：與設計師一起預測汽車價格](tutorial-designer-automobile-price-train-score.md)。
