---
title: 匯入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [匯入資料] 模組，從現有的雲端資料服務將資料載入機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 5fe1c3e344705b6cde9791f889b22be53a9e8c76
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372590"
---
# <a name="import-data-module"></a>匯入資料模組

本文描述 Azure Machine Learning 設計工具中的模組。

使用此模組，從現有的雲端資料服務將資料載入機器學習管線。 

> [!Note]
> 此模組所提供的所有功能都可以透過工作區登陸頁面中的 **資料** 存放區和 **資料集** 來完成。 建議 **您使用資料** 存放區和資料 **集** ，其中包含資料監視等其他功能。 若要深入瞭解，請參閱 [如何存取資料](../how-to-access-data.md) 和 [如何註冊資料集](../how-to-create-register-datasets.md) 文章。
> 註冊資料集之後，您可以在設計工具介面的 [**資料**集  ->  **我的資料集**] 類別中找到它。 此課程模組已保留給 Studio (傳統) 使用者，以獲得熟悉的體驗。 
>

「匯 **入資料** 」模組支援從下列來源讀取資料：

- 經由 HTTP 的 URL
- 透過 [**資料存放區**](../how-to-access-data.md)) 的 Azure 雲端儲存體
    - Azure Blob 容器
    - Azure 檔案共用
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure 于 postgresql    

使用雲端儲存體之前，您必須先在 Azure Machine Learning 工作區中註冊資料存放區。 如需詳細資訊，請參閱 [如何存取資料](../how-to-access-data.md)。 

在您定義想要的資料並連接到來源之後，匯 **[入資料](./import-data.md)** 會根據所包含的值來推斷每個資料行的資料類型，並將資料載入您的設計工具管線。 匯 **入資料** 的輸出是可搭配任何設計工具管線使用的資料集。

如果您的來源資料有所變更，您可以重新執行匯 [入資料](./import-data.md)來重新整理資料集，並加入新的資料。

> [!WARNING]
> 如果您的工作區位於虛擬網路中，您必須將資料存放區設定為使用設計工具的資料視覺效果功能。 如需如何在虛擬網路中使用資料存放區和資料集的詳細資訊，請參閱 [在 Azure 虛擬網路中使用 Azure Machine Learning studio](../how-to-enable-studio-virtual-network.md)。


## <a name="how-to-configure-import-data"></a>如何設定匯入資料

1. 將 [匯 **入資料** ] 模組新增至您的管線。 您可以在設計工具的 [ **資料輸入] 和 [輸出** ] 類別中找到此模組。

1. 選取模組以開啟右窗格。

1. 選取 [ **資料來源**]，然後選擇資料來源類型。 它可以是 HTTP 或資料存放區。

    如果您選擇資料存放區，您可以選取已向 Azure Machine Learning 工作區註冊的現有資料存放區，或建立新的資料存放區。 然後，在資料存放區中定義要匯入的資料路徑。 您可以輕鬆地流覽路徑，方法是按一下 [ **流覽路徑** ![ 螢幕擷取畫面]，即可顯示 [流覽路徑] 連結，以開啟 [路徑選取] 對話方塊。](media/module/import-data-path.png)

1. 選取預覽架構，以篩選您想要包含的資料行。 您也可以在剖析選項中定義諸如分隔符號的 advanced 設定。

    ![匯入-資料-預覽](media/module/import-data.png)

1. [重新產生 **輸出**] 核取方塊會決定是否要執行模組，以便在執行時間重新產生輸出。 

    預設為未選取，這表示如果已使用先前相同的參數來執行模組，系統將會重複使用上次執行的輸出，以縮短執行時間。 

    如果選取此選項，系統會再次執行模組以重新產生輸出。 因此，當儲存體中的基礎資料更新時，請選取此選項，以協助取得最新的資料。


1. 提交管線。

    當匯入資料將資料載入至設計工具時，它會根據所包含的值（數值或類別）推斷每個資料行的資料類型。

    如果標頭存在，則使用標頭來命名輸出資料集的資料行。

    如果資料中沒有現有的資料行標頭，則會使用格式 col1、col2 來產生新的資料行名稱,.。。 , coln*.

## <a name="results"></a>結果

當匯入完成時，按一下輸出資料集並選取 [ **視覺化** ]，以查看資料是否已成功匯入。

如果您想要儲存資料以供重複使用，而不是在每次執行管線時都匯入一組新的資料，請選取模組右面板的 [**輸出**] 索引標籤下的 [**註冊資料集**] 圖示。 選擇資料集的名稱。 儲存的資料集會在儲存時保留資料，當重新執行管線時，資料集不會更新，即使管線中的資料集有所變更。 這有助於取得資料的快照集。

匯入資料之後，可能需要一些額外的模型和分析準備：

- 您可以使用 [ [編輯中繼資料](./edit-metadata.md) ] 來變更資料行名稱、將資料行處理為不同的資料類型，或表示某些資料行是標籤或特徵。

- 使用 [ [選取資料集中的資料行](./select-columns-in-dataset.md) ]，即可選取要在模型中轉換或使用的資料行子集。 您可以使用 [ [加入資料行](./add-columns.md) ] 模組，輕鬆地將已轉換或已移除的資料行重新加入原始資料集。  

- 使用資料分割 [和範例](./partition-and-sample.md) 來分割資料集、執行取樣，或取得前 n 個數據列。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
