---
title: 將 V2 Azure IoT Central 應用程式遷移至 V3 |Microsoft Docs
description: 以系統管理員身分，瞭解如何將 V2 Azure IoT Central 應用程式遷移至 V3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3f81ae72af48ec934d1c2c2567ebdd212d8e0499
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763350"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>將 V2 IoT Central 應用程式遷移至 V3

*本文適用于系統管理員。*

目前，當您建立新的 IoT Central 應用程式時，它是 V3 應用程式。 如果您先前已建立應用程式，則取決於建立應用程式的時間，它可能是 V2。 本文說明如何將 V2 遷移至 V3 應用程式，以確定您使用的是最新的 IoT Central 功能。

若要瞭解如何識別 IoT Central 應用程式的版本，請參閱 [應用程式的相關資訊](howto-get-app-info.md)。

將應用程式從 V2 遷移至 V3 的步驟如下：

1. 從 V2 應用程式建立新的 V3 應用程式。
1. 設定 V3 應用程式。
1. 刪除 V2 應用程式。

## <a name="create-a-new-v3-application"></a>建立新的 V3 應用程式

使用 [遷移嚮導] 來建立新的 V3 應用程式。

IoT Central 不支援遷移至現有的 V3 應用程式。 若要自動移動現有的裝置，請使用 [遷移嚮導] 來建立 V3 應用程式。

[遷移嚮導]：

- 建立新的 V3 應用程式。
- 檢查您的裝置範本是否與 V3 相容。
- 將您所有的裝置範本複製到新的 V3 應用程式。
- 將所有使用者和角色指派複製到新的 V3 應用程式。

> [!NOTE]
> 為確保裝置與 V3 相容，裝置範本中的基本型別會變成物件屬性。 您不需要對裝置程式碼進行任何變更。

您必須是系統管理員，才能將應用程式遷移到 V3。

1. 在 [ **管理** ] 功能表上，選取 [ **遷移至 V3 應用程式**]：

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="顯示應用程式遷移嚮導的螢幕擷取畫面":::

1. 輸入新的 **應用程式名稱** ，並選擇性地變更自動產生的  **URL**。 URL 不能與您目前 V2 應用程式的 URL 相同。 不過，您稍後可以在刪除 V2 應用程式之後變更 URL。

1. 選取 [ **建立新的 V3 應用程式**]。

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="顯示應用程式遷移嚮導中之選項的螢幕擷取畫面":::

    視裝置範本的數量和複雜度而定，此程式可能需要幾分鐘的時間才能完成。

    > [!Warning]
    > 如果有任何裝置範本的欄位以數位開頭，或包含下列任何字元 (、、、、、、、、、、、) ，您的 V3 應用程式建立將會失敗 `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` 。 V3 應用程式使用的 DTDL 裝置範本架構不允許使用這些字元。 在遷移至 V3 之前，請先更新您的裝置範本以解決此問題。

1. 當您的新 V3 應用程式就緒時，請選取 [ **開啟新的應用程式** ] 以開啟它。

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="螢幕擷取畫面，顯示應用程式遷移之後的應用程式遷移嚮導":::

## <a name="configure-the-v3-application"></a>設定 V3 應用程式

建立新的 V3 應用程式之後，請先進行任何設定變更，再將裝置從 V2 應用程式移至 V3 應用程式。

> [!TIP]
> 請花點時間 [熟悉 V3](overview-iot-central-tour.md#navigate-your-application) ，因為它與前一版有一些差異。

以下是一些建議考慮的設定步驟：

- [設定儀表板](howto-add-tiles-to-your-dashboard.md)
- [設定資料匯出](howto-export-data.md)
- [設定規則和動作](quick-configure-rules.md)
- [自訂應用程式 UI](howto-customize-ui.md)

當您的 V3 應用程式設定為符合您的需求時，您就可以開始將您的裝置從 V2 應用程式移至 V3 應用程式。

## <a name="move-your-devices-to-the-v3-application"></a>將您的裝置移至 V3 應用程式

完成此步驟後，您的所有裝置都只會與新的 V3 應用程式通訊。

> [!IMPORTANT]
> 將裝置移至 V3 應用程式之前，請先刪除您在 V3 應用程式中建立的任何裝置。

此步驟會將您所有現有的裝置移至新的 V3 應用程式。 不會複製您的裝置資料。

選取 [ **移動所有裝置** ] 以開始移動您的裝置。 此步驟可能需要幾分鐘的時間才能完成。

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="螢幕擷取畫面，顯示 [移動裝置] 選項的應用程式遷移嚮導":::

移動完成之後，請重新開機您的所有裝置，以確保它們連接到新的 V3 應用程式。

> [!WARNING]
> 請勿刪除您的 V3 應用程式，因為您的 V2 應用程式現在無法運作。

## <a name="delete-your-old-v2-application"></a>刪除舊的 V2 應用程式

在您驗證新 V3 應用程式中的所有專案都如預期般運作之後，請刪除舊的 V2 應用程式。 此步驟可確保您不會因為不再使用的應用程式而向您收費。

> [!Note]
> 若要刪除應用程式，您必須有權刪除您在建立應用程式時所選擇的 Azure 訂用帳戶中的資源。 若要深入了解，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](../../active-directory/role-based-access-control-configure.md)。

1. 在您的 V2 應用程式中，選取功能表中的 [系統 **管理** ] 索引標籤
2. 選取 [ **刪除** ] 以永久刪除您的 IoT Central 應用程式。 此選項會永久刪除與該應用程式相關聯的所有資料。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何遷移您的應用程式，建議的下一個步驟是複習 [AZURE IOT CENTRAL UI](overview-iot-central-tour.md) ，以查看 V3 中有哪些變更。
