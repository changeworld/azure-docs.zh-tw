---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174482"
---
Azure App Service 的 Mobile Apps 使用 [Azure 通知中樞]來傳送推送，因此您將為行動應用程式設定通知中樞。

1. 在 [Azure 入口網站]中，移至 [應用程式服務]****，然後選取應用程式後端。 在 [設定]**** 底下，選取 [推播]****。
2. 將通知中樞資源新增至應用程式，選取 [連線]****。 您可以建立中樞或連線到現有的中樞。

    ![設定中樞](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

現在您已將通知中樞連接到 Mobile Apps 後端專案。 稍後您要設定此通知中樞，使其連線到平台通知系統 (PNS) 以推播至裝置。

[Azure 門戶]: https://portal.azure.com/
[Azure 通知中心]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
