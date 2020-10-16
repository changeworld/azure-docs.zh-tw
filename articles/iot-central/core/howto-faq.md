---
title: Azure IoT Central 的常見問題 |Microsoft Docs
description: Azure IoT Central 常見問題) 和解答的常見問題 (
author: dominicbetts
ms.author: dobett
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cbe0dbe55f903c25556698a5d510bf9e67b408be
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126715"
---
# <a name="frequently-asked-questions-for-iot-central"></a>IoT Central 的常見問題

**如果裝置未連線到我的 IoT Central 應用程式，如何? 檢查是否有認證問題？**

[疑難排解裝置的資料未顯示 Azure IoT Central](troubleshoot-connection.md)包含診斷裝置連線問題的步驟。

**如何? 向客戶支援提出票證？**

如果您需要協助，您可以提出 [Azure 支援的票證](https://portal.azure.com/#create/Microsoft.Support)。

如需詳細資訊（包括其他支援選項），請參閱 [Azure IoT 支援和協助選項](../../iot-fundamentals/iot-support-help.md)。

**如何? 將裝置解除封鎖？**

當裝置被封鎖時，就無法將資料傳送到您的 IoT Central 應用程式。 封鎖的裝置在應用程式的 [**裝置**] 頁面上具有 [已**封鎖**] 狀態。 操作員必須解除封鎖裝置，才能繼續傳送資料：

:::image type="content" source="media/howto-faq/blocked.png" alt-text="顯示已封鎖裝置的螢幕擷取畫面":::

當操作員解除封鎖裝置時，狀態會回到先前的值，即 **已註冊或已** 布 **建**。

**如何? 核准裝置？**

如果裝置狀態為 [**裝置**] 頁面上的 [**等待核准**]，表示已停用 [**自動核准**] 選項：

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="顯示已封鎖裝置的螢幕擷取畫面":::

操作員必須先明確核准裝置，才能開始傳送資料。 未在 [ **裝置** ] 頁面上手動註冊的裝置，但使用有效的認證連線時，會有裝置狀態 **等候核准**。 操作員可以使用 [**核准**] 按鈕，從 [**裝置**] 頁面核准這些裝置：

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="顯示已封鎖裝置的螢幕擷取畫面":::

**如何? 將裝置與裝置範本產生關聯？**

如果裝置狀態為 [無 **關聯**]，表示連接到 IoT Central 的裝置沒有相關聯的裝置範本。 這種情況通常會發生在下列情況：

- 您可以使用 [**裝置**] 頁面上的 [匯**入**] 來新增一組裝置，而不需要指定裝置範本。
- 裝置已在 [ **裝置** ] 頁面上手動註冊，但未指定裝置範本。 裝置接著會使用有效的認證來連線。  

操作員可以使用 [**遷移**] 按鈕，從 [**裝置**] 頁面將裝置與裝置範本建立關聯。 若要深入瞭解，請參閱 [在 Azure IoT Central 應用程式中管理裝置 > 將裝置遷移至範本](howto-manage-devices.md)。

**哪裡可以深入瞭解 IoT 中樞？**

Azure IoT 中心使用 Azure IoT 中樞作為裝置連線的雲端閘道。 IoT 中樞可讓您︰

- 在雲端大規模擷取資料。
- 裝置管理。
- 安全的裝置連線能力。

若要深入了解 IoT 中樞，請參閱 [Azure IoT 中樞](../../iot-hub/index.yml)。

**我可以在哪裡深入瞭解 (DPS) 的裝置布建服務？**

Azure IoT Central 使用 DPS 讓裝置連接到您的應用程式。 若要深入瞭解 DPS 在將裝置連接到 IoT Central 時所扮演的角色，請參閱 [連接到 Azure IoT Central](concepts-get-connected.md)。 若要深入瞭解 DPS，請參閱 [使用 Azure IoT 中樞裝置](../../iot-dps/about-iot-dps.md)布建服務來布建裝置。