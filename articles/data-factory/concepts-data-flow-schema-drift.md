---
title: 映射資料流程中的架構漂移
description: 透過結構描述漂移在 Azure Data Factory 中建置復原性資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2019
ms.openlocfilehash: 9daf7973a2e48f866a8d0b93a682851d31dc3af7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928546"
---
# <a name="schema-drift-in-mapping-data-flow"></a>映射資料流程中的架構漂移

架構漂移是源經常更改中繼資料的情況。 欄位、列和 類型可以動態添加、刪除或更改。 如果不處理架構漂移，資料流程就容易受到上游資料來源更改的影響。 當傳入列和欄位發生更改時，典型的 ETL 模式將失敗，因為它們往往綁定到這些源名稱。

為了防止架構漂移，在資料流程工具中擁有這些設施非常重要，以便您作為資料工程師：

* 定義具有可變欄位名稱、資料類型、值和大小的源
* 定義可搭配資料模式 (而不是硬式編碼的欄位和值) 運作的轉換參數
* 定義一些運算式，以了解要比對傳入欄位的模式，而不需使用具名欄位

Azure 資料工廠本機支援從執行更改為執行的靈活架構，以便無需重新編譯資料流程即可構建通用資料轉換邏輯。

您需要在資料流程中做出架構決策，才能接受整個流程的結構描述漂移。 當您這樣做時，您可以抵抗來自來源的結構描述變更。 但是，您將在整個資料流程中丟失列和類型的早期繫結。 Azure 資料工廠將架構漂移流視為後期綁定流，因此，在生成轉換時，漂移列名稱將在整個流的架構視圖中不可用。

## <a name="schema-drift-in-source"></a>架構漂移在源

來自源定義的資料流程的列在源投影中不存在時定義為"漂移"。 您可以在源轉換中的投影選項卡中查看源投影。 當您為源選擇資料集時，ADF 將自動從資料集獲取架構，並從該資料集架構定義創建專案。

在源轉換中，架構漂移定義為未定義資料集架構的讀取列。 要啟用架構漂移，請在源轉換中**檢查允許架構漂移**。

![架構漂移源](media/data-flow/schemadrift001.png "架構漂移源")

啟用架構漂移後，在執行期間從源讀取所有傳入欄位，並將整個流傳遞到接收器。 預設情況下，所有新檢測到的列（稱為*漂移列*）都作為字串資料類型到達。 如果希望資料流程自動推斷漂移列的資料類型，請在源設置中檢查**推斷漂移列類型**。

## <a name="schema-drift-in-sink"></a>架構漂移在水槽中

在接收器轉換中，架構漂移是在接收器資料架構中定義的內容之上寫入其他列時。 要啟用架構漂移，請在接收器轉換中**檢查允許架構漂移**。

![架構漂移接收器](media/data-flow/schemadrift002.png "架構漂移接收器")

如果啟用了架構漂移，請確保"映射"選項卡中的**自動映射**滑塊已打開。 打開此滑塊後，所有傳入列將寫入目標。 否則，必須使用基於規則的映射來編寫漂移列。

![沉自動映射](media/data-flow/automap.png "沉自動映射")

## <a name="transforming-drifted-columns"></a>變換漂移列

當資料流程漂移列時，可以使用以下方法在轉換中訪問它們：

* 使用`byPosition`和`byName`運算式按名稱或位置編號顯式引用列。
* 在派生列或聚合變換中添加列模式，以匹配名稱、流、位置或類型的任意組合
* 在 Select 或 Sink 轉換中添加基於規則的映射，以便通過模式將漂移列與列別名匹配

有關如何實現列模式的詳細資訊，請參閱[映射資料流程的列模式](concepts-data-flow-column-pattern.md)。

### <a name="map-drifted-columns-quick-action"></a>映射漂移列快速操作

要顯式引用漂移列，可以通過資料預覽快速操作快速生成這些列的映射。 [啟動偵錯模式](concepts-data-flow-debug-mode.md)後，轉到"資料預覽"選項卡，然後按一下"**刷新"** 以獲取資料預覽。 如果資料工廠檢測到存在漂移列，則可以按一下 **"映射漂移"** 並生成派生列，允許您引用下游架構視圖中的所有漂移列。

![地圖漂移](media/data-flow/mapdrifted1.png "地圖漂移")

在生成的派生列轉換中，每個漂移列映射到其檢測到的名稱和資料類型。 在上述資料預覽中，將檢測出列"movieId"為整數。 按一下 **"地圖漂移**"後，movieId 在派生列`toInteger(byName('movieId'))`中定義為並包含在下游轉換中的架構視圖中。

![地圖漂移](media/data-flow/mapdrifted2.png "地圖漂移")

## <a name="next-steps"></a>後續步驟
在["資料流程運算式語言](data-flow-expression-functions.md)"中，您將找到列模式和架構漂移的其他工具，包括"按名稱"和"按位置"。
