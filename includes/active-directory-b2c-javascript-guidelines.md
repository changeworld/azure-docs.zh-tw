---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185825"
---
## <a name="guidelines-for-using-javascript"></a>使用 JavaScript 的指導方針

在您使用 JavaScript 來自訂應用程式的介面時，請遵循這些指導方針：

- 不要將 Click 事件繫結到 `<a>` HTML 元素上。
- 不要採用 Azure AD B2C 程式碼或註解上的相依性。
- 不要變更 Azure AD B2C HTML 元素的順序或階層。 使用 Azure AD B2C 原則來控制 UI 元素的順序。
- 您可以呼叫任何 RESTful 服務，但有下列考量：
    - 您可能需要將 RESTful 服務 CORS 設定為允許用戶端 HTTP 呼叫。
    - 確定您的 RESTful 服務是安全的，並僅使用 HTTPS 通訊協定。
    - 不要直接使用 JavaScript 來呼叫 Azure AD B2C 端點。
- 您可以內嵌 JavaScript 或連結至外部的 JavaScript 檔案。 使用外部 JavaScript 檔案時，請務必使用絕對 URL，而非相對 URL。
- JavaScript 架構：
    - Azure AD B2C 會使用特定版本的 jQuery。 不要包含其他版本的 jQuery。 在相同頁面上使用多個版本會導致問題。
    - 不支援使用 RequireJS。
    - Azure AD B2C 不支援大部分的 JavaScript 架構。
- 可以呼叫 `window.SETTINGS`、`window.CONTENT` 物件來讀取 Azure AD B2C 設定，例如目前的 UI 語言。 請不要變更這些物件的值。
- 若要自訂 Azure AD B2C 錯誤訊息，請在原則中使用當地語系化。
- 如果可以透過原則來達成某個事項，便建議使用原則來達成它。
