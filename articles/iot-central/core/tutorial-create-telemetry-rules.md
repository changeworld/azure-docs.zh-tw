---
title: 教學課程 - 在 Azure IoT Central 應用程式中建立和管理規則
description: 本教學課程說明 Azure IoT Central 規則如何讓您近乎即時地監視裝置，以及在觸發規則時自動叫用動作，例如傳送電子郵件。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6d49e3585460c95ca931f497a63cbc281aed1db1
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990967"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>教學課程：在 Azure IoT Central 應用程式中建立規則和設定通知

*本文適用於操作員、建置員及系統管理員。*

您可以使用 Azure IoT Central 來遠端監視連線的裝置。 Azure IoT Central 規則可讓您近乎即時地監視裝置，以及自動叫用動作，例如傳送電子郵件。 本文將說明如何建立規則來監視裝置所傳送的遙測。

裝置可以使用遙測資料，從裝置傳送數值資料。 當選取的遙測資料超出指定閾值時，便會觸發規則。

在本教學課程中，您會建立在模擬感應器裝置的溫度超過 70&deg; F 時傳送電子郵件的規則。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立規則
> * 新增電子郵件動作

## <a name="prerequisites"></a>Prerequisites

開始之前，請先完成 [建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)和 [將模擬裝置新增至 IoT Central 應用程式](./quick-create-simulated-device.md)快速入門，以建立要使用的 **控制器感應器** 裝置範本。

## <a name="create-a-rule"></a>建立規則

若要建立遙測規則，裝置範本必須至少包含一個遙測值。 本教學課程會使用模擬的 **感應器控制器** 裝置，傳送溫度和濕度遙測資料。 在[將模擬裝置新增至 IoT Central 應用程式](./quick-create-simulated-device.md)快速入門中，會新增此裝置範本，並建立模擬裝置。 此規則會監視裝置所報告的遙測，並在裝置溫度超過 70 度時傳送電子郵件。

1. 在左側窗格中，選取 [規則]  。

1. 如果尚未建立任何規則，則會看到下列畫面：

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="顯示空白規則清單的螢幕擷取畫面":::

1. 選取 [+ 新增] 以新增規則。

1. 輸入「溫度監視器」  作為用以識別規則的的名稱，並按下 Enter。

1. 選取 [感應器控制器] 裝置範本。 此規則預設會自動套用到與裝置範本建立關聯的所有裝置。 若要篩選裝置的子集，請選取 [+ 篩選]  並利用裝置屬性來識別裝置。 若要停用規則，請切換 [啟用/停用] 按鈕：

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="顯示已在規則定義中選取裝置範本的螢幕擷取畫面":::

### <a name="configure-the-rule-conditions"></a>設定規則條件

由條件來定義規則所要監控的準則。 在本教學課程中，要設定溫度超過 70&deg; F 時所會引發的規則。

1. 在 [遙測]  下拉式清單中選取 [溫度]  。

1. 接下來，在 [運算子]  選擇 [大於]  ，並在 [值]  輸入「70」  。

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="顯示規則之溫度條件的螢幕擷取畫面":::

1. 或者也可以設定 **時間彙總**。 選取時間彙總時，也必須從 [彙總] 下拉式功能表選取彙總類型，例如平均或總和。

    * 如果沒有彙總，規則就會在每個遙測資料點符合條件時觸發。 例如，如果將規則設定為在溫度超過 70 時觸發，則此規則幾乎會在裝置溫度超過此值時立即觸發。
    * 使用彙總時，如果時間範圍內遙測資料點的彙總值符合條件，此規則就會觸發。 例如，如果規則是設定為在當溫度高於 70 觸發，而時間彙總設為 10 分鐘，該規則就會在裝置回報平均溫度高於 70 時觸發 (以 10 分鐘間隔計算)。

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="顯示已填入彙總條件的螢幕擷取畫面":::

您可以選取 [+ 條件]  ，將多個條件新增至規則。 若指定多個條件，就必須符合所有條件才會觸發規則。 每個條件都是由隱含 `AND` 子句聯結。 如果您使用的是具有多項條件的時間彙總，則必須彙總所有的遙測值。

### <a name="configure-actions"></a>設定動作

定義條件之後，您可以設定規則引發時所要採取的動作。 當規則中指定的所有條件都評估為 True 時，即會叫用動作。

1. 在 [動作]  區段中，選取 [+ 電子郵件]  。

1. 輸入「溫度警告」  當做動作的顯示名稱，並且在 [收件人]  欄位中輸入您的電子郵件地址，然後輸入「請檢查裝置！」  作為在電子郵件本文中顯示的附註。

    > [!NOTE]
    > 只有已新增至應用程式且至少已登入一次的使用者才會收到電子郵件。 深入了解 Azure IoT Central 中的[使用者管理](howto-administer.md)。

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="顯示規則之電子郵件動作的螢幕擷取畫面":::

1. 若要儲存動作，請選擇 [完成]  。 您可以在一條規則中新增多個動作。

1. 若要儲存規則，請選擇 [儲存]  。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的遙測。 符合規則中指定的條件時，規則就會觸發所設定的電子郵件動作。

之後在引發規則時，就會收到如下的電子郵件訊息：

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="顯示通知電子郵件的螢幕擷取畫面":::

## <a name="delete-a-rule"></a>刪除規則

如果不再需要某個規則，請開啟規則並選擇 [刪除]  來加以刪除。

## <a name="enable-or-disable-a-rule"></a>啟用或停用規則

選擇您要啟用或停用的規則。 切換規則中的 [啟用/停用]  按鈕，即可啟用或停用規則範圍內所有裝置的該項規則。

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>啟用或停用特定裝置的規則

選擇您要自訂的規則。 使用 [目標裝置]  區段中的一或多個篩選條件，將規則的範圍縮小至您要監視的裝置。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 建立以遙測為基礎的規則
* 新增動作

現在，您已定義以臨界值為基礎的規則，建議您接下來學習：

> [!div class="nextstepaction"]
> [設定連續資料匯出](./howto-export-data.md)。
