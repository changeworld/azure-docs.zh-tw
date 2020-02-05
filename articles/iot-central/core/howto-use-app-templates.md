---
title: 匯出 Azure IoT Central 應用程式 |Microsoft Docs
description: 身為解決方案管理員，我想要匯出應用程式範本，以便能夠重複使用它。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b5c8f7fd1c87ce279a8edd39aacb332b8aef28be
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023441"
---
# <a name="export-your-application"></a>匯出您的應用程式



本文說明如何以解決方案管理員的身分，匯出 IoT Central 應用程式，以重複使用它。

您有兩個選擇：

- 如果您只需要建立應用程式的複本，您可以建立應用程式的複本。
- 如果您打算建立多個複本，您可以從應用程式建立應用程式範本。

## <a name="copy-your-application"></a>複製您的應用程式

您可以建立任何應用程式複本，並減去其中的任何裝置執行個體、裝置資料歷程記錄和使用者資料。 此複本會使用您將支付費用的標準定價方案。 您無法藉由複製應用程式來建立使用免費定價方案的應用程式。

選取 [**複製**]。 在 [] 對話方塊中，輸入新應用程式的詳細資料。 然後選取 [**複製**] 以確認您想要繼續。 若要深入瞭解表單中的欄位，請參閱[建立應用程式](quick-deploy-iot-central.md)快速入門。

![[應用程式設定] 頁面](media/howto-use-app-templates/appcopy2.png)

應用程式複製作業成功之後，您可以使用連結流覽至新的應用程式。

![[應用程式設定] 頁面](media/howto-use-app-templates/appcopy3a.png)

複製應用程式也會複製規則的定義和電子郵件動作。 某些動作（例如 Flow 和 Logic Apps）會透過規則識別碼系結至特定規則。 當規則複製到不同的應用程式時，它會取得自己的規則識別碼。 在此情況下，使用者將必須建立新的動作，然後將新的規則與它建立關聯。 一般來說，檢查規則和動作以確定它們在新應用程式中是最新的，是個不錯的主意。

> [!WARNING]
> 如果儀表板包含顯示特定裝置相關資訊的磚，這些磚會顯示在新的應用程式中**找不到所要求的資源**。 您必須重新設定這些磚，才能在新的應用程式中顯示裝置的相關資訊。

## <a name="create-an-application-template"></a>建立應用程式範本

當您建立 Azure IoT Central 應用程式時，您可以選擇內建範例範本。 您也可以從現有的 IoT Central 應用程式建立您自己的應用程式範本。 當您建立新的應用程式時，您可以使用自己的應用程式範本。

當您建立應用程式範本時，它會包含來自現有應用程式的下列專案：

- 預設的應用程式儀表板，包括儀表板版面配置和您已定義的所有磚。
- 裝置範本，包括度量、設定、屬性、命令和儀表板。
- 規則。 包含所有規則定義。 不過，除了電子郵件動作以外的動作不會包含在內。
- 裝置集，包括其條件和儀表板。

> [!WARNING]
> 如果儀表板包含顯示特定裝置相關資訊的磚，這些磚會顯示在新的應用程式中**找不到所要求的資源**。 您必須重新設定這些磚，才能在新的應用程式中顯示裝置的相關資訊。

當您建立應用程式範本時，它不會包含下列專案：

- 裝置
- 使用者人數
- 作業定義
- 連續資料匯出定義

請手動將這些專案新增至從應用程式範本建立的任何應用程式。

若要從現有的 IoT Central 應用程式建立應用程式範本：

1. 移至應用程式中的 [系統**管理**] 區段。
1. 選取 [**應用程式範本匯出**]。
1. 在 [**應用程式範本匯出**] 頁面上，輸入範本的名稱和描述。
1. 選取 [**匯出**] 按鈕，以建立應用程式範本。 您現在可以複製可**共用的連結**，讓其他人可以從範本建立新的應用程式：

![建立應用程式範本](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>使用應用程式範本

若要使用應用程式範本建立新的 IoT Central 應用程式，您需要先前建立的可**共用連結**。 將可**共用的連結**貼到瀏覽器的網址列。 [**建立應用程式**] 頁面隨即顯示，並選取您的自訂應用程式範本：

![從範本建立應用程式](media/howto-use-app-templates/create-app.png)

選取您的定價方案，並在表單上填寫其他欄位。 然後選取 [**建立**]，從應用程式範本建立新的 IoT Central 應用程式。

### <a name="manage-application-templates"></a>管理應用程式範本

在 [**應用程式範本匯出**] 頁面上，您可以刪除或更新應用程式範本。

如果您刪除應用程式範本，就無法再使用先前產生的可共用連結來建立新的應用程式。

若要更新您的應用程式範本，請在**應用程式範本**的 [匯出] 頁面上變更範本名稱或描述。 然後再次選取 [**匯出**] 按鈕。 此動作會產生新的可**共用連結**，並使任何先前可**共用的連結**URL 失效。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用應用程式範本，建議的下一個步驟是瞭解如何[從 Azure 入口網站管理 IoT Central](howto-manage-iot-central-from-portal.md)
