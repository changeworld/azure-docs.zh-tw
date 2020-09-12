---
title: 自訂 Azure IoT Central UI |Microsoft Docs
description: 如何自訂 Azure IoT central 應用程式的主題和協助連結
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019830"
---
# <a name="customize-the-azure-iot-central-ui"></a>自訂 Azure IoT Central UI

本文說明如何以系統管理員的身分，藉由套用自訂主題和修改說明連結來指向您自己的自訂說明資源，來自訂應用程式的 UI。 

下列螢幕擷取畫面顯示使用標準主題的頁面：

![標準 IoT Central 主題](./media/howto-customize-ui/standard-ui.png)

下列螢幕擷取畫面顯示的頁面使用自訂的螢幕擷取畫面，其中已醒目提示自訂 UI 元素：

![自訂 IoT Central 主題](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>建立主題

若要建立自訂主題，請流覽至 [**管理**] 區段中的 [**自訂您的應用程式**] 頁面：

![IoT Central 主題](./media/howto-customize-ui/themes.png)

在此頁面上，您可以自訂應用程式的下列層面：

### <a name="application-logo"></a>應用程式標誌

PNG 影像，不超過 1 MB，而且具有透明背景。 此標誌會顯示在 IoT Central 應用程式標題列的左邊。

如果您的標誌影像包含應用程式的名稱，您可以隱藏應用程式名稱文字。 如需詳細資訊，請參閱 [管理您的應用程式](howto-administer.md#change-application-name-and-url)。

### <a name="browser-icon-favicon"></a>瀏覽器圖示 (favicon) 

PNG 影像，不超過 32 x 32 圖元，且具有透明背景。 網頁瀏覽器可在網址列、歷程記錄、書簽和瀏覽器索引標籤中使用此映射。

### <a name="browser-colors"></a>瀏覽器色彩

您可以變更頁面標題的色彩，以及用於 accenting 按鈕和其他醒目顯示的色彩。 以格式使用六個字元的十六進位色彩值 `##ff6347` 。 如需 **十六進位值** 色彩標記法的詳細資訊，請參閱 [HTML 色彩](https://www.w3schools.com/html/html_colors.asp)。

> [!NOTE]
> 您可以隨時還原為 [ **自訂您的應用程式** ] 頁面上的預設選項。

### <a name="changes-for-operators"></a>操作員的變更

如果系統管理員建立自訂主題，則應用程式的操作員和其他使用者將無法再于 [ **設定**] 中選擇主題。

## <a name="replace-help-links"></a>取代說明連結

若要提供自訂的說明資訊給操作員和其他使用者，您可以 **修改應用程式** [說明] 功能表上的連結。

若要修改說明連結，請流覽至 [**管理**] 區段中的 [**自訂**] 說明頁面：

![自訂 IoT Central 說明連結](./media/howto-customize-ui/help-links.png)

您也可以將新專案新增至 [說明] 功能表，並移除預設專案：

![自訂的 IoT Central 說明](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 您一律可以還原回 [ **自訂** 說明] 頁面上的預設 [說明] 連結。

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解如何在 IoT Central 應用程式中自訂 UI，以下是一些建議的後續步驟：

- [管理您的應用程式](./howto-administer.md)
- [將圖格新增至您的儀表板](howto-add-tiles-to-your-dashboard.md)