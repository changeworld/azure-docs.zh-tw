---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174484"
---
1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [瀏覽全部]**** > [應用程式服務]****，再按一下 [Mobile Apps 後端]。 在 [設定]**** 中，按一下 [App Service 推播]****，然後按一下通知中樞名稱。
2. 移至 **Google (GCM)**，輸入在先前程序中透過 Firebase 取得的**伺服器金鑰**值，然後按一下 [儲存]****。

    ![在入口網站中設定 API 金鑰](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Mobile Apps 後端現已設定成使用 Firebase 雲端通訊。 這個設定可讓您透過通知中樞，將推播通知傳送到在 Android 裝置上執行的應用程式。
