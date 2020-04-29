---
title: 建立 Azure IoT Central 個人儀表板 |Microsoft Docs
description: 身為使用者，瞭解如何建立及管理您的個人儀表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985485"
---
# <a name="create-and-manage-multiple-dashboards"></a>建立和管理多個儀表板

[**儀表板**] 是當您第一次流覽至應用程式時所載入的頁面。 應用程式中的**builder**會定義所有使用者的預設應用程式儀表板。 此外，您還可以建立自己個人化的應用程式儀表板。 您可以有數個顯示不同資料的儀表板，並在兩者之間切換。

如果您是應用程式的系統**管理員**，您也可以建立最多10個應用層級的儀表板，與應用程式的其他使用者共用。 只有系統**管理員**能夠建立、編輯和刪除應用層級的儀表板。 

## <a name="create-dashboard"></a>建立儀表板

下列螢幕擷取畫面顯示從**自訂應用程式**範本建立的應用程式中的儀表板。 您可以使用個人儀表板來取代預設的應用程式儀表板，或者如果您是系統管理員，另一個應用層級儀表板。 若要這麼做，請選取頁面左上角的 [ **+ 新增**]。
 
> [!div class="mx-imgBorder"]
> ![以「自訂應用程式」範本為基礎的應用程式儀表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

選取 [ **+ 新增**] 會開啟儀表板編輯器。 在編輯器中，您可以為儀表板命名，並從程式庫選擇專案。 程式庫包含您可以用來自訂儀表板的圖格和儀表板基本專案。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-create-personal-dashboards/dashboard-library.png)

如果您是應用程式的系統**管理員**，您可以選擇是否要建立個人層級儀表板或應用層級儀表板來切換。 如果您建立個人層級儀表板，只有您才能夠看到它。 如果您建立應用層級儀表板，應用程式的每個使用者都可以看到它。 輸入標題並選取您想要建立的儀表板類型之後，您可以稍後再儲存並新增磚。 或者，如果您現在已經準備好，並已新增裝置範本和裝置實例，可以繼續建立您的第一個磚。 

> [!div class="mx-imgBorder"]
> ![[設定裝置詳細資料] 表單，其中包含溫度的詳細資料](media/howto-create-personal-dashboards/device-details.png)

例如，您可以為裝置的目前溫度新增**遙測**磚。 操作方法：
1. 選取**裝置範本**
1. 針對您想要在儀表板磚上看到的裝置，選取**裝置實例**。 然後您會看到可在磚上使用的裝置屬性清單。
1. 若要在儀表板上建立磚，請按一下 [**溫度**]，並將它拖曳至 [儀表板] 區域。 您也可以按一下 [**溫度**] 旁的核取方塊，然後按一下 [**合併**]。 下列螢幕擷取畫面顯示選取裝置範本和裝置實例，然後在儀表板上建立 [溫度遙測] 磚。
1. 選取左上方的 [**儲存**]，將磚儲存至儀表板。

> [!div class="mx-imgBorder"]
> ![[儀表板] 索引標籤，其中包含 [溫度] 磚的詳細資料](media/howto-create-personal-dashboards/temperature-tile-edit.png)

現在當您查看個人儀表板時，您會看到新的磚，其中包含裝置的**溫度**設定：

> [!div class="mx-imgBorder"]
> ![[儀表板] 索引標籤，其中包含 [溫度] 磚的詳細資料](media/howto-create-personal-dashboards/temperature-tile-complete.png)

您可以流覽程式庫中的其他磚類型，以探索如何進一步自訂您的個人儀表板。

若要深入瞭解如何在 Azure IoT Central 中使用磚，請參閱[將磚新增至儀表板](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理儀表板

您可以有數個個人儀表板，並在它們之間切換，或從其中一個預設應用程式儀表板進行選擇：

> [!div class="mx-imgBorder"]
> ![切換儀表板](media/howto-create-personal-dashboards/switch-dashboards.png)

您可以編輯您的個人儀表板，並刪除不再需要的任何儀表板。 如果您是系統**管理員**，也能夠編輯或刪除應用層級的儀表板。

> [!div class="mx-imgBorder"]
> ![刪除儀表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何建立和管理個人儀表板，您可以[瞭解如何管理您的應用程式喜好](howto-manage-preferences.md)設定
