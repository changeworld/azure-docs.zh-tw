---
title: 新增 LONG 資料型別的支援 |Microsoft Docs
description: LONG 資料型別的支援
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: ebb62b67b56134902f2752b43dd25fb0a7c6ccd4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045768"
---
# <a name="adding-support-for-long-data-type"></a>新增 long 資料類型的支援

這些變更只會套用至預覽（PAYG）環境。 如果您有 Standard （S） SKU TSI 環境，您可能會忽略這些變更。

我們會變更在 Azure 時間序列深入解析 Preview 中儲存和編制數值資料索引的方式，這可能會對您造成影響。 如果您受到下列任何一種情況的影響，請儘快進行必要的變更。 視您的地區而定，您的資料將會開始編制為長的索引，並在2020年6月29日之間加上30。 如果您有任何關於這種變更的問題或疑慮，請透過 Azure 入口網站提交支援票證，並提及此通訊。

這項變更會影響您在下列情況下的情況：

1. 如果您目前使用時間序列模型變數，並只在遙測資料中傳送整數資料類型。
1. 如果您目前使用時間序列模型變數，並在您的遙測資料中傳送整數和非整數資料類型。
1. 如果您使用分類變數將整數值對應至類別目錄。
1. 如果您使用 JavaScript SDK 來建立自訂的前端應用程式。
1. 如果您接近暖存放區（WS）中的 1000-屬性名稱限制，並同時傳送整數和非整數資料，則可以在[Azure 入口網站](https://portal.azure.com/)中將屬性計數視為度量。

如果上述任一案例適用于您，您必須對模型進行變更，以配合這項變更。 在 TSI Explorer 和任何自訂用戶端中，使用我們的 Api 搭配建議的變更，來更新變數定義中的時間序列運算式。 如需詳細資訊，請參閱下文。

根據您的 IoT 解決方案和條件約束，您可能無法看到傳送至 TSI PAYG 環境的資料。 如果您不確定您的資料是唯一的，或是整數和非整數，您有幾個選項。 您可以等候釋放功能，然後在 explorer UI 中探索原始事件，以瞭解哪些屬性已經儲存在兩個不同的資料行中。 您可以事先針對所有數值標記進行下列變更，或暫時將事件子集路由至儲存體，以進一步瞭解並探索您的架構。 若要儲存事件，請開啟事件中樞的[事件捕捉](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)，或從您的 IoT 中樞[路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)至 Azure Blob 儲存體。 您也可以透過[事件中樞瀏覽器](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)或使用[事件處理器主機](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)來觀察資料。 如果您使用 IoT 中樞，請參閱[這裡](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)的檔，以瞭解如何存取內建端點。

請注意，如果您受到這些變更的影響，而且無法由上述日期加以使用，您可能會遇到中斷，其中透過查詢 Api 或時間序列深入解析總管存取的受影響時間序列變數會傳回*null* （也就是在 Explorer 中顯示 [沒有資料]）。

## <a name="recommended-changes"></a>建議的變更

案例 1 & 2：**使用時間序列模型變數，並只傳送整數資料類型，或在遙測資料中傳送整數和非整數類型。**

如果您目前正在傳送整數遙測資料，您的資料會分成兩個數據行：「propertyValue_double」和「propertyValue_long」。

當變更生效時，您的整數資料會寫入「propertyValue_long」，而先前的內嵌（以及未來的內嵌）將不會複製 "propertyValue_double" 中的數值資料。

如果您想要針對 "propertyValue" 屬性查詢這兩個數據行的資料，就必須在 TSX 中使用*聯合（）* 純量函數。 函數接受相同資料類型的引數，並傳回引數清單中的第一個非 null 值（請參閱[這裡](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)的詳細資訊）。

### <a name="variable-definition-in-time-series-explorer---numeric"></a>時間序列瀏覽器中的變數定義-數值

*先前的變數定義：*

[![先前的變數定義](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新增變數定義：*

[![新增變數定義](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

您也可以使用「*合併（$Event toDouble （$event）* 」做為自訂[時間序列運算式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>使用時間序列查詢 Api 的內嵌變數定義-數值

*先前的變數定義：*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*新增變數定義：*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

您也可以使用「*合併（$Event toDouble （$event）* 」做為自訂[時間序列運算式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> 我們建議您在所有可能使用的位置更新這些變數（時間序列模型、儲存的查詢、Power BI 的連接器查詢）。

案例3：**使用類別變數將整數值對應至分類**

如果您目前使用的類別變數會將整數值對應至分類，您可能會使用 toLong 函數，將 Double 類型的資料轉換成 Long 類型。 如同上述案例，您必須合併 Double 和 LONG 資料型別的資料行。

### <a name="variable-definition-in-time-series-explorer---categorical"></a>時間序列瀏覽器中的變數定義-類別

*先前的變數定義：*

[![先前的變數定義](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新增變數定義：*

[![新增變數定義](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

您也可以使用「*合併（$Event toDouble （$event）* 」做為自訂[時間序列運算式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

類別變數仍然需要值為整數類型。 在自訂[時間序列運算式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中，聯合（）中所有引數的資料類型必須是 Long 類型。

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>使用時間序列查詢 Api 的內嵌變數定義-類別

*先前的變數定義：*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "tolong($event.propertyValue.Double)"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

*新增變數定義：*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

類別變數仍然需要值為整數類型。 在自訂[時間序列運算式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中，聯合（）中所有引數的資料類型必須是 Long 類型。

> [!NOTE]
> 我們建議您在所有可能使用的位置更新這些變數（時間序列模型、儲存的查詢、Power BI 的連接器查詢）。

案例4：**使用 JAVASCRIPT SDK 來建立自訂前端應用程式**

如果您受到上述案例1-3 的影響，並建立自訂應用程式，您必須更新查詢以使用*聯合（）* 函式，如上述範例中所示。

案例5：**接近暖存放區1000屬性限制**

如果您是具有大量屬性的暖存放區使用者，並認為這項變更會將您的環境推送至 1000 WS 屬性名稱限制，請透過 Azure 入口網站提交支援票證，並提及這項通訊。

## <a name="next-steps"></a>後續步驟

* 請參閱[這](concepts-supported-data-types.md)篇文章，以查看支援的資料類型完整清單。
