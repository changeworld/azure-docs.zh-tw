---
title: 對應資料流程中的架構漂移
description: 透過結構描述漂移在 Azure Data Factory 中建置復原性資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 5edea4b3d3834d8f99159546c0279394ec3986f8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324343"
---
# <a name="schema-drift-in-mapping-data-flow"></a>對應資料流程中的架構漂移

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

架構漂移是您的來源經常變更中繼資料的情況。 欄位、資料行和類型可以即時加入、移除或變更。 在不處理架構漂移的情況下，您的資料流程會容易受到上游資料來源變更的影響。 當內送資料行和欄位變更時，一般的 ETL 模式會失敗，因為它們通常會系結至這些來源名稱。

若要防止架構漂移，請務必讓資料流程工具中的設施可讓您以資料工程師的形式提供：

* 定義具有可變動功能變數名稱、資料類型、值和大小的來源
* 定義可搭配資料模式 (而不是硬式編碼的欄位和值) 運作的轉換參數
* 定義一些運算式，以了解要比對傳入欄位的模式，而不需使用具名欄位

Azure Data Factory 原本就支援從執行變更為執行的彈性架構，如此您就可以建立泛型資料轉換邏輯，而不需要重新編譯您的資料流程。

您需要在資料流程中做出架構決策，才能接受整個流程的結構描述漂移。 當您這樣做時，您可以抵抗來自來源的結構描述變更。 不過，您將會在整個資料流程中遺失資料行和類型的早期繫結。 Azure Data Factory 會將架構漂移流程視為晚期繫結流程，因此當您建立轉換時，漂移資料行名稱在整個流程的架構視圖中都不會提供給您使用。

這段影片提供一些複雜解決方案的簡介，這些解決方案可讓您輕鬆地在 ADF 中使用資料流程的架構漂移功能來建立。 在此範例中，我們會根據彈性資料庫架構來建立可重複使用的模式：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>來源中的架構漂移

從來源定義進入資料流程的資料行，在來源投射中不存在時，會定義為 "漂移"。 您可以從來源轉換的 [預測] 索引標籤中，查看來源投射。 當您選取來源的資料集時，ADF 會自動從資料集取得架構，並從該資料集架構定義建立投影。

在來源轉換中，架構漂移定義為讀取未定義資料集架構的資料行。 若要啟用架構漂移，請勾選 [允許來源轉換中的 **架構漂移** ]。

![架構漂移來源](media/data-flow/schemadrift001.png "架構漂移來源")

啟用架構漂移時，在執行期間會從來源讀取所有傳入的欄位，並將整個流程傳遞至接收。 根據預設，所有新偵測到的資料行（稱為 *漂移資料行*）都會抵達為字串資料類型。 如果您希望資料流程自動推斷漂移資料行的資料類型，請在來源設定中檢查 **推斷漂移資料行類型** 。

## <a name="schema-drift-in-sink"></a>接收中的架構漂移

在接收轉換中，架構漂移是當您在接收資料架構中定義的資料行上方寫入額外的資料行時。 若要啟用架構漂移，請檢查接收轉換中的 [ **允許架構漂移** ]。

![架構漂移接收](media/data-flow/schemadrift002.png "架構漂移接收")

如果已啟用架構漂移，請確定已開啟 [對應] 索引標籤中的 **自動對應** 滑杆。 在上使用此滑杆時，所有傳入的資料行都會寫入目的地。 否則，您必須使用以規則為基礎的對應來寫入漂移資料行。

![接收自動對應](media/data-flow/automap.png "接收自動對應")

## <a name="transforming-drifted-columns"></a>轉換漂移資料行

當您的資料流程有漂移資料行時，您可以使用下列方法在轉換中存取這些資料行：

* 使用 `byPosition` 和 `byName` 運算式，依名稱或位置編號明確參考資料行。
* 在衍生的資料行或匯總轉換中加入資料行模式，以符合名稱、資料流程、位置、來源或類型的任何組合
* 在 Select 或 Sink 轉換中新增以規則為基礎的對應，以透過模式將漂移資料行對應到資料行別名

如需如何執行資料行模式的詳細資訊，請參閱 [對應資料流程中的資料行模式](concepts-data-flow-column-pattern.md)。

### <a name="map-drifted-columns-quick-action"></a>地圖漂移資料行快速動作

若要明確地參考漂移資料行，您可以透過資料預覽快速動作，快速產生這些資料行的對應。 開啟 [偵錯工具模式](concepts-data-flow-debug-mode.md) 之後，請移至 [資料預覽] 索引標籤， **然後按一下 [** 重新整理] 以提取資料預覽。 如果 data factory 偵測到漂移資料行存在，您可以按一下 [ **地圖漂移** ] 並產生衍生的資料行，讓您在下游的架構視圖中參考所有漂移資料行。

![地圖漂移](media/data-flow/mapdrifted1.png "地圖漂移")

在產生的「衍生的資料行」轉換中，每個漂移資料行都會對應至其偵測到的名稱和資料類型。 在上述的資料預覽中，會將資料行 ' movieId ' 偵測為整數。 按一下 **Map 漂移** 之後，movieId 會定義在衍生的資料行中， `toInteger(byName('movieId'))` 並包含在下游轉換的架構視圖中。

![地圖漂移](media/data-flow/mapdrifted2.png "地圖漂移")

## <a name="next-steps"></a>後續步驟
在 [資料流程運算式語言](data-flow-expression-functions.md)中，您可以找到資料行模式和架構漂移的額外功能，包括 "byName" 和 "byPosition"。
