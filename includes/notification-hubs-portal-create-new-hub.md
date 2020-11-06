---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 2ec602f056b339a1b1dcb78d6b8d7583aeaf0434
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376003"
---
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左功能表上的 [所有服務]，然後選取 [行動] 區段中的 [通知中樞]。 選取服務名稱旁邊的星號圖示，將服務加到左功能表上的 [我的最愛]  區段。 將 [通知中樞]  新增至 [我的最愛]  之後，在左功能表上予以選取。

      ![Azure 入口網站 - 選取通知中樞](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. 在 [通知中樞]  頁面上，選取工具列上的 [新增]  。

      ![通知中樞 - 新增工具列按鈕](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. 在 [通知中樞]  頁面上，執行下列步驟：

    1. 在 [通知中樞]  中輸入名稱。  

    1. 在 [建立新的命名空間]  中輸入名稱。 命名空間包含一或多個中樞。

    1. 從 [位置]  下拉式清單方塊選取值。 此值會指定您要在其中建立中樞的位置。

    1. 在 [資源群組]  中選取現有的資源群組，或建立一個新群組。

    1. 選取 [建立]  。

        ![Azure 入口網站 - 設定通知中樞屬性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. 選取 [通知]  \(鈴鐺圖示)，然後選取 [前往資源]  。 您也可以重新整理 [通知中樞]  頁面中的清單，然後選取您的中樞。

      ![Azure 入口網站 - 前往資源](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. 從清單中選取 [存取原則]  。 請記下您可使用的兩個連接字串。 您稍後需要用到這些連接字串來處理推播通知。

      >[!IMPORTANT]
      >「請勿」  在應用程式中使用 **DefaultFullSharedAccessSignature** 原則。 這只能在後端使用。
      >

      ![Azure 入口網站 - 通知中樞連接字串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
