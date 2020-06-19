---
title: 將圖格新增至您的儀表板 | Microsoft Docs
description: 了解如何以建置者身分設定預設 Azure IoT Central 應用程式儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659033"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

[儀表板] 是當可存取應用程式的使用者瀏覽至應用程式的 URL 時所載入的頁面。 如果您從其中一個 [應用程式範本] 建立應用程式，您的應用程式將會有預先定義的儀表板可啟動。 如果您從 [自訂應用程式] 應用程式範本建立應用程式，您的儀表板將會顯示一些開始使用的秘訣。

> [!NOTE]
> 除了預設應用程式儀表板之外，使用者還可以[建立多個儀表板](howto-create-personal-dashboards.md)。 這些儀表板只能供使用者個人使用，或由應用程式的所有使用者共用。  

## <a name="add-tiles"></a>新增圖格

下列螢幕擷取畫面顯示從**自訂應用程式**範本建立的應用程式中的儀表板。 若要自訂應用程式的預設儀表板，請選取頁面左上方的 [編輯]。

> [!div class="mx-imgBorder"]
> ![以「範例 Contoso」範本為基礎的應用程式儀表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

選取 [編輯] 即可開啟儀表板程式庫面板。 程式庫包含您可以用來自訂儀表板的圖格和儀表板基本項目。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，您可以為裝置的目前溫度新增 [遙測] 圖格。 若要這樣做：

1. 選取 [裝置範本]。
1. 針對您想要在儀表板圖格中看見的裝置，從 [裝置] 選取裝置。 然後，您會看見可在圖格上使用的裝置屬性清單。
1. 若要在儀表板上建立圖格，請按一下 [溫度]，並將其拖曳至儀表板區域。 您也可以按一下 [溫度] 旁的核取方塊，然後按一下 [新增圖格]。 以下螢幕擷取畫面顯示選取 [裝置範本] 和 [裝置執行個體]，然後在儀表板上建立 [溫度遙測] 圖格。
1. 選取左上方的 [儲存]，將此圖格儲存至儀表板。

> [!div class="mx-imgBorder"]
> ![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-add-tiles-to-your-dashboard/device-details.png)

現在，操作員會在檢視預設應用程式儀表板時，看到含有裝置 [溫度] 的新圖格。 每個圖格都有預先選取的圖形、圖表等，其會在建立圖格後顯示。 不過，使用者可以選擇編輯和變更此視覺效果。  

> [!div class="mx-imgBorder"]
> ![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>編輯圖格

若要在儀表板上編輯圖格，請先按一下頁面左上方的 [編輯]，這會為儀表板及其所有圖格開啟編輯模式。  

> [!div class="mx-imgBorder"]
> ![已針對所選圖格啟用編輯模式的儀表板畫面](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然後按一下您想編輯圖格右上角的 [齒輪] 圖示。 您可以在此編輯圖格的各個層面，包括其標題、視覺效果、彙總等等。

> [!div class="mx-imgBorder"]
> ![圖格彙總設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

您也可以按一下圖格上的 [尺規] 圖示來變更圖表視覺效果。

> [!div class="mx-imgBorder"]
> ![圖格視覺效果設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>圖格類型

下表摘要說明 Azure IoT Central 中圖格的使用方式：

| 圖格 | 儀表板 | 描述
| ----------- | ------- | ------- |
| 內容 | 應用程式和裝置集儀表板 |Markdown 支援的圖格是可按式圖格，其中顯示標題和描述文字。 您也可以使用此圖格作為連結圖格，可讓使用者瀏覽至與您的應用程式相關的 URL。|
| 映像 | 應用程式和裝置集儀表板 |影像圖格會顯示自訂影像，而且可按一下。 使用影像圖格將圖形新增至儀表板，並選擇性地讓使用者瀏覽至與您的應用程式相關的 URL。|
| 標籤 | 應用程式儀表板 |標籤圖格會在儀表板上顯示自訂文字。 您可選擇文字的大小。 使用標籤圖格，將相關資訊新增至儀表板，例如描述、連絡人詳細資料或說明。|
| 對應 | 應用程式和裝置儀表板 |地圖圖格會在地圖上顯示裝置的位置。 您也可以顯示裝置的位置歷程記錄中多達 100 個點。 例如，您可以顯示裝置在過去一週的取樣路由。|
| 折線圖 | 應用程式和裝置儀表板 |折線圖圖格會顯示裝置在一段時間內的彙總測量圖表。 例如，您可以顯示折線圖，其中顯示裝置在過去一小時內的平均溫度和壓力。|
| 長條圖 | 應用程式和裝置儀表板 |橫條圖圖格會顯示裝置在一段時間內的彙總測量圖表。 例如，您可以顯示橫條圖，其中顯示裝置在過去一小時內的平均溫度和壓力。|
| 圓形圖 | 應用程式和裝置集儀表板 |圓形圖圖格會顯示裝置在一段時間內的彙總測量圖表。|
| 熱度圖 | 應用程式和裝置集儀表板 |熱度圖圖格會顯示裝置的相關資訊 (以色彩表示)。|
| 事件歷程記錄 | 應用程式和裝置儀表板 |[事件歷程記錄] 圖格會顯示裝置在一段時間內的事件。 例如，您可使用此圖格來顯示裝置在過去一小時內的所有溫度變更。|
| 狀態記錄 | 應用程式和裝置儀表板 |狀態歷程記錄圖格會顯示一段時間內的測量值。 例如，您可使用此圖格來顯示裝置在過去一小時內的溫度值。|
| KPI | 應用程式和裝置儀表板 | KPI 圖格會顯示一段時間內的彙總遙測或事件測量。 例如，您可使用此圖格來顯示裝置在過去一小時內所達到的最高溫度。|
| 上一個已知的值 | 應用程式和裝置儀表板 |上一個已知的值圖格會顯示遙測或狀態測量的最新值。 例如，您可使用此圖格來顯示裝置的最近溫度、壓力和濕度測量。 |
| 屬性 | 應用程式和裝置儀表板 | 屬性圖格會顯示裝置目前的屬性和雲端屬性值。 例如，您可使用此圖格來顯示裝置屬性，例如裝置的製造商或韌體版本。 |

### <a name="customizing-visualizations"></a>自訂視覺效果

對於折線圖、橫條圖和圓形圖，您可以在圖表中自訂由不同遙測所顯示的色彩。 若要這麼做，請選取您要自訂的遙測旁邊的調色盤圖示，然後選擇色彩。

> [!div class="mx-imgBorder"]
> ![遙測色彩顯示設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/color-customization.png)

對於字串類型的遙測或屬性，您可以選擇將文字視覺化的方式。 例如，如果您的裝置以字串遙測的形式傳送 URL，您可將該 URL 視覺化為可按式連結。 如果 URL 參考影像，您可以在上一個已知的值或屬性圖格中呈現影像。 您可藉由選取遙測名稱旁的齒輪來變更字串遙測的顯示方式。 如此一來，您即可將文字顯示為文字、連結或影像。

> [!div class="mx-imgBorder"]
> ![字串視覺效果設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>後續步驟

既然您已了解如何設定 Azure IoT Central 預設應用程式儀表板，即可[了解如何建立個人儀表板](howto-create-personal-dashboards.md)。
