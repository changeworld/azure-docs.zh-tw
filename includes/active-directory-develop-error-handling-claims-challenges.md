---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760661"
---
## <a name="conditional-access-and-claims-challenges"></a>條件式存取和宣告挑戰

以無訊息模式取得權杖時，若您嘗試存取的 API 需要[條件式存取宣告挑戰](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) (例如 MFA 原則)，則您的應用程式可能會收到錯誤。

用於處理此錯誤的模式是使用 MSAL 以互動方式取得權杖。 這會提示使用者，讓他們有機會滿足所需的條件式存取原則。

在某些情況下，當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 例如，如果條件式存取原則是必須具有受控裝置 (Intune)，則錯誤可能會是 [AADSTS53000：您的裝置必須受到管理才能存取此資源](../articles/active-directory/develop/reference-aadsts-error-codes.md)，或類似的錯誤。 在此情況下，您可以在取得權杖的呼叫中傳入宣告，讓系統提示使用者符合適當的原則。
