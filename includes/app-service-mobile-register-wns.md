---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174476"
---
1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下 Windows 市集應用程式專案。 然後選擇 **"使用應用商店的應用商店** > **關聯應用**"。

    ![建立應用程式與 Windows 市集的關聯](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. 在精靈中，選取 [下一步]****。 然後使用您的 Microsoft 帳戶登入。 在 [保留新的應用程式名稱]**** 中，輸入您應用程式的名稱，然後選取 [保留]****。
3. 成功建立應用程式註冊之後，選取新的應用程式名稱。 選取 [下一步]****，然後選取 [關聯]****。 此程序會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。
4. 使用您之前為 Windows 市集應用程式所建立的相同註冊，針對 Windows Phone 市集應用程式專案重複步驟 1 和 3。  
5. 移至 [Windows 開發人員中心](https://dev.windows.com/en-us/overview)，然後使用您的 Microsoft 帳戶登入。 在 [我的應用程式]**** 中，選取新的應用程式註冊。 然後展開**服務** > **推送通知**。
6. 在 [推播通知]**** 頁面上，在 [Windows 推播通知服務 (WNS) 和 Microsoft Azure Mobile Apps]**** 下，選取 [線上服務網站]****。  請記下**套件 SID** 的值和**應用程式密碼**中的*目前*值。 

    ![開發人員中心的應用程式設定](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > 應用程式密碼與封裝 SID 是重要的安全性認證。 請勿與任何人共用這些值，或與您的應用程式一起散發密碼。
   >
   >
