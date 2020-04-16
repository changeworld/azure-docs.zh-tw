---
title: 在 Azure 資料工廠中使用映射資料流程處理固定長度的文字檔
description: 瞭解如何使用對應資料流在 Azure 資料工廠中處理固定長度的文本檔。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414379"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>使用資料工廠映射資料流程處理固定長度的文字檔

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

通過使用 Microsoft Azure 數據工廠中的映射數據流,可以從固定寬度文本檔轉換數據。 在以下任務中,我們將為沒有分隔符的文本檔定義數據集,然後根據單位位置設置子字串拆分。

## <a name="create-a-pipeline"></a>建立管線

1. 選擇 **「新建管道**」 以建立新管道。

2. 新增資料串流活動,該活動將用於處理固定寬度檔案:

    ![固定寬度導管](media/data-flow/fwpipe.png)

3. 在資料串流活動中,選擇 **「新建映射資料串**流」。

4. 新增來源、衍生式、選擇及接收器轉換:

    ![固定寬度資料流程](media/data-flow/fw2.png)

5. 將源轉換配置為使用新資料集,該資料集將屬於"分隔文字"類型。

6. 不要設置任何列分隔符或標題。

   現在,我們將為此檔的內容設置欄位起始點和長度:

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

7. 在"源"轉換的 **"投影"** 選項卡上,應看到名為*Column_1*的字串列。

8. 在"派生"列中,創建新列。

9. 我們將給列簡單名稱,如*col1。*

10. 在表示式產生器中,鍵入以下內容:

    ```substring(Column_1,1,4)```

    ![衍生的資料行](media/data-flow/fwderivedcol1.png)

11. 對於需要分析的所有列,重複步驟 10。

12. 選擇 **「檢查**」選項卡以檢視將產生新欄:

    ![檢查](media/data-flow/fwinspect.png)

13. 使用「選擇」轉換刪除不需要轉換的任何欄位:

    ![選擇轉換](media/data-flow/fwselect.png)

14. 使用「接收器」將資料輸出到資料夾:

    ![固定寬度水槽](media/data-flow/fwsink.png)

    以下是輸出的外觀:

    ![固定寬度輸出](media/data-flow/fxdoutput.png)

  固定寬度數據現在被拆分,每個字元有四個字元,並分配給 Col1、Col2、Col3、Col4 等。 根據前面的示例,數據分為四列。

## <a name="next-steps"></a>後續步驟

* 通過使用映射數據流[轉換](concepts-data-flow-overview.md)構建數據流邏輯的其餘部分。
