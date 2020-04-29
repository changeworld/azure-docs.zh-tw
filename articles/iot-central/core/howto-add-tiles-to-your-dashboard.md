---
title: 將磚新增至儀表板 |Microsoft Docs
description: 作為建立者，瞭解如何設定預設的 Azure IoT Central 應用程式儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310291"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

當具有應用程式存取權的使用者流覽至應用程式的 URL 時，**儀表板**就是載入的頁面。 如果您是從其中一個**應用程式範本**建立應用程式，您的應用程式將會有預先定義的儀表板來啟動。 如果您是從**舊版應用**程式範本建立應用程式，您的儀表板將會是空白，而無法啟動。

> [!NOTE]
> 除了預設的應用程式儀表板之外，使用者也可以[建立多個儀表板](howto-create-personal-dashboards.md)。 這些儀表板只能供使用者個人或在應用程式的所有使用者之間共用。 

## <a name="add-tiles"></a>新增圖格

下列螢幕擷取畫面顯示從**自訂應用程式**範本建立的應用程式中的儀表板。 若要自訂應用程式的預設儀表板，請選取頁面左上角的 [**編輯**]。

> [!div class="mx-imgBorder"]
> ![以「範例 Contoso」範本為基礎的應用程式儀表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

選取 [**編輯**] 會開啟 [儀表板程式庫] 面板。 程式庫包含您可以用來自訂儀表板的圖格和儀表板基本專案。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，您可以為裝置的目前溫度新增**遙測**磚。 操作方法：
1. 選取**裝置範本**
1. 針對您想要在儀表板磚上看到的裝置，選取**裝置實例**。 然後您會看到可在磚上使用的裝置屬性清單。
1. 若要在儀表板上建立磚，請按一下 [**溫度**]，並將它拖曳至 [儀表板] 區域。 您也可以按一下 [**溫度**] 旁的核取方塊，然後按一下 [**合併**]。 下列螢幕擷取畫面顯示選取裝置範本和裝置實例，然後在儀表板上建立 [溫度遙測] 磚。
1. 選取左上方的 [**儲存**]，將磚儲存至儀表板。

> [!div class="mx-imgBorder"]
> ![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-add-tiles-to-your-dashboard/device-details.png)

現在，當操作員查看預設應用程式儀表板時，會看到新的磚，其中包含裝置的**溫度**。 每個磚都有預先選取的圖形、圖表等，會在建立磚時顯示。 不過，使用者可以選擇編輯和變更此視覺效果。 

> [!div class="mx-imgBorder"]
> ![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>編輯磚

若要編輯儀表板上的圖格，請先按一下頁面左上角的 [**編輯**]，這將會開啟儀表板及其所有磚的編輯模式。 

> [!div class="mx-imgBorder"]
> ![已針對選取的磚啟用編輯模式的儀表板畫面](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然後按一下您想要編輯之磚右上角的**齒輪**圖示。 您可以在這裡編輯磚的各個層面，包括其標題、視覺效果、匯總等等。

> [!div class="mx-imgBorder"]
> ![磚匯總設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

您也可以按一下圖格上的**尺規**圖示來變更圖表視覺效果。

> [!div class="mx-imgBorder"]
> ![磚視覺效果設定的下拉式清單](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磚類型

下表摘要說明 Azure IoT Central 中磚的使用方式：
 
| 圖格 | 儀表板 | 描述
| ----------- | ------- | ------- |
| 內容 | 應用程式和裝置集合儀表板 |Markdown 支援的磚是可按顯示標題和描述文字的磚。 您也可以使用此磚作為連結磚，讓使用者流覽至與您的應用程式相關的 URL。|
| Image | 應用程式和裝置集合儀表板 |影像磚會顯示自訂影像，而且可以按按一下。 使用影像磚將圖形新增至儀表板，並選擇性地讓使用者流覽至與您的應用程式相關的 URL。|
| 標籤 | 應用程式儀表板 |標籤磚：在儀表板上顯示自訂文字。 您可以選擇文字的大小。 使用 [標籤] 磚，將相關資訊加入儀表板，例如描述、連絡人詳細資料或協助。|
| 對應 | 應用程式和裝置儀表板 |地圖底圖會在地圖上顯示裝置的位置。 您也可以顯示最多100點的裝置位置歷程記錄。 例如，您可以顯示裝置在過去一周的取樣路由。|
| 折線圖 | 應用程式和裝置儀表板 |折線圖磚：顯示一段時間內裝置的匯總測量圖表。 例如，您可以顯示折線圖，其中顯示過去一小時內裝置的平均溫度和壓力。|
| 長條圖 | 應用程式和裝置儀表板 |橫條圖磚：顯示裝置在一段時間內的匯總測量圖表。 例如，您可以顯示橫條圖，其中顯示過去一小時內裝置的平均溫度和壓力。|
| 圓形圖 | 應用程式和裝置集合儀表板 |圓形圖磚會顯示一段時間內裝置的匯總測量圖表。|
| 熱度圖 | 應用程式和裝置集合儀表板 |熱度圖磚顯示裝置組的相關資訊，以色彩表示。|
| 事件歷程記錄 | 應用程式和裝置儀表板 |[事件歷程記錄] 磚會顯示裝置在一段時間內的事件。 例如，您可以使用它來顯示過去一小時內裝置的所有溫度變更。|
| 狀態記錄 | 應用程式和裝置儀表板 |[狀態歷程記錄] 磚會顯示一段時間的度量值。 例如，您可以使用它來顯示過去一小時內裝置的溫度值。|
| KPI | 應用程式和裝置儀表板 | KPI 磚會顯示一段時間的匯總遙測或事件測量。 例如，您可以使用它來顯示過去一小時內裝置達到的最高溫度。|
| 上次已知值 | 應用程式和裝置儀表板 |[上次已知值] 磚會顯示遙測或狀態測量的最新值。 例如，您可以使用此磚顯示裝置的最新溫度、壓力和濕度測量。|

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何設定 Azure IoT Central 預設應用程式儀表板，您可以[瞭解如何建立個人儀表板](howto-create-personal-dashboards.md)。
