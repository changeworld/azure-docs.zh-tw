---
title: 將磁貼添加到儀表板 |微軟文檔
description: 作為產生器，瞭解如何配置預設的 Azure IoT 中央應用程式儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158720"
---
# <a name="configure-the-application-dashboard"></a>配置應用程式儀表板

儀表板**是在**有權訪問應用程式的使用者導航到應用程式的 URL 時載入的頁面。 如果從其中一個**應用程式範本**創建應用程式，則應用程式將有一個預定義的儀表板開始。 如果從**繼承應用程式**範本創建應用程式，則儀表板將為空。

> [!NOTE]
> 除了預設應用程式儀表板之外，還可以[創建多個儀表板](howto-create-personal-dashboards.md)。 這些儀表板只能由使用者共用，也可以在應用程式的所有使用者之間共用。 

## <a name="add-tiles"></a>新增圖格

以下螢幕截圖顯示了從**自訂應用程式**範本創建的應用程式中的儀表板。 要自訂應用程式的預設儀表板，請選擇頁面左上角的 **"編輯**"。

> [!div class="mx-imgBorder"]
> ![基於"示例 Contoso"範本的應用程式儀表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

選擇 **"編輯**"將打開儀表板庫面板。 庫包含可用於自訂儀表板的磁貼和儀表板基元。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，可以為設備的當前溫度添加**遙測**磁貼。 若要這樣做：
1. 選擇**設備範本**
1. 為要在儀表板磁貼上查看的設備選擇**設備實例**。 然後，您將看到可在磁貼上使用的裝置屬性的清單。
1. 要在儀表板上創建磁貼，請按一下"**溫度"** 並將其拖動到儀表板區域。 您還可以按一下 **"溫度**"旁邊的核取方塊，然後按一下"**合併**"。 以下螢幕截圖顯示選擇設備範本和設備實例，然後在儀表板上創建溫度遙測磁貼。
1. 選擇 **"在**左上角保存"以將磁貼保存到儀表板。

> [!div class="mx-imgBorder"]
> ![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-add-tiles-to-your-dashboard/device-details.png)

現在，當操作員查看預設應用程式儀表板時，他們會看到帶有設備**溫度**的新磁貼。 每個磁貼都有一個預先選擇的圖形、圖表等，將在創建磁貼時顯示。 但是，使用者可以選擇編輯和更改此視覺化效果。 

> [!div class="mx-imgBorder"]
> ![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>編輯磁貼

要編輯儀表板上的磁貼，首先按一下頁面左上角的 **"編輯"，** 這將打開儀表板及其所有磁貼的編輯模式。 

> [!div class="mx-imgBorder"]
> ![為所選磁貼啟動編輯模式的儀表板螢幕](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然後按一下要編輯的磁貼右上角的 **"齒輪"** 圖示。 在這裡，您可以編輯磁貼的各個方面，包括其標題、視覺化效果、聚合等。

> [!div class="mx-imgBorder"]
> ![磁貼聚合設置的下拉清單](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

您還可以通過按一下磁貼上的**尺規**圖示來更改圖表視覺化效果。

> [!div class="mx-imgBorder"]
> ![磁貼視覺化設置的下拉清單](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磁貼類型

下表總結了 Azure IoT 中心中切片的使用方式：
 
| 圖格 | 儀表板 | 描述
| ----------- | ------- | ------- |
| 內容 | 應用程式和設備集儀表板 |標記支援的磁貼是顯示標題和描述文本的可按一下磁貼。 您還可以使用此磁貼作為連結磁貼，使使用者能夠導航到與應用程式相關的 URL。|
| 映像 | 應用程式和設備集儀表板 |圖像磁貼顯示自訂圖像，可以按一下。 使用圖像磁貼將圖形添加到儀表板，並選擇性地允許使用者導航到與應用程式相關的 URL。|
| 標籤 | 應用程式儀表板 |標籤磁貼在儀表板上顯示自訂文本。 您可以選擇文本的大小。 使用標籤磁貼向儀表板添加相關資訊，例如說明、連絡人詳細資訊或説明。|
| 對應 | 應用程式和設備集儀表板 |地圖切片在地圖上顯示設備的位置和狀態。 例如，您可以顯示裝置的位置以及設備風扇是否已打開。|
| 折線圖 | 應用程式和設備儀表板 |折線圖磁貼顯示裝置一段時間內的聚合測量圖表。 例如，可以顯示顯示裝置過去一小時的平均溫度和壓力的折線圖。|
| 長條圖 | 應用程式和設備儀表板 |橫條圖磁貼顯示裝置一段時間內的聚合測量圖表。 例如，可以顯示顯示裝置過去一小時的平均溫度和壓力的橫條圖。|
| 圓形圖 | 應用程式和設備集儀表板 |圓形圖磁貼顯示裝置一段時間內的聚合測量圖表。|
| 熱度圖 | 應用程式和設備集儀表板 |熱圖磁貼顯示有關設備集的資訊，表示為顏色。|
| 事件歷史記錄 | 應用程式和設備儀表板 |事件歷史記錄磁貼顯示裝置在一段時間內的事件。 例如，您可以使用它顯示裝置在最後一小時的所有溫度變化。|
| 狀態記錄 | 應用程式和設備儀表板 |狀態歷史記錄磁貼顯示時間段的測量值。 例如，可以使用它顯示裝置在最後一小時的溫度值。|
| KPI | 應用程式和設備儀表板 | KPI 磁貼顯示一段時間內的聚合遙測或事件度量。 例如，您可以使用它來顯示裝置在最後一小時達到的最高溫度。|
| 最後一個已知值 | 應用程式和設備儀表板 |上次已知值磁貼顯示遙測或狀態測量的最新值。 例如，可以使用此磁貼顯示裝置溫度、壓力和濕度的最新測量值。|

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何配置 Azure IoT 中央預設應用程式儀表板，您可以[瞭解如何創建個人儀表板](howto-create-personal-dashboards.md)。
