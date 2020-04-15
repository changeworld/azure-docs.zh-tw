---
title: 將磁貼添加到儀錶板 |微軟文件
description: 作為生成器,瞭解如何配置預設的 Azure IoT 中央應用程式儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310291"
---
# <a name="configure-the-application-dashboard"></a>設定應用程式儀表板

儀表板**是在**有權訪問應用程式的用戶導航到應用程式的 URL 時載入的頁面。 如果從其中一個**應用程式範本**創建應用程式,則應用程式將有一個預定義的儀表板開始。 如果從**舊版應用程式**範本創建應用程式,則儀錶板將為空。

> [!NOTE]
> 除了預設應用程式儀表板之外,還可以[創建多個儀表板](howto-create-personal-dashboards.md)。 這些儀錶板只能由使用者共用,也可以在應用程式的所有用戶之間共用。 

## <a name="add-tiles"></a>新增圖格

以下螢幕截圖顯示了從**自定義應用程式**範本創建的應用程式中的儀錶板。 要自定義應用程式的預設儀表板,請選擇頁面左上角的 **「編輯**」。

> [!div class="mx-imgBorder"]
> ![以「範例 Contoso」樣本的應用程式儀表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

選擇 **「編輯**」將打開儀表板庫面板。 庫包含可用於自定義儀錶板的磁貼和儀錶板基元。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如,可以為設備的當前溫度添加**遙測**磁貼。 若要這樣做：
1. 選擇**裝置樣本**
1. 為在儀表板磁貼上檢視的裝置選擇**裝置實例**。 然後,您將看到可在磁貼上使用的設備屬性的清單。
1. 要在儀錶板上創建磁貼,請單擊「**溫度」** 並將其拖動到儀錶板區域。 您還可以按下 **「溫度**」旁邊的複選框,然後單擊「**合併**」 。 以下螢幕截圖顯示選擇設備範本和設備實例,然後在儀錶板上創建溫度遙測磁貼。
1. 選擇 **「在**左上角保存」 以將磁貼保存到儀表板。

> [!div class="mx-imgBorder"]
> ![含設定和屬性詳細資料的 [設定裝置詳細資料] 表單](media/howto-add-tiles-to-your-dashboard/device-details.png)

現在,當操作員查看默認應用程式儀錶板時,他們會看到帶有設備**溫度**的新磁貼。 每個磁貼都有一個預先選擇的圖形、圖表等,將在創建磁貼時顯示。 但是,用戶可以選擇編輯和更改此可視化效果。 

> [!div class="mx-imgBorder"]
> ![含針對圖格顯示之設定和屬性的 [儀表板] 索引標籤](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>編輯磁貼

要編輯儀錶板上的磁貼,首先按一下頁面左上角的 **「編輯」,** 這將打開儀錶板及其所有磁貼的編輯模式。 

> [!div class="mx-imgBorder"]
> ![選擇選擇的磁貼啟動編輯模式的儀表板螢幕](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然後按下要編輯的磁貼右上角的 **「齒輪」** 圖示。 在這裡,您可以編輯磁貼的各個方面,包括其標題、可視化效果、聚合等。

> [!div class="mx-imgBorder"]
> ![磁貼集合合設定的下拉清單](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

您還可以透過按下磁貼上的**尺規**圖示來更改圖表視覺化效果。

> [!div class="mx-imgBorder"]
> ![磁貼可檢視化設定的下拉清單](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磁貼類型

下表總結了 Azure IoT 中心中切片的使用方式:
 
| 圖格 | 儀表板 | 描述
| ----------- | ------- | ------- |
| 內容 | 應用程式及裝置集儀表板 |標記支援的磁貼是顯示標題和描述文本的可按下磁貼。 您還可以使用此磁貼作為連結磁貼,使用戶能夠導航到與應用程式相關的 URL。|
| 映像 | 應用程式及裝置集儀表板 |圖像磁貼顯示自定義圖像,可以按一下。 使用圖像磁貼將圖形添加到儀錶板,並選擇性地允許用戶導航到與應用程式相關的 URL。|
| 標籤 | 應用程式儀表板 |標籤磁貼在儀錶板上顯示自定義文本。 您可以選擇文字的大小。 使用標籤磁貼向儀錶板添加相關資訊,例如說明、聯繫人詳細資訊或説明。|
| 對應 | 應用程式與裝置儀表板 |地圖切片在地圖上顯示設備的位置。 您還可以顯示設備位置歷史記錄的 100 個點。 例如,您可以顯示設備過去一周所在的位置的採樣路線。|
| 折線圖 | 應用程式與裝置儀表板 |折線圖磁貼顯示設備一段時間內的聚合測量圖表。 例如,可以顯示顯示設備過去一小時的平均溫度和壓力的折線圖。|
| 長條圖 | 應用程式與裝置儀表板 |條形圖磁貼顯示設備一段時間內的聚合測量圖表。 例如,可以顯示顯示設備過去一小時的平均溫度和壓力的條形圖。|
| 圓形圖 | 應用程式及裝置集儀表板 |餅圖磁貼顯示設備一段時間內的聚合測量圖表。|
| 熱度圖 | 應用程式及裝置集儀表板 |熱圖磁貼顯示有關設備集的資訊,表示為顏色。|
| 事件歷程記錄 | 應用程式與裝置儀表板 |事件歷史記錄磁貼顯示設備在一段時間內的事件。 例如,您可以使用它顯示設備在最後一小時的所有溫度變化。|
| 狀態記錄 | 應用程式與裝置儀表板 |狀態歷史記錄磁貼顯示時間段的測量值。 例如,可以使用它顯示設備在最後一小時的溫度值。|
| KPI | 應用程式與裝置儀表板 | KPI 磁貼顯示一段時間內的聚合遙測或事件度量。 例如,您可以使用它來顯示設備在最後一小時達到的最高溫度。|
| 最後一個已知值 | 應用程式與裝置儀表板 |上次已知值磁貼顯示遙測或狀態測量的最新值。 例如,可以使用此磁貼顯示設備溫度、壓力和濕度的最新測量值。|

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何設定 Azure IoT 中央預設應用程式儀表板,您可以[瞭解如何創建個人儀表板](howto-create-personal-dashboards.md)。
