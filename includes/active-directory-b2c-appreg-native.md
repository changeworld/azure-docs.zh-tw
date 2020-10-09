---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793664"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選取 [應用程式註冊]****，然後選取 [新增註冊]****。
1. 輸入應用程式的 [名稱]****。 例如，*nativeapp1*。
1. 在 [支援的帳戶類型]**** 下，選取 [任何組織目錄或任何識別提供者中的帳戶]****。
1. 在 [重新導向 URI]**** 下，使用下拉式清單來選取 [公用用戶端/原生 (行動裝置及桌上型電腦)]****。
1. 輸入具有唯一配置的重新導向 URI。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 選擇重新導向 URI 時，有一些重要考量︰
    * **開發** 為了供開發之用，您可以將重新導向 URI 設定為 `http://localhost`，Azure AD B2C 將會遵循要求中的任何連接埠。 如果已註冊的 URI 包含連接埠，Azure AD B2C 將只會使用該連接埠。 例如，如果已註冊的重新導向 URI 為 `http://localhost`，則要求中的重新導向 URI 可能是 `http://localhost:<randomport>`。 如果已註冊的重新導向 URI 為 `http://localhost:8080`，則要求中的重新導向 URI 必須是 `http://localhost:8080`。
    * **唯一**：每個應用程式的重新導向 URI 的配置必須是唯一。 在範例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`中，`com.onmicrosoft.contosob2c.exampleapp` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者選擇錯誤，登入就會失敗。
    * **完整**︰重新導向 URI 必須同時具備配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//oauth/` 可正常運作，而 `//oauth` 會失敗。 請勿在 URI 中包含特殊字元，例如底線。
1. 在 [權限]**** 下，選取 [對 openid 與 offline_access 權限授與管理員同意]** 核取方塊。
2. 選取 [註冊]****。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選取 [應用程式 (舊版)]****，然後選取 [新增]****。
1. 輸入應用程式的名稱。 例如，*nativeapp1*。
1. 對於 [原生用戶端]****，選取 [是]****。
1. 輸入具有唯一配置的 [自訂重新導向 URI]****。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 選擇重新導向 URI 時，有兩個重要考量︰
    * **唯一**：每個應用程式的重新導向 URI 的配置必須是唯一。 在範例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`中，`com.onmicrosoft.contosob2c.exampleapp` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者選擇錯誤，登入就會失敗。
    * **完整**︰重新導向 URI 必須同時具備配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//oauth/` 可正常運作，而 `//oauth` 會失敗。 請勿在 URI 中包含特殊字元，例如底線。
1. 選取 [建立]****。