---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760667"
---
## <a name="retrying-after-errors-and-exceptions"></a>在發生錯誤和例外狀況後重試

在呼叫 MSAL 時，您應該執行自己的重試原則。 MSAL 會對 Azure AD 服務發出 HTTP 呼叫，有時可能會發生失敗。 例如，網路可能會中斷，或伺服器已超載。  

### <a name="http-429"></a>HTTP 429

服務權杖伺服器 (STS) 因為要求太多而超載時，便會傳回 HTTP 錯誤 429，並提示您要等多久才能在 `Retry-After` 回應欄位中再試一次。