---
title: 將磚新增至您的 Azure IoT Central 儀表板 |Microsoft Docs
description: 作為建立者，瞭解如何使用磚設定預設的 Azure IoT Central 應用程式儀表板。
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5276f9b8c6dd3bdc305142e5b9452cd9c5d60bce
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376976"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

當您連接至 IoT Central 應用程式時， **儀表板** 是您看到的第一個頁面。 如果您是從其中一個以產業為主的 [應用程式範本](./concepts-app-templates.md)建立應用程式，則您的應用程式會有預先定義的儀表板開始。 如果您是從自訂 [應用程式範本](./concepts-app-templates.md)建立應用程式，您的儀表板會顯示一些開始使用的秘訣。

> [!TIP]
> 除了預設應用程式儀表板之外，使用者還可以[建立多個儀表板](howto-create-personal-dashboards.md)。 這些儀表板只能供使用者個人使用，或由應用程式的所有使用者共用。  

## <a name="add-tiles"></a>新增圖格

下列螢幕擷取畫面顯示從 **自訂應用程式** 範本所建立之應用程式中的儀表板。 若要自訂目前的儀表板，請選取 [ **編輯** ]，以新增自訂個人或共用儀表板，選取 [ **新增** ]：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="以自訂應用程式範本為基礎之應用程式的儀表板":::

當您選取 [ **編輯** ] 或 [ **新增** ] 之後，儀表板就會處於 *編輯* 模式。 您可以使用 [ **編輯儀表板** ] 面板中的工具，將磚新增至儀表板，並自訂和移除儀表板上的磚。 例如，若要新增 **遙測** 磚，以顯示一或多個裝置回報的目前溫度：

1. 在 [ **編輯儀表板** ] 面板中，選取 **裝置群組** 。
1. 在 [ **裝置** ] 下拉式清單中選取一或多個裝置，以顯示在磚上。 您現在會看到來自裝置的可用遙測、屬性和命令。
1. 選取 [遙測] 區段中的 [ **溫度** ]，然後選取 [ **新增磚** ]。 磚現在會顯示在儀表板上，您可以在其中變更視覺效果、調整磚大小並加以設定：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="將溫度遙測磚新增至儀表板":::

當您完成新增和自訂儀表板上的磚時，請選取 [ **儲存** ]。

## <a name="customize-tiles"></a>自訂磚

