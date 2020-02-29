---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184334"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。
1. 在 [**管理**] 下，選取 **[應用程式註冊（舊版）** ]。
1. 選取 [新增應用程式註冊]。
1. 輸入應用程式的名稱。 例如， *managementapp1*。
1. 針對 [**應用程式類型**]，選取 [ **Web 應用程式/API**]。
1. 在 [登入**url**] 中輸入任何有效的 url。 例如： `https://localhost` 。 端點不需要可連線，但必須是有效的 URL。
1. 選取 [建立]。
1. 記錄 [**已註冊的應用**程式總覽] 頁面上所顯示的**應用程式識別碼**。 您會在稍後的步驟中使用此值。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在 Azure 入口網站中，搜尋並選取  **Azure AD B2C**。
1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *managementapp1*。
1. 選取 [**僅此組織目錄中的帳戶**]。
1. 在 [**許可權**] 底下，清除 [授與系統*管理員同意 openid 和 offline_access 許可權*] 核取方塊。
1. 選取 [註冊]。
1. 記錄顯示在應用程式總覽頁面上的**應用程式（用戶端）識別碼**。 您會在稍後的步驟中使用此值。