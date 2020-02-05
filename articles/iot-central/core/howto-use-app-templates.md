---
title: 使用 Azure IoT Central 中的應用程式範本 |Microsoft Docs
description: 如何以操作員的身分在 Azure IoT Central 應用程式中使用裝置集合。
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982455"
---
# <a name="use-application-templates"></a>使用應用程式範本

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文說明如何以解決方案管理員的身分，建立和使用應用程式範本。

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

## <a name="create-an-application-template"></a>建立應用程式範本

若要從現有的 IoT Central 應用程式建立應用程式範本：

1. 移至應用程式中的 [系統**管理**] 區段。
1. 選取 [**應用程式範本匯出**]。
1. 在 [**應用程式範本匯出**] 頁面上，輸入範本的名稱和描述。
1. 選取 [**匯出**] 按鈕，以建立應用程式範本。 您現在可以複製可**共用的連結**，讓其他人可以從範本建立新的應用程式：

![建立應用程式範本](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>使用應用程式範本

若要使用應用程式範本建立新的 IoT Central 應用程式，您需要先前建立的可**共用連結**。 將可**共用的連結**貼到瀏覽器的網址列。 [**建立應用程式**] 頁面隨即顯示，並選取您的自訂應用程式範本：

![從範本建立應用程式](media/howto-use-app-templates/create-app.png)

選取您的定價方案，並在表單上填寫其他欄位。 然後選取 [**建立**]，從應用程式範本建立新的 IoT Central 應用程式。

## <a name="manage-application-templates"></a>管理應用程式範本

在 [**應用程式範本匯出**] 頁面上，您可以刪除或更新應用程式範本。

如果您刪除應用程式範本，就無法再使用先前產生的可共用連結來建立新的應用程式。

若要更新您的應用程式範本，請在**應用程式範本**的 [匯出] 頁面上變更範本名稱或描述。 然後再次選取 [**匯出**] 按鈕。 此動作會產生新的可**共用連結**，並使任何先前可**共用的連結**URL 失效。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用應用程式範本，建議的下一個步驟是瞭解如何[從 Azure 入口網站管理 IoT Central](howto-manage-iot-central-from-portal.md)
