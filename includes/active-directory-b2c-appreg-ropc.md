---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f941f67105227b3973386b015344d5fa787c3485
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317775"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如 *ROPC_Auth_app*。
1. 保留其他值，然後選取 [註冊]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。
1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [試用全新體驗] (若顯示的話)。
1. 在 [預設用戶端類型] 下方選取 [是]，將應用程式視為公用用戶端。 這是 ROPC 流程的必要設定。
1. 選取 [儲存]。
1. 在左側功能表中選取 [資訊清單]，以開啟資訊清單編輯器。 
1. 將 **oauth2AllowImplicitFlow** 屬性設為 *true*：
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. 選取 [儲存]。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式 (舊版)]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如 *ROPC_Auth_app*。
1. 對於 [原生用戶端]，選取 [是]。
1. 保留其他值，然後選取 [建立]。
1. 記錄 [應用程式識別碼]，以便在稍後的步驟中使用。