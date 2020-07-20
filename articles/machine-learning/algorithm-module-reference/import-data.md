---
title: 匯入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的匯入資料模組，將資料從現有的雲端資料服務載入機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: e47cb1180bbc6eaaaffd79b78563ab1b1d5e016a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611810"
---
# <a name="import-data-module"></a>匯入資料模組

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組，將資料從現有的雲端資料服務載入機器學習管線。 

> [!Note]
> 此模組所提供的所有功能都可以透過工作區登陸頁面中的**資料**存放區和**資料集**來完成。 我們建議**您使用資料**存放區和**資料集**，其中包含其他功能，例如資料監視。 若要深入瞭解，請參閱[如何存取資料](../how-to-access-data.md)和[如何註冊資料集](../how-to-create-register-datasets.md)一文。
> 註冊資料集之後，您可以在設計工具介面的 [**資料**  ->  **集]** 類別目錄中找到它。 本課程模組已保留給 Studio （傳統）使用者使用，以獲得熟悉的體驗。 
>

「匯**入資料**」模組支援從下列來源讀取資料：

- 透過 HTTP 的 URL
- 透過[**資料存放區**](../how-to-access-data.md)的 Azure 雲端儲存體）
    - Azure Blob 容器
    - Azure 檔案共用
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure 于 postgresql    

使用雲端存放裝置之前，您必須先在 Azure Machine Learning 工作區中註冊資料存放區。 如需詳細資訊，請參閱[如何存取資料](../how-to-access-data.md)。 

定義您想要的資料並連接到來源之後，匯**[入資料](./import-data.md)** 會根據其包含的值來推斷每個資料行的資料類型，並將資料載入您的設計工具管線。 匯**入資料**的輸出是可以搭配任何設計工具管線使用的資料集。

如果您的來源資料變更，您可以重新執行匯[入資料](./import-data.md)來重新整理資料集，並加入新的資料。

> [!WARNING]
> 如果您的工作區位於虛擬網路中，您必須將資料存放區設定為使用設計工具的資料視覺效果功能。 如需如何在虛擬網路中使用資料存放區和資料集的詳細資訊，請參閱[使用私人虛擬網路進行定型 & 推斷期間的網路隔離](../how-to-enable-virtual-network.md#machine-learning-studio)。


## <a name="how-to-configure-import-data"></a>如何設定匯入資料

1. 將匯**入資料**模組新增至您的管線。 您可以在設計工具的 [**資料輸入和輸出**] 分類中找到此模組。

1. 選取模組以開啟右窗格。

1. 選取 [**資料來源**]，然後選擇 [資料來源類型]。 它可以是 HTTP 或資料存放區。

    如果您選擇 [資料存放區]，您可以選取已向 Azure Machine Learning 工作區註冊的現有資料存放區，或建立新的資料存放區。 然後在資料存放區中定義要匯入之資料的路徑。 您可以按一下 **[流覽路徑]** [匯 ![ 入-資料-路徑]，輕鬆地流覽路徑](media/module/import-data-path.png)

1. 選取 [預覽] 架構，以篩選您想要包含的資料行。 您也可以在剖析選項中定義像是分隔符號的 advanced 設定。

    ![匯入-資料-預覽](media/module/import-data.png)

1. [重新產生**輸出**] 核取方塊會決定是否要執行模組，以便在執行時間重新產生輸出。 

    預設為未選取，這表示如果模組先前已使用相同的參數執行，則系統會重複使用最後一次執行的輸出，以縮短執行時間。 

    如果選取此選項，系統會再次執行模組以重新產生輸出。 因此，當儲存體中的基礎資料更新時，請選取此選項，這有助於取得最新的資料。


1. 提交管線。

    當 [匯入資料] 將資料載入設計工具時，它會根據其包含的值（數值或類別）來推斷每個資料行的資料類型。

    如果標頭存在，則使用標頭來命名輸出資料集的資料行。

    如果資料中沒有現有的資料行標頭，則會使用 col1 格式來產生新的資料行名稱，col2,.。。 , coln*.

## <a name="results"></a>結果

當匯入完成時，按一下輸出資料集，然後選取 [**視覺化**] 以查看資料是否已成功匯入。

如果您想要儲存資料以供重複使用，而不是在每次執行管線時匯入新的資料集，請在模組的右面板中，選取 [**輸出**] 索引標籤底下的 [**註冊資料集**] 圖示。 選擇資料集的名稱。 儲存的資料集會在儲存時保留資料，而在重新執行管線時不會更新資料集，即使管線中的資料集變更也是一樣。 這有助於製作資料的快照集。

匯入資料之後，可能需要一些額外的模型化和分析準備：

- 使用 [[編輯中繼資料](./edit-metadata.md)] 來變更資料行名稱、將資料行處理為不同的資料類型，或表示某些資料行是標籤或特徵。

- 使用 [[選取資料集中的資料行](./select-columns-in-dataset.md)]，即可選取要在模型化中轉換或使用的資料行子集。 您可以使用 [[加入資料行](./add-columns.md)] 模組，輕鬆地將已轉換或已移除的資料行重新加入至原始資料集。  

- 使用[Partition 和 Sample](./partition-and-sample.md)來分割資料集、執行取樣，或取得前 n 個數據列。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
