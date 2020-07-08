---
title: 建立 Azure IoT Central 個人儀表板 | Microsoft Docs
description: 以使用者身分了解如何建立及管理您的個人儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f7dca7352a49e668231f64632371a445985e4de3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83634566"
---
# <a name="create-and-manage-multiple-dashboards"></a>建立和管理多個儀表板

**儀表板**是您第一次導覽至應用程式時載入的頁面。 應用程式中的**建立器**會為所有使用者定義預設應用程式儀表板。 此外，您還可以建立自己的個人化應用程式儀表板。 您可以具有多個顯示不同資料的儀表板，並在它們之間進行切換。

如果您是應用程式的**管理員**，您也可建立最多 10 個應用程式層級的儀表板，以便與應用程式的其他使用者共用。 只有**管理員**才能夠建立、編輯和刪除應用程式層級儀表板。  

## <a name="create-dashboard"></a>建立儀表板

下列螢幕擷取畫面顯示從**自訂應用程式**範本建立的應用程式儀表板。 您可以使用個人儀表板取代預設的應用程式儀表板，或如果您是管理員，可使用另一個應用程式層級儀表板來取代。 若要這麼做，請選取頁面左上方的 [+ 新增]。

> [!div class="mx-imgBorder"]
> ![以「自訂應用程式」範本為基礎的應用程式儀表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

選取 [+ 新增]，開啟儀表板編輯器。 在編輯器中，您可以為儀表板命名，並從程式庫選擇項目。 程式庫包含您可以用來自訂儀表板的圖格和儀表板基本項目。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-create-personal-dashboards/dashboard-library.png)

如果您是應用程式的**管理員**，系統會提供選項，以供您建立個人層級儀表板或應用程式層級儀表板。 如果您建立個人層級儀表板，則只有您才能看見該儀表板。 如果您建立應用程式層級儀表板，應用程式的每個使用者皆可看見該儀表板。 輸入標題並選取您要建立的儀表板類型之後，您可以稍後再儲存並新增圖格。 或者，如果您現在已經準備好，並已新增裝置範本和裝置執行個體，您可以繼續建立第一個圖格。  

> [!div class="mx-imgBorder"]
> ![[設定裝置詳細資料] 表單，其中包含溫度的詳細資料](media/howto-create-personal-dashboards/device-details.png)

例如，您可以為裝置的目前溫度新增 [遙測] 圖格。 若要這樣做：

1. 選取 [裝置範本]
1. 針對您想要在儀表板圖格中看見的裝置，從 [裝置] 選取裝置。 然後，您會看見可在圖格上使用的裝置屬性清單。
1. 若要在儀表板上建立圖格，請按一下 [溫度]，並將其拖曳至儀表板區域。 您也可以按一下 [溫度] 旁的核取方塊，然後按一下 [新增圖格]。 以下螢幕擷取畫面顯示選取 [裝置範本] 和裝置，然後在儀表板上建立 [溫度遙測] 圖格。
1. 選取左上方的 [儲存]，以將您的變更儲存至儀表板。

> [!div class="mx-imgBorder"]
> ![[儀表板] 索引標籤，其中包含 [溫度] 圖格的詳細資料](media/howto-create-personal-dashboards/temperature-tile-edit.png)

現在，當您檢視個人儀表板時，會看見新的圖格，其中包含該裝置的 [溫度] 設定：

> [!div class="mx-imgBorder"]
> ![[儀表板] 索引標籤，其中包含 [溫度] 圖格的詳細資料](media/howto-create-personal-dashboards/temperature-tile-complete.png)

您可以在程式庫中瀏覽其他圖格類型，以探索如何進一步自訂您的自訂儀表板。

若要深入了解如何在 Azure IoT Central 中使用圖格，請參閱[將圖格新增至儀表板](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理儀表板

您可以有數個個人儀表板，並在這些儀表板之間切換，或從其中一個預設應用程式儀表板進行選擇：

> [!div class="mx-imgBorder"]
> ![在儀表板之間切換](media/howto-create-personal-dashboards/switch-dashboards.png)

您可以編輯您的個人儀表板，並刪除不再需要的任何儀表板。 如果您是**管理員**，您也可以編輯或刪除應用程式層級儀表板。

> [!div class="mx-imgBorder"]
> ![刪除儀表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>後續步驟

現在您已了解如何建立和管理個人儀表板，您可以[了解如何管理您的應用程式喜好設定](howto-manage-preferences.md)。
