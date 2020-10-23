---
title: 使用資料流程程式碼片段重復資料列和尋找 null
description: 瞭解如何使用資料流程中的程式碼片段，輕鬆重復資料列的重復資料，並尋找 null
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458161"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>使用資料流程程式碼片段重復資料列和尋找 null

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

藉由在對應資料流程中使用程式碼片段，您可以輕鬆地執行一般工作，例如重復資料刪除和 null 篩選。 本文說明如何使用資料流程腳本片段，輕鬆地將這些函式新增至您的管線。
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>建立管線

1. 選取 [ **新增管線**]。

1. 新增資料流程活動。

1. 選取 [ **來源設定** ] 索引標籤，新增來源轉換，然後將它連接到您的其中一個資料集。

    ![新增來源類型的 [來源設定] 窗格螢幕擷取畫面。](media/data-flow/snippet-adf-2.png)

    重復資料與 null 檢查程式碼片段會使用利用資料流程架構漂移的一般模式。 程式碼片段適用于您的資料集的任何架構，或是具有未預先定義架構的資料集。

1. 在 [ [資料流程腳本 (DFS) ](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)的 [使用所有資料行的相異資料列] 區段中，複製 DistinctRows 的程式碼片段。

1. 在您的 [資料流程設計師] UI 中，選取右上角的 [ **腳本** ] 按鈕，以開啟資料流程圖形後方的腳本編輯器。

    ![來原始程式碼段的螢幕擷取畫面。](media/data-flow/snippet-adf-3.png)

1. 在您的腳本中，在的定義之後 `source1` ，按 Enter，然後貼上程式碼片段。

1. 請執行下列其中一個動作：

   * 在貼上的程式碼前面輸入 **source1.rc** ，將這個貼上的程式碼片段連接到您稍早在圖形中建立的來源轉換。

   * 或者，您也可以從圖形中的 [新增轉換] 節點選取內送資料流程，以連接到設計工具中的新轉換。

     ![[條件式分割設定] 窗格的螢幕擷取畫面。](media/data-flow/snippet-adf-4.png)

   現在，您的資料流程會使用「匯總」轉換，從來源中移除重複的資料列，該轉換會在所有資料行值中使用一般雜湊，以依所有資料列分組。
    
1. 新增程式碼片段，以將您的資料分割成一個資料流程，其中包含具有 null 的資料列和另一個沒有 null 的資料流程。 若要這樣做：

   a. 返回至 [程式碼片段程式庫](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)，然後複製 null 檢查的程式碼。

   b. 在資料流程設計師中，再次選取 [ **腳本** ]，然後將這個新的轉換程式碼貼到底部。 此動作會將該轉換的名稱放在貼上的程式碼片段前面，藉此將腳本連接到您先前的轉換。

   您的資料流程圖形現在看起來應該像這樣：

    ![資料流程圖的螢幕擷取畫面。](media/data-flow/snippet-adf-1.png)

  您現在已建立具有泛型 deduping 和 null 檢查的工作資料流程，方法是從資料流程腳本庫取得現有的程式碼片段，並將它們加入至現有的設計。

## <a name="next-steps"></a>後續步驟

* 使用對應資料流程 [轉換](concepts-data-flow-overview.md)來建立其餘的資料流程邏輯。
