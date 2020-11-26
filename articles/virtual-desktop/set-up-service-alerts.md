---
title: 設定 Windows 虛擬桌面的服務警示 - Azure
description: 如何設定 Azure 服務健康狀態，以接收 Windows 虛擬桌面的服務通知。
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66617afee11b02eae0ba5e36d9ff91cbdf21911f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023100"
---
# <a name="tutorial-set-up-service-alerts"></a>教學課程：設定服務警示

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md)。

您可以使用 Azure 服務健康狀態，監視 Windows 虛擬桌面的服務問題和健康狀態諮詢。 Azure 服務健康狀態會以不同類型的警示 (例如，電子郵件或簡訊) 通知您，協助您了解問題所帶來的影響，並在問題解決時通知您； 也會協助您少停機時間，並針對可能影響資源可用性的計劃性維護和變更做好準備。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立及設定服務警示。

若要深入了解 Azure 服務健康狀態，請參閱 [Azure 健康狀態文件](../service-health/index.yml)。

## <a name="create-service-alerts"></a>建立服務警示

本節說明如何設定 Azure 服務健康狀態以及如何設定通知 (您可以在 Azure 入口網站上進行存取)。 您可以設定不同類型的警示，並排定以及時方式通知您。

### <a name="recommended-service-alerts"></a>建議的服務警示

我們建議您針對下列健康狀態事件類型建立服務警示：

- **服務問題：** 接收主要問題的通知，而這類問題會影響使用者與服務的連線能力，或管理 Windows 虛擬桌面租用戶的能力。
- **健康狀態諮詢：** 接收您需要注意的通知。 以下是這類型通知的一些範例：
    - 未安全地將虛擬機器 (VM) 設定為開放連接埠 3389
    - 功能淘汰

### <a name="configure-service-alerts"></a>設定服務警示

若要設定服務警示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [服務健康狀態]  。
3. 遵循[建立服務通知的活動記錄警示](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json)中的指示，設定您的警示和通知。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何設定 Azure 服務健康狀態，以及用於監視 Windows 虛擬桌面的服務問題和健康狀態諮詢。 若要了解如何登入 Windows 虛擬桌面，請繼續進行「連線到 Windows 虛擬桌面」操作說明。

> [!div class="nextstepaction"]
> [連線到 Windows 7 和 Windows 10 上的遠端桌面用戶端](./connect-windows-7-10.md)