若要自訂儀表板上的磚，儀表板必須處於編輯模式。 可用的自訂選項取決於 [圖格類型](#tile-types)：

* 磚上的尺規圖示可讓您變更視覺效果。 視覺效果包括折線圖、最後一個已知值和熱度圖。

* 正方形圖示可讓您調整圖格的大小。

* 齒輪圖示可讓您設定視覺效果。 例如，您可以選擇在折線圖視覺效果中顯示圖例和軸，然後選擇要繪製的時間範圍。

## <a name="tile-types"></a>圖格類型

下表說明您可以新增至儀表板的不同圖格類型：

| 磚             | 描述 |
| ---------------- | ----------- |
| Markdown         | Markdown 圖格是可按一下的圖格，其會顯示使用 Markdown 格式化的標題和描述文字。 URL 可以是應用程式中其他頁面的相對連結，也可以是外部網站的絕對連結。|
| 映像            | 影像圖格會顯示自訂影像，而且可按一下。 URL 可以是應用程式中其他頁面的相對連結，也可以是外部網站的絕對連結。|
| 標籤            | 標籤圖格會在儀表板上顯示自訂文字。 您可選擇文字的大小。 使用標籤圖格，將相關資訊新增至儀表板，例如描述、連絡人詳細資料或說明。|
| 計數            | 計數磚會顯示裝置群組中的裝置數目。|
| 對應              | 地圖底圖會在地圖上顯示一或多個裝置的位置。 您也可以顯示裝置的位置歷程記錄中多達 100 個點。 例如，您可以顯示過去一周內裝置的取樣路由。|
| KPI              |  KPI 磚會在一段時間內顯示一或多個裝置的匯總遙測值。 例如，您可以使用它來顯示過去一小時內一或多個裝置的最大溫度和壓力。|
| 折線圖       | 折線圖圖格會在一段時間內為一或多個裝置繪製一或多個匯總遙測值。 例如，您可以顯示折線圖來繪製過去一小時內一或多個裝置的平均溫度和壓力。|
| 橫條圖        | 橫條圖圖格會在一段時間內為一或多個裝置繪製一或多個匯總遙測值。 例如，您可以顯示橫條圖，以顯示過去一小時內一或多個裝置的平均溫度和壓力。|
| 圓形圖        | 圓形圖圖格會針對一或多個裝置顯示一或多個匯總遙測值一段時間。|
| 熱度圖         | 熱度圖磚會顯示一或多個裝置的相關資訊，以色彩表示。|
| 上一個已知的值 | 最後一個已知值的磚會顯示一或多個裝置的最新遙測值。 例如，您可以使用此磚來顯示一或多個裝置的最新溫度、壓力和濕度值。 |
| 事件歷程記錄    | [事件歷程記錄] 圖格會顯示裝置在一段時間內的事件。 例如，您可以使用它來顯示過去一小時內一或多個裝置的所有閥門開啟和關閉事件。|
| 屬性         |  屬性圖格會顯示一或多個裝置的屬性和雲端屬性目前的值。 例如，您可以使用此磚來顯示裝置屬性，例如裝置的製造商或固件版本。 |

目前，您最多可以將10個裝置新增至支援多個裝置的磚。

### <a name="customizing-visualizations"></a>自訂視覺效果

依預設，折線圖會顯示一段時間的資料。 選取的時間範圍會分割成50的相等大小值區，然後將裝置資料匯總為每個值區，以在選取的時間範圍內提供50個資料點。 如果您想要查看原始資料，您可以變更您的選取專案，以查看最後的100值。 若要變更時間範圍或選取原始資料視覺效果，請使用 [ **設定圖表** ] 面板中的 [顯示範圍] 下拉式清單。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="變更折線圖的顯示範圍":::

針對顯示匯總值的磚，請在 [ **設定圖表** ] 面板中選取 [遙測類型] 旁邊的齒輪圖示，以選擇匯總。 您可以選擇平均、總和、最大值、最小值和計數。

針對折線圖、橫條圖和圓形圖，您可以自訂不同遙測值的色彩。 選取您要自訂的遙測旁的 [調色板] 圖示：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="變更遙測值的色彩":::

針對顯示字串屬性或遙測值的圖格，您可以選擇顯示文字的方式。 例如，如果裝置將 URL 儲存在字串屬性中，您可以將它顯示為可按的連結。 如果 URL 參考影像，您可以在上一個已知的值或屬性圖格中呈現影像。 若要變更字串的顯示方式，請在圖格設定中選取遙測類型或屬性旁的齒輪圖示：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="變更字串在磚上的顯示方式":::

針對數值 **KPI** 、 **最後一個已知值** 和 **屬性** 圖格，您可以使用條件式格式設定，根據其目前的值自訂磚的色彩。 若要新增條件式格式設定，請選取磚上的 [ **設定** ]，然後選取要自訂之值旁的 [ **條件式格式設定** ] 圖示：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="顯示如何尋找磚設定選項的螢幕擷取畫面，以及條件式格式化圖示":::

新增您的條件式格式化規則：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="顯示平均流程的條件式格式規則的螢幕擷取畫面。有三個規則-less 20 為綠色、小於50為黃色，而任何50為紅色":::

下列螢幕擷取畫面顯示條件式格式設定規則的效果：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="螢幕擷取畫面，顯示平均水流程磚上的黃色背景色彩。磚上的數位是40.84":::

## <a name="next-steps"></a>後續步驟

既然您已了解如何設定 Azure IoT Central 預設應用程式儀表板，即可[了解如何建立個人儀表板](howto-create-personal-dashboards.md)。
