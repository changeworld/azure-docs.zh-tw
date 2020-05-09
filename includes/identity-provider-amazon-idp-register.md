---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900340"
---
1. 使用您的 Amazon 帳號憑證登入[Amazon 開發人員主控台](https://developer.amazon.com/dashboard)。
1. 若您尚未執行此動作，請按一下 [註冊] ****、遵循開發人員註冊步驟，並接受原則。
1. 從儀表板中，選取 **[使用 Amazon 登**入]。
1. 選取 [建立新的安全性設定檔]****。
1. 輸入**安全性設定檔名稱**、**安全性設定檔描述**，以及**同意隱私權注意事項 URL**， `https://www.contoso.com/privacy`例如隱私權注意事項 url 是您管理的頁面，可為使用者提供隱私權資訊。 然後按一下 [儲存]  。
1. 在 [**使用 Amazon 設定登**入] 區段中，選取您建立的**安全性設定檔名稱**，按一下 [**管理**] 圖示，然後選取 [ **Web 設定**]。
1. 在 [Web 設定]**** 區段中，複製 [用戶端識別碼]**** 的值。 選取 [顯示密碼]**** 以取得用戶端密碼，然後複製該密碼。 您必須使用這兩個值，將 Amazon 帳戶設為租用戶中的識別提供者。 **用戶端密碼**是重要的安全性認證。
1. 在 [ **Web 設定**] 區段中，選取 [**編輯**]。 在 [**允許的來源**] 和 [允許的傳回**url**] 中，輸入適當的 url （如上所述）。 
1. 按一下 [檔案]  。
