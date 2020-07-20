---
title: 在 Azure Data Factory 中處理具有對應資料流程的固定長度文字檔
description: 瞭解如何使用對應的資料流程，在 Azure Data Factory 中處理固定長度的文字檔。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414379"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>使用 Data Factory 對應資料流程來處理固定長度的文字檔

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

藉由使用 Microsoft Azure Data Factory 中的對應資料流程，您可以從固定寬度的文字檔轉換資料。 在下列工作中，我們將為不含分隔符號的文字檔定義資料集，然後根據序數位置設定子字串分割。

## <a name="create-a-pipeline"></a>建立管線

1. 選取 [ **+ 新增管線**] 以建立新的管線。

2. 新增「資料流程」活動，以用於處理固定寬度的檔案：

    ![固定寬度管線](media/data-flow/fwpipe.png)

3. 在 [資料流程] 活動中，選取 [**新增對應資料流程**]。

4. 新增來源、衍生的資料行、選取和接收轉換：

    ![固定寬度資料流程](media/data-flow/fw2.png)

5. 將來源轉換設定為使用新的資料集，這會是分隔的文字類型。

6. 請勿設定任何資料行分隔符號或標頭。

   現在，我們將為此檔案的內容設定欄位的起點和長度：

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 在來源轉換的 [**投射**] 索引標籤上，您應該會看到名為*Column_1*的字串資料行。

8. 在 [衍生] 資料行中，建立新的資料行。

9. 我們會為數據行提供簡單的名稱，例如*col1*。

10. 在 [運算式產生器] 中，輸入下列內容：

    ```substring(Column_1,1,4)```

    ![衍生的資料行](media/data-flow/fwderivedcol1.png)

11. 針對您需要剖析的所有資料行重複步驟10。

12. 選取 [**檢查**] 索引標籤，以查看將產生的新資料行：

    ![檢驗](media/data-flow/fwinspect.png)

13. 使用 [選取轉換] 來移除任何您不需要轉換的資料行：

    ![選取轉換](media/data-flow/fwselect.png)

14. 使用接收將資料輸出至資料夾：

    ![固定寬度接收](media/data-flow/fwsink.png)

    輸出看起來像這樣：

    ![固定寬度輸出](media/data-flow/fxdoutput.png)

  固定寬度的資料現在會進行分割，其中每個字元都有四個字元，且會指派給 Col1、Col2、Col3、Col4 等等。 根據上述範例，資料會分割成四個數據行。

## <a name="next-steps"></a>後續步驟

* 使用對應資料流程[轉換](concepts-data-flow-overview.md)來建立其餘的資料流程邏輯。
