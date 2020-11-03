---
title: 使用 Azure 入口網站建立 Azure 通知中樞 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure 入口網站建立 Azure 通知中樞。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 141812e83653b458a2562bf73b70117c39bf7be0
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426553"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure 通知中樞

Azure 通知中樞提供易於使用且相應放大的推播引擎，可讓您從任何後端 (雲端或內部部署) 傳送通知到任何平台 (iOS、Android、Windows、Kindle、Baidu 等)。 如需該服務的詳細資訊，請參閱[什麼是 Azure 通知中樞？](notification-hubs-push-notification-overview.md)。

在本快速入門中，您會在 Azure 入口網站建立通知中樞。 第一節會提供相關步驟讓您建立通知中樞命名空間以及該命名空間中的中樞。 第二節會提供相關步驟讓您在現有通知中樞命名空間中建立通知中樞。

## <a name="create-a-namespace-and-a-notification-hub"></a>建立命名空間和通知中樞

在本節中，您會建立命名空間以及命名空間中的中樞。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>在現有命名空間中建立通知中樞

在本節中，您會在現有命名空間中建立通知中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]  ，搜尋 **通知中樞** ，選取 [通知中樞命名空間]  旁的 **星號** (`*`)，以將其新增至左側功能表上的 [我的最愛]  區段。 選取 [通知中樞命名空間]  。

      ![Azure 入口網站 - 選取通知中樞命名空間](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. 在 [通知中樞命名空間]  頁面上，從清單中選取您的命名空間。

      ![從清單中選取您的命名空間](./media/create-notification-hub-portal/select-namespace.png)
4. 在 [通知中樞命名空間]  頁面上，選取工具列上的 [新增中樞]  。

      ![通知中樞命名空間 - [新增中樞] 按鈕](./media/create-notification-hub-portal/add-hub-button.png)
5. 在 [新增通知中樞]  頁面上，輸入通知中樞的名稱，然後選取 [確定]  。

      ![[新增通知中樞] 頁面 -> 輸入中樞的名稱](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. 選取頂端的 [通知]  (鈴鐺圖示) 以查看新中樞的部署狀態。 選取右上角的 **X** 來關閉通知視窗。

      ![部署通知](./media/create-notification-hub-portal/deployment-notification.png)
7. 重新整理 [通知中樞命名空間]  網頁，以在清單中查看您的新中樞。

      ![顯示通知中樞命名空間網頁清單中列出新中樞的螢幕擷取畫面。](./media/create-notification-hub-portal/new-hub-in-list.png)
8. 選取您的 **通知中樞** 以查看通知中樞的首頁。

      ![顯示通知中樞歡迎頁面的螢幕擷取畫面。](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立通知中樞。 若要了解如何使用平台通知系統 (PNS) 設定來設定中樞，請參閱[使用 PNS 設定來設定通知中樞](configure-notification-hub-portal-pns-settings.md)。
