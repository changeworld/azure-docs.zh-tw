---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183768"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式]  ，然後選取 [新增]  。
1. 輸入應用程式的名稱。 例如，*nativeapp1*。
1. 對於 [原生用戶端]  ，選取 [是]  。
1. 輸入具有唯一配置的 [自訂重新導向 URI]  。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 選擇重新導向 URI 時，有兩個重要考量︰
    * **唯一**：每個應用程式的重新導向 URI 的配置必須是唯一。 在範例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`中，`com.onmicrosoft.contosob2c.exampleapp` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者選擇錯誤，登入就會失敗。
    * **完整**︰重新導向 URI 必須同時具備配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//oauth/` 可正常運作，而 `//oauth` 會失敗。 請勿在 URI 中包含特殊字元，例如底線。
1. 選取 [建立]  。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式註冊 (預覽)]  ，然後選取 [新增註冊]  。
1. 輸入應用程式的 [名稱]  。 例如，*nativeapp1*。
1. 在 [支援的帳戶類型]  下，選取 [任何組織目錄或任何識別提供者中的帳戶]  。
1. 在 [重新導向 URI]  下，使用下拉式清單來選取 [公用用戶端/原生 (行動裝置及桌上型電腦)]  。
1. 輸入具有唯一配置的重新導向 URI。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 選擇重新導向 URI 時，有兩個重要考量︰
    * **唯一**：每個應用程式的重新導向 URI 的配置必須是唯一。 在範例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`中，`com.onmicrosoft.contosob2c.exampleapp` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者選擇錯誤，登入就會失敗。
    * **完整**︰重新導向 URI 必須同時具備配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//oauth/` 可正常運作，而 `//oauth` 會失敗。 請勿在 URI 中包含特殊字元，例如底線。
1. 在 [權限]  下，選取 [對 openid 與 offline_access 權限授與管理員同意]  核取方塊。
1. 選取 [註冊]  。