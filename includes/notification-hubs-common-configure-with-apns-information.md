---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095184"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>使用 APNS 資訊設定您的通知中樞

在 [通知服務] 下，選取 [Apple]，然後遵循先前在＜[建立通知中樞的憑證](#creating-a-certificate-for-notification-hubs)＞一節中選擇的方法進行適當的步驟。  

> [!NOTE]
> 只有在您想傳送推播通知給從市集購買應用程式的使用者時，才可使用 [生產] 的 [應用程式模式]。

### <a name="option-1-using-a-p12-push-certificate"></a>選項 1：使用 .p12 推送憑證

1. 選取 [憑證] 。

1. 選取 [檔案] 圖示。

1. 選取您先前匯出的 .p12 檔案，然後選取 [開啟]。

1. 若有需要，請指定正確的密碼。

1. 選取 [沙箱] 模式。

1. 選取 [儲存]。

### <a name="option-2-using-token-based-authentication"></a>選項 2：使用權杖型驗證

1. 選取**權杖**。
1. 輸入您稍早取得的下列值：

    - **金鑰識別碼**
    - **套件組合識別碼**
    - **小組識別碼**
    - **權杖**

1. 選擇 [沙箱]。
1. 選取 [儲存]。
