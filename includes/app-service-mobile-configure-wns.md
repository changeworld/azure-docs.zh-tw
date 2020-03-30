---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174478"
---
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [瀏覽全部]**** > [應用程式服務]****。 然後選取您的 Mobile Apps 後端。 在 [設定]**** 下，選取 [App Service 推播]****。 然後選取您的通知中樞名稱。
2. 移至 **Windows (WNS)**。 然後輸入您從線上服務網站取得的 [安全性金鑰]**** \(用戶端秘密) 和 [封裝 SID]****。 接下來，選取 [儲存]****。

    ![在入口網站中設定 WNS 金鑰](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

您的後端現在已設定成使用 WNS 傳送來傳送推播通知。
