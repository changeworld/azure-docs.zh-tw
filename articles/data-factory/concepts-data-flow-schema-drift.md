---
title: 映射資料串流中的架構漂移
description: 透過結構描述漂移在 Azure Data Factory 中建置復原性資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2019
ms.openlocfilehash: a6b3b1d11242434088c138460d968f39d5273e4f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418417"
---
# <a name="schema-drift-in-mapping-data-flow"></a>映射資料串流中的架構漂移

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

架構漂移是源經常更改元數據的情況。 欄位、列和 類型可以動態添加、刪除或更改。 如果不處理架構漂移,數據流就容易受到上游數據源更改的影響。 當傳入列和欄位發生更改時,典型的 ETL 模式將失敗,因為它們往往綁定到這些源名稱。

為了防止架構漂移,在資料流工具中擁有這些設施非常重要,以便您作為數據工程師:

* 定義具有可變欄位名稱、資料類型、值和大小的來源
* 定義可搭配資料模式 (而不是硬式編碼的欄位和值) 運作的轉換參數
* 定義一些運算式，以了解要比對傳入欄位的模式，而不需使用具名欄位

Azure 資料工廠本機支援從執行更改為執行的靈活架構,以便無需重新編譯資料流即可構建通用資料轉換邏輯。

您需要在資料流程中做出架構決策，才能接受整個流程的結構描述漂移。 當您這樣做時，您可以抵抗來自來源的結構描述變更。 但是,您將在整個資料流中丟失列和類型的早期綁定。 Azure 數據工廠將架構漂移流視為後期綁定流,因此,在生成轉換時,漂移列名稱將在整個流的架構視圖中不可用。

## <a name="schema-drift-in-source"></a>架構漂移來源

來自源定義的數據串流的列在源投影中不存在時定義為「漂移」。 您可以在源轉換中的投影選項卡中查看源投影。 當您為源選擇資料集時,ADF 將自動從資料集獲取架構,並從該資料集架構定義創建專案。

在源轉換中,架構漂移定義為未定義數據集架構的讀取列。 要開啟架構漂移,請在源轉換中**檢查讓架構漂移**。

![架構漂移源](media/data-flow/schemadrift001.png "架構漂移源")

啟用架構漂移後,在執行期間從源讀取所有傳入欄位,並將整個流傳遞到接收器。 默認情況下,所有新檢測到的列(稱為*漂移列*)都作為字串數據類型到達。 如果希望資料串流自動推斷漂移欄的資料型態,請在來源設定中檢查**推斷漂移列型態**。

## <a name="schema-drift-in-sink"></a>架構漂移在水槽中

在接收器轉換中,架構漂移是在接收器數據架構中定義的內容之上寫入其他列時。 要開啟架構漂移,請在接收器轉換中**檢查讓架構漂移**。

![架構漂移接收器](media/data-flow/schemadrift002.png "架構漂移接收器")

如果啟用了架構漂移,請確保「映射」選項卡中的**自動映射**滑塊已打開。 打開此滑塊后,所有傳入列將寫入目標。 否則,必須使用基於規則的映射來編寫漂移列。

![沉自動映射](media/data-flow/automap.png "沉自動映射")

## <a name="transforming-drifted-columns"></a>轉換到移列

當資料流漂移列時,可以使用以下方法在轉換中存取它們:

* 使用`byPosition`與`byName`表示式依名稱或位置編號顯示式引用列。
* 在衍生的群組或聚合轉換中加入列模式,以符合名稱、串流、位置或類型的任何組合
* 在 Select 或 Sink 轉換中加入基於規則的映射,以便透過模式將漂移列與欄別名符合

有關如何實現列模式的詳細資訊,請參閱[映射資料串流的欄模式](concepts-data-flow-column-pattern.md)。

### <a name="map-drifted-columns-quick-action"></a>映射漂移列快速操作

要顯式引用漂移列,可以通過數據預覽快速操作快速生成這些列的映射。 [啟動除錯模式](concepts-data-flow-debug-mode.md)後,轉到「資料預覽」選項卡,然後單擊「**刷新」** 以獲取數據預覽。 如果資料工廠檢測到存在漂移列,則可以按下 **「映射漂移」** 並生成派生列,允許您引用下游架構視圖中的所有漂移列。

![地圖漂移](media/data-flow/mapdrifted1.png "地圖漂移")

在生成的派生列轉換中,每個漂移列映射到其檢測到的名稱和數據類型。 在上述數據預覽中,將檢測出列"movieId"為整數。 點擊 **「地圖漂移**」后,movieId 在派`toInteger(byName('movieId'))`生列 中定義為並包含在下游轉換中的架構視圖中。

![地圖漂移](media/data-flow/mapdrifted2.png "地圖漂移")

## <a name="next-steps"></a>後續步驟
在[「資料流表示式語言](data-flow-expression-functions.md)」中,您將找到列模式和架構漂移的其他工具,包括"按名稱"和"按位置"。
