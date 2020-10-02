---
title: 使用資料流程程式碼片段重復資料列和尋找 null
description: 瞭解如何使用資料流程中的程式碼片段，輕鬆重複使用資料列重復資料列和尋找 null
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 683d7ffe5549b86a587cd2dc3c9a86a36aee1bba
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637137"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>使用資料流程程式碼片段重復資料列和尋找 null

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

藉由在對應資料流程中使用程式碼片段，您很容易就能執行常見的工作，例如重復資料刪除和 null 篩選。 本操作指南將說明如何使用資料流程腳本片段，輕鬆地將這些函式新增至您的管線。

## <a name="create-a-pipeline"></a>建立管線

1. 選取 [ **+ 新增管線** ] 以建立新的管線。

2. 新增資料流程活動。

3. 新增來源轉換並將其連接到您的其中一個資料集

    ![來原始程式碼段2](media/data-flow/snippet-adf-2.png)

4. 重復資料與 Null 檢查程式碼片段會使用採用資料流程架構漂移的一般模式，讓它們可與資料集內的任何架構或沒有任何預先定義架構的資料集搭配使用。

5. [移至 [資料流程腳本] 檔頁面，並複製相異資料列的程式碼片段。](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. 在您的 [資料流程設計師] UI 中，按一下右上方的 [腳本] 按鈕，以開啟資料流程圖形後方的腳本編輯器。

    ![來原始程式碼段3](media/data-flow/snippet-adf-3.png)

7. 在腳本的定義之後 ```source1``` ，按 Enter 鍵，然後在程式碼片段中貼上。

8. 您會在貼上的程式碼前面輸入 "source1.rc"，將這個貼上的程式碼片段連接至您在圖形中建立的先前來源轉換。

9. 或者，您也可以從圖形中的 [新增轉換] 節點選取內送資料流程，以連接到設計工具中的新轉換。

    ![來原始程式碼段4](media/data-flow/snippet-adf-4.png)

10. 現在，您的資料流程會使用「匯總」轉換，從來源中移除重複的資料列，該轉換會在所有資料行值中使用一般雜湊來分組所有資料列。
    
11. 接下來，我們將加入程式碼片段，以將您的資料分割成包含 Null 的資料列，以及沒有任何 Null 之資料流程的資料流程。

12. [返回至程式碼片段程式庫，這次會複製 Null 檢查的程式碼。](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. 在資料流程設計師中，再次按一下 [腳本]，然後將這個新的轉換程式碼貼到底部，在貼上的程式碼片段前面輸入該轉換的名稱，將它連接到先前的轉換。

14. 您的資料流程圖形現在看起來應該像這樣：

    ![來原始程式碼段1](media/data-flow/snippet-adf-1.png)

  現在，您可以從資料流程腳本庫取得現有的程式碼片段，並將它們加入現有的設計中，以搭配泛型 deduping 和 Null 檢查來使用工作資料流程。

## <a name="next-steps"></a>後續步驟

* 使用對應資料流程 [轉換](concepts-data-flow-overview.md)來建立其餘的資料流程邏輯。
