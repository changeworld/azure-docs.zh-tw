---
title: itsme OpenID Connect 與 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 瞭解如何使用 client_secret 使用者流程原則，整合 Azure AD B2C authentication 與 itsme OIDC。 itsme 是數位識別碼應用程式。 它可以讓您安全地登入，而不需要卡片讀取器、密碼、雙因素驗證和多個 PIN 碼。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8229864acc80a27994ae3c795213dc2a65d22db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385564"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 itsme OpenID Connect （OIDC）

Itsme 數位識別碼應用程式可讓您安全地登入，而不需要卡片讀取器、密碼、雙因素驗證或多個 PIN 碼。 Itsme 應用程式會使用已驗證的身分識別，提供強大的客戶驗證。 在本文中，您將瞭解如何使用用戶端密碼使用者流程原則，將 Azure AD B2C authentication 與 itsme OpenID Connect （OIDC）進行整合。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。
* Itsme 提供的用戶端識別碼（也稱為合作夥伴代碼）。
* Itsme 提供的服務程式代碼。
* 您的 itsme 帳戶的用戶端密碼。

## <a name="scenario-description"></a>案例描述

![itsme 架構圖](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

|   |   |
|------|------|
|1     | 在您的網站或應用程式中，藉由調整 Azure AD B2C 使用者流程來包含 [**以 Itsme 登入**] 按鈕。 互動流程會在使用者按一下此按鈕時啟動。  |
|2     | Azure AD B2C 將授權要求傳送至 itsme 用戶端密碼 API，以啟動 OpenID connect 流程。 有一個知名/OpenID 設定端點可供使用，其中包含端點的相關資訊。  |
|3     | Itsme 環境會將使用者重新導向至 itsme 識別自己的頁面，讓使用者填寫其電話號碼。  |
|4     | Itsme 環境會接收來自使用者的電話號碼，並驗證正確性。  |
|5     | 如果電話號碼屬於作用中的 itsme 使用者，就會為 itsme 應用程式建立動作。  |
|6     | 使用者會開啟 itsme 應用程式、檢查要求，並確認該動作。  |
|7     |  應用程式會通知 itsme 環境動作已確認。 |
|8     |  Itsme 環境會傳回要 Azure AD B2C 的 OAuth 授權碼。 |
|9     |  使用授權程式碼，Azure AD B2C 會執行權杖要求。 |
| 10 | Itsme 環境會檢查權杖要求，如果仍然有效，則會傳回 OAuth 存取權杖，以及包含所要求之使用者資訊的識別碼權杖。 |
| 11 | 最後，系統會將使用者重新導向至 [重新導向 url] 做為已驗證的使用者。  |
|   |   |

## <a name="onboard-with-itsme"></a>使用 itsme 上架

1. 若要使用 itsme 建立帳戶，請造訪[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)的 itsme。

2. 藉由傳送電子郵件到來啟用您的 itsme 帳戶 onboarding@itsme.be 。 您將會收到您的 B2C 設定所需的**合作夥伴代碼**和**服務代碼**。

3. 啟用 itsme 合作夥伴帳戶之後，您會收到一封電子郵件，其中包含**用戶端密碼**的一次性連結。

4. 遵循[itsme](https://business.itsme.be/en)的指示完成設定。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>在 Azure AD B2C 中設定新的識別提供者

> [!NOTE]
> 如果您還沒有租用戶，請[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到 Azure 訂用帳戶。

1. 請確定您使用的是包含 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。

2. 在 [ **Azure 服務**] 底下，選取 [ **Azure AD B2C** ] （或選取 [**更多服務**]，然後使用 [**所有服務**] 搜尋方塊來搜尋*Azure AD B2C*）。

3. 選取 [**識別提供者**]，然後選取 **[新增 OpenID Connect 提供者]**。

4. 在表單中填入下列資訊：

   |屬性 | 值 |
   |------------ |------- |
   | 名稱 | itsme |
   | 中繼資料 URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>其中 `<environment>` 是 `e2e` （測試環境）或 `prd` （生產）  |
   | ClientID     | 您的**用戶端識別碼**，也稱為貿易**夥伴代碼**  |
   | 用戶端密碼 | 您的**client_secret** |
   | 影響範圍  | openid 服務： YOURSERVICECODE 設定檔電子郵件 [電話] [位址]  |
   |回應類型 | 代碼 |
   |回應模式 | 查詢 |
   |網域提示 | *您可以將此保留空白* |
   |UserID | sub |
   |顯示名稱 | NAME |
   |指定的名稱 | given_name |
   |Surname | family_name |
   |電子郵件 | 電子郵件|

5. 選取 [儲存]。

### <a name="configure-a-user-flow"></a>設定使用者流程

1. 在您的 Azure AD B2C 租使用者的 [**原則**] 底下，選取 [**使用者流程**]。

2. 選取 [**新增使用者流程**]。

3. 選擇 [**註冊並登入**]。

4. 輸入 [名稱]。

5. 在 [**識別提供者**] 區段中，選取 [ **itsme**]。

6. 選取 [建立]。

7. 選取使用者流程名稱，以開啟新建立的使用者流程。

8. 選取 [**屬性**] 並調整下列值：

   * 將**存取權 & 識別碼權杖存留期（分鐘）** 變更為**5**。
   * 將重新整理**權杖滑動視窗存留期**變更為**不過期**。

### <a name="register-an-application"></a>註冊應用程式

1. 在 B2C 租使用者的 [**管理**] 底下，選取 [**應用程式註冊**  >  **新增註冊**]。

2. 提供應用程式的**名稱**，並輸入您的重新**導向 URI**。 基於測試目的，請輸入 `https://jwt.ms` 。

3. 請確定已**停用**多重要素驗證。

4. 選取 [註冊]。

   a. 基於測試目的，請選取 [**驗證**]，然後在 **[隱含授**與] 底下，選取 [**存取權杖**] 和 [**識別碼權杖**] 核取方塊。  

   b. 選取 [儲存]。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 在 B2C 租使用者的 [**原則**] 底下，選取 [**使用者流程**]。

2. 選取您先前建立的使用者流程。

3. 選取 [執行使用者流程]。

   a. **應用程式**：*選取已註冊的應用程式*

   b. **回復 url**：*選取 [重新導向 url* ]

4. [Itsme**識別自己**] 頁面隨即出現。  

5. 輸入您的行動電話號碼，然後選取 [**傳送**]。

6. 確認 itsme 應用程式中的動作。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

* [Azure AD B2C 中的自訂原則](custom-policy-overview.md)

* [開始使用 Azure AD B2C 中的自訂原則](custom-policy-get-started.md?tabs=applications)