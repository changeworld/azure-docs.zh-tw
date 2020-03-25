---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183362"
---
Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式]  ，然後選取 [新增]  。
1. 輸入應用程式的名稱。 例如，*webapi1*。
1. 針對 [Web 應用程式/Web API]  ，選取 [是]  。
1. 針對 [允許隱含流程]  ，選取 [是]  。
1. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:5000` 接聽。
1. 針對 [應用程式識別碼 URI]  ，將 API 端點識別碼新增至顯示的 URI。 在本教學課程中請輸入 `api`，讓完整的 URI 類似於 `https://contosob2c.onmicrosoft.com/api`。
1. 選取 [建立]  。
1. 記錄 [應用程式識別碼]  ，以便在稍後的步驟中使用。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式註冊 (預覽)]  ，然後選取 [新增註冊]  。
1. 輸入應用程式的 [名稱]  。 例如，*webapi1*。
1. 在 [重新導向 URI]  底下，選取 [Web]  ，然後輸入 Azure AD B2C 應傳回您應用程式所要求之任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `http://localhost:5000` 接聽。
1. 選取 [註冊]  。
1. 記錄 [應用程式 (用戶端) 識別碼]  ，以便在稍後的步驟中使用。

接下來，啟用隱含授與流程：

1. 在 [管理]  底下，選取 [驗證]  。
1. 選取 [試用全新體驗]  (若顯示的話)。
1. 在 [隱含授與]  底下，同時選取 [存取權杖]  和 [識別碼權杖]  核取方塊。
1. 選取 [儲存]  。