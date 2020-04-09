---
title: 創建 Azure IoT 中心個人儀錶板 |微軟文件
description: 作為使用者,瞭解如何創建和管理個人儀錶板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985485"
---
# <a name="create-and-manage-multiple-dashboards"></a>建立與管理多個儀表板

儀錶板**是**首次導航到應用程式時載入的頁面。 應用程式中的**生成器**為所有使用者定義預設應用程式儀表板。 您還可以另外創建自己的個人化應用程式儀錶板。 可以有多個儀錶板,這些儀錶板顯示不同的數據並在它們之間切換。

如果您是應用程式的**管理員**,還可以創建多達 10 個應用程式級儀錶板,以便與應用程式的其他使用者共用。 只有**管理員**才能建立、編輯和刪除應用程式級別的儀錶板。 

## <a name="create-dashboard"></a>建立儀表板

以下螢幕截圖顯示了從**自定義應用程式**範本創建的應用程式中的儀錶板。 您可以將預設應用程式儀錶板替換為個人儀表板,或者如果您是管理員,則可以將另一個應用程式級別儀錶板替換。 為此,請選擇頁面左上角的 **「新建**」。。
 
> [!div class="mx-imgBorder"]
> ![以「自訂應用程式」樣本的應用程式儀表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

選擇 **= 新建**將開啟儀錶板編輯器。 在編輯器中,可以為儀錶板指定名稱並從庫中選擇專案。 庫包含可用於自定義儀錶板的磁貼和儀錶板基元。

> [!div class="mx-imgBorder"]
> ![儀表板程式庫](media/howto-create-personal-dashboards/dashboard-library.png)

如果您是應用程式的**管理員**,如果要創建個人級別儀表板或應用程式級別儀錶板,將為您提供切換選項。 如果創建個人級別儀錶板,則只有您才能看到它。 如果創建應用程式級儀錶板,應用程式的每個用戶將能夠看到它。 輸入標題並選擇要創建的儀表板類型後,可以稍後保存和添加磁貼。 或者,如果您現在已準備就緒,並且添加了設備範本和設備實例,則可以繼續創建第一個磁貼。 

> [!div class="mx-imgBorder"]
> ![設定裝置詳細資訊「表單」,其中提供了溫度的詳細資訊](media/howto-create-personal-dashboards/device-details.png)

例如,可以為設備的當前溫度添加**遙測**磁貼。 若要這樣做：
1. 選擇**裝置樣本**
1. 為在儀表板磁貼上檢視的裝置選擇**裝置實例**。 然後,您將看到可在磁貼上使用的設備屬性的清單。
1. 要在儀錶板上創建磁貼,請單擊「**溫度」** 並將其拖動到儀錶板區域。 您還可以按下 **「溫度**」旁邊的複選框,然後單擊「**合併**」 。 以下螢幕截圖顯示選擇設備範本和設備實例,然後在儀錶板上創建溫度遙測磁貼。
1. 選擇 **「在**左上角保存」 以將磁貼保存到儀表板。

> [!div class="mx-imgBorder"]
> !["儀錶板"選項卡,包含"溫度"磁貼的詳細資訊](media/howto-create-personal-dashboards/temperature-tile-edit.png)

現在,當您查看個人儀表板時,您將看到帶有裝置 **「溫度」** 設定的新磁貼:

> [!div class="mx-imgBorder"]
> !["儀錶板"選項卡,包含"溫度"磁貼的詳細資訊](media/howto-create-personal-dashboards/temperature-tile-complete.png)

您可以流覽庫中的其他磁貼類型,瞭解如何進一步自定義個人儀錶板。

要瞭解有關如何在 Azure IoT 中心中使用磁貼的更多內容,請參閱[向儀表板添加磁貼](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理儀表板

您可以有多個個人儀表板並在它們之間切換,也可以從預設應用程式儀表板中選擇:

> [!div class="mx-imgBorder"]
> ![切換儀表板](media/howto-create-personal-dashboards/switch-dashboards.png)

您可以編輯個人儀表板並刪除不再需要的任何儀錶板。 如果您是**管理員**,您還可以編輯或刪除應用程式級別的儀錶板。

> [!div class="mx-imgBorder"]
> ![移除儀表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何建立和管理個人儀表板,您可以[瞭解如何管理應用程式首選項](howto-manage-preferences.md)
