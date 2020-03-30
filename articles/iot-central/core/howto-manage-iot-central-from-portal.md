---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: 本文介紹如何從 Azure 門戶創建和管理 IoT 中心應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157920"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure 門戶](https://portal.azure.com)來管理應用程式，而不是在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上創建和管理 IoT 中央應用程式。

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

要創建應用程式，請導航到[Azure 門戶](https://ms.portal.azure.com)，然後選擇 **"創建資源**"。

**在"搜索市場"** 欄中，鍵入*IoT 中心*：

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜尋結果中選擇**IoT 中央應用程式**磁貼：

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選擇 **"創建**"

![管理入口網站：IoT Central 資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 此表單類似于在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上填寫的創建應用程式的表單。 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

![創建 IoT 中心表單](media/howto-manage-iot-central-from-portal/image6a.png)

[位置]**** 是您要在其中建立應用程式的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 一般而言，您應選擇實際上與您的裝置最接近的位置，以取得最佳效能。 Azure IoT 中心目前**在澳大利亞、****亞太地區**、**歐洲**、**美國**、**英國****和日本**地區提供。 選擇位置後，即無法將應用程式移到不同的位置。

填寫所有欄位後，選擇 **"創建**"。

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 您無法在 Azure 門戶中查看在免費定價計畫上創建的應用程式，因為它們與訂閱無關。

要開始，請選擇門戶中的所有**資源**。 選擇 **"顯示隱藏類型**"，然後**開始在"篩選器"中按名稱**鍵入應用程式的名稱以查找它。 然後選擇要管理的 IoT 中央應用程式。

要導航到應用程式，請選擇**IoT 中央應用程式 URL**：

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image3.png)

要將應用程式移動到其他資源組，請選擇資源組旁邊的**更改**。 在 **"移動資源"** 頁上，選擇要將此應用程式移動到以下資源組：

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image4a.png)

要將應用程式移動到其他訂閱，請選擇訂閱旁邊的**更改**。 在 **"移動資源"** 頁上，選擇要將此應用程式移動到：

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)