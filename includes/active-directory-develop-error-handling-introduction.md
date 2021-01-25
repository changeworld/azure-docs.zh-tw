---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760679"
---
本文概述不同類型的錯誤，以及用於處理常見登入錯誤的建議。

## <a name="msal-error-handling-basics"></a>MSAL 錯誤處理基本概念

Microsoft 驗證程式庫中的例外狀況 (MSAL) 適用于應用程式開發人員進行疑難排解，而不是向終端使用者顯示。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱 [Azure AD 驗證和授權錯誤碼](../articles/active-directory/develop/reference-aadsts-error-codes.md)。

在登入體驗期間，您可能會遇到關於同意、條件式存取 (MFA、裝置管理、位置型限制)、權杖發行和兌換，以及使用者屬性的錯誤。

下一節提供有關應用程式錯誤處理的詳細資料。