---
title: 導入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習中的導入資料模組從現有雲資料服務將資料載入到機器學習管道中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456194"
---
# <a name="import-data-module"></a>導入資料模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組將資料從現有雲資料服務載入到機器學習管道中。 

> [!Note]
> 此模組提供的所有功能都可以由工作資料登錄頁中的**資料存儲**和**資料集**完成。 我們建議您使用**資料存儲**和**資料集**，其中包括資料監視等其他功能。 要瞭解詳細資訊，請參閱[如何訪問資料和](../how-to-access-data.md)[如何註冊資料集](../how-to-create-register-datasets.md)一文。
> 註冊資料集後，可以在設計器介面中的 **"資料集** -> **""我的資料集"** 類別中找到它。 此模組為 Studio（經典）使用者保留，以進行熟悉的體驗。 
>

**導入資料**模組支援從以下來源讀取資料：

- 通過 HTTP 的 URL
- 通過[**資料存儲**](../how-to-access-data.md)的 Azure 雲存儲 ）
    - Azure Blob 容器
    - Azure 檔案共用
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

在使用雲存儲之前，需要首先在 Azure 機器學習工作區中註冊資料存儲。 有關詳細資訊，請參閱[如何訪問資料](../how-to-access-data.md)。 

定義所需資料並連接到源後，**[導入資料](./import-data.md)** 會根據每列包含的值推斷每列的資料類型，並將資料載入到設計器管道中。 **導入資料的**輸出是可用於任何設計器管道的資料集。

如果來源資料發生更改，則可以通過重新運行[導入資料](./import-data.md)來刷新資料集並添加新資料。

## <a name="how-to-configure-import-data"></a>如何配置導入資料

1. 將**導入資料**模組添加到管道中。 您可以在設計器中**的資料輸入和輸出**類別中找到此模組。

1. 選擇模組以打開右側窗格。

1. 選擇**資料來源**，然後選擇資料來源類型。 它可以是 HTTP 或資料存儲。

    如果選擇資料存儲，則可以選擇已註冊到 Azure 機器學習工作區的現有資料存儲或創建新的資料存儲。 然後定義要在資料存儲中導入的資料路徑。 您可以通過按一下 **"流覽路徑**![導入資料路徑"輕鬆流覽路徑](media/module/import-data-path.png)

1. 選擇預覽架構以篩選要包括的列。 您還可以在分析選項中定義高級設置，如分隔符號。

    ![導入資料預覽](media/module/import-data.png)

1. 核取方塊"**重新生成輸出**"將決定是否執行模組以在運行時重新生成輸出。 

    預設情況下未選中，這意味著如果模組以前使用相同的參數執行，系統將重用上次運行的輸出以縮短執行時間。 

    如果選中，系統將再次執行模組以重新生成輸出。 因此，在更新存儲中的基礎資料時選擇此選項，它可以説明獲取最新資料。


1. 提交管道。

    當導入資料將資料載入到設計器中時，它會根據資料包含的值（數值或分類）推斷每列的資料類型。

    如果標頭存在，則使用標頭來命名輸出資料集的資料行。

    如果資料中沒有現有的列標題，則使用格式 col1 col2 生成新的列名稱,... ，科爾內。

## <a name="results"></a>結果

導入完成後，按一下輸出資料集並選擇 **"視覺化"** 以查看是否成功導入資料。

如果要保存資料以供重用，而不是每次運行管道時導入一組新資料，請在模組右側面板的 **"輸出"** 選項卡下選擇 **"註冊"資料集**圖示。 選擇資料集的名稱。 保存的資料集在保存時保留資料，在重新運行管道時不會更新資料集，即使管道中的資料集發生更改也是如此。 這對於拍攝資料快照非常有用。

導入資料後，可能需要一些額外的建模和分析準備工作：

- 使用["編輯中繼資料](./edit-metadata.md)"更改列名稱、將列作為其他資料類型處理，或指示某些列是標籤或要素。

- 使用["資料集中選擇列"](./select-columns-in-dataset.md)選擇要在建模中轉換或使用的列子集。 通過使用["添加列"](./add-columns.md)模組，可以輕鬆地將轉換或刪除的列重新加入原始資料集。  

- 使用[分區和示例](./partition-and-sample.md)分隔資料集、執行採樣或獲取前 n 行。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
