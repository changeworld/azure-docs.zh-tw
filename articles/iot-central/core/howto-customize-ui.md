---
title: 自訂 Azure IoT 中央 UI |微軟文檔
description: 如何為 Azure IoT 中央應用程式自訂主題和説明連結
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157969"
---
# <a name="customize-the-azure-iot-central-ui"></a>自訂 Azure IoT 中央 UI

本文介紹作為管理員，如何通過應用自訂主題和修改説明連結以指向您自己的自訂説明資源來自訂應用程式的 UI。 



以下螢幕截圖顯示了使用標準主題的頁面：

![標準 IoT 中心主題](./media/howto-customize-ui/standard-ui.png)

以下螢幕截圖顯示了使用自訂螢幕截圖的頁面，其中突出顯示了自訂 UI 元素：

![自訂 IoT 中心主題](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>創建主題

要創建自訂主題，請導航到 **"管理**"部分中的 **"自訂應用程式**"頁：

![IoT 中心主題](./media/howto-customize-ui/themes.png)

在此頁上，您可以自訂應用程式的以下方面：

### <a name="application-logo"></a>應用程式徽標

具有透明背景的 PNG 圖像，不超過 1 MB。 此徽標顯示在 IoT 中央應用程式標題列的左側。

如果徽標圖像包含應用程式的名稱，則可以隱藏應用程式名稱文本。 有關詳細資訊，請參閱[管理應用程式](howto-administer.md#change-application-name-and-url)。

### <a name="browser-icon-favicon"></a>瀏覽器圖示（圖示）

具有透明背景的 PNG 圖像，不超過 32 x 32 圖元。 Web 瀏覽器可以在網址列、歷史記錄、書簽和瀏覽器選項卡中使用此圖像。

### <a name="browser-colors"></a>瀏覽器顏色

您可以更改頁面標題的顏色以及用於重音按鈕和其他高光的顏色。 在格式`##ff6347`中使用六個字元的十六進位顏色值。 有關**HEX 值**顏色標記法的詳細資訊，請參閱[HTML 顏色](https://www.w3schools.com/html/html_colors.asp)。

> [!NOTE]
> 您可以隨時恢復到 **"自訂應用程式**"頁上的預設選項。

### <a name="changes-for-operators"></a>運算子的更改

如果管理員創建自訂主題，則應用程式的操作員和其他使用者無法再在 **"設置"** 中選擇主題。

## <a name="replace-help-links"></a>替換説明連結

要向操作員和其他使用者提供自訂説明資訊，可以修改應用程式**説明**功能表上的連結。

要修改説明連結，請導航到 **"管理**"部分中的 **"自訂説明**"頁：

![自訂 IoT 中心説明連結](./media/howto-customize-ui/help-links.png)

您還可以向説明功能表添加新條目並刪除預設條目：

![自訂 IoT 中心説明](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 您可以隨時恢復到 **"自訂説明**"頁上的預設説明連結。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何在 IoT Central 應用程式中自訂 UI，下面是一些建議的後續步驟：

- [管理您的應用程式](./howto-administer.md)
- [將圖格新增至您的儀表板](howto-add-tiles-to-your-dashboard.md)