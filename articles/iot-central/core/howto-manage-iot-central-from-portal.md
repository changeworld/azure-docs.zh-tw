---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: 本文說明如何從 Azure 入口網站建立及管理您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 8cf8fdfe0c8eb557946baa95d0d8beb2b388baa5
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674441"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure 入口網站](https://portal.azure.com)來管理您的應用程式，而不需在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

若要建立應用程式，請流覽至 [Azure 入口網站](https://ms.portal.azure.com) 並選取 [ **建立資源**]。

在 **[搜尋 Marketplace** ] 列中，輸入 *IoT Central*：

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜尋結果中選取 [ **IoT Central 應用程式** ] 磚：

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選取 [ **建立**：

![管理入口網站：IoT Central 資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 此表單類似于您在 [Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral) 網站上填寫來建立應用程式的表單。 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

![建立 IoT Central 表單](media/howto-manage-iot-central-from-portal/image6a.png)

[位置]  是您要在其中建立應用程式的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 一般而言，您應選擇實際上與您的裝置最接近的位置，以取得最佳效能。 Azure IoT Central 目前適用于 **澳大利亞**、**亞太地區**、**歐洲**、**美國**、英國和 **日本** 地區。  選擇位置後，即無法將應用程式移到不同的位置。

填寫所有欄位之後，請選取 [ **建立**]。

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 使用 *免費* 方案建立的應用程式不需要 azure 訂用帳戶，因此您在 Azure 入口網站的 azure 訂用帳戶中不會列出這些應用程式。 您只能從 IoT Central 入口網站查看及管理免費的應用程式。

若要開始使用，請選取入口網站中的 **所有資源** 。 選取 [ **顯示隱藏的類型** ]，然後開始在 [ **依名稱篩選** ] 中輸入您的應用程式名稱來尋找它。 然後選取您想要管理的 IoT Central 應用程式。

若要流覽至應用程式，請選取 **IoT Central 的應用程式 URL**：

![顯示 [總覽] 頁面的螢幕擷取畫面，其中已醒目提示 [IoT Central 應用程式 URL]。](media/howto-manage-iot-central-from-portal/image3.png)

若要將應用程式移至不同的資源群組，請選取資源群組旁的 [ **變更** ]。 在 [ **移動資源** ] 頁面上，選擇您想要將此應用程式移至的資源群組：

![顯示 [總覽] 頁面的螢幕擷取畫面，其中已醒目提示 [資源群組 (變更) ]。](media/howto-manage-iot-central-from-portal/image4a.png)

若要將應用程式移至不同的訂用帳戶，請選取訂用帳戶旁邊的  **變更** 。 在 [ **移動資源** ] 頁面上，選擇您想要將此應用程式移至的訂用帳戶：

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)