---
title: 使用 Azure Active Directory B2C 設定無索引鍵的教學課程
titleSuffix: Azure AD B2C
description: 使用適用于無密碼 authentication 的 Azure Active Directory B2C 設定無索引鍵的教學課程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2ab1b5ae87cee6265c965c45b706c7fbf10dfe3f
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702949"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 設定無索引鍵

在此範例教學課程中，我們會提供有關如何使用 [無索引鍵](https://keyless.io/)來設定 AZURE ACTIVE DIRECTORY (AD) B2C 的指引。 使用 Azure AD B2C 作為身分識別提供者時，您可以將無索引鍵與任何客戶應用程式整合，為您的使用者提供真正的無密碼驗證。

無索引鍵的解決方案 **無索引鍵 Zero-Knowledge 生物特徵辨識 (ZKB™)** 提供無密碼的多重要素驗證，以消除詐騙、網路釣魚和認證重複使用，同時增強客戶體驗和保護其隱私權。

## <a name="pre-requisites"></a>必要條件

若要開始使用，您需要：

- Azure 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- [Azure AD B2C 的租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。 租使用者必須連結到您的 Azure 訂用帳戶。

- 無索引鍵雲端租使用者，取得免費 [試用帳戶](https://keyless.io/go)。

- 無索引鍵驗證器應用程式安裝在使用者的裝置上。

## <a name="scenario-description"></a>案例描述

無索引鍵整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者。

- Web 和行動應用程式–您選擇使用無索引鍵和 Azure AD B2C 保護的行動或 web 應用程式。

- 無索引鍵行動應用程式–無索引鍵行動裝置應用程式將用於驗證 Azure AD B2C 啟用的應用程式。

下列架構圖顯示執行。

![影像顯示無索引鍵架構圖](./media/partner-keyless/keyless-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取登入/註冊並輸入使用者名稱
| 2. | 應用程式會將使用者屬性傳送給 Azure AD B2C，以進行身分識別驗證。
| 3. | Azure AD B2C 會收集使用者屬性，並將屬性傳送至無索引鍵，以透過無索引鍵行動裝置應用程式驗證使用者。
| 4. | 無索引鍵會傳送推播通知給已註冊使用者的行動裝置，以臉部生物特徵辨識掃描的形式提供隱私權保留的驗證。
| 5. | 使用者回應推播通知之後，就會根據驗證結果，授與或拒絕使用者對客戶應用程式的存取權。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="add-a-new-identity-provider"></a>新增身分識別提供者

若要加入新的身分識別提供者，請遵循下列步驟：

1. 以 Azure AD B2C 租使用者的全域管理員身分登入 **[Azure 入口網站](https://portal.azure.com/#home)** 。

2. 選取頂端功能表上的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。

4. 流覽至  >  **Azure Active Directory B2C** 身分  >   **識別提供者** 的儀表板

5. 選取 [ **識別提供者**]。

6. 選取 [新增]  。

### <a name="configure-an-identity-provider"></a>設定身分識別提供者

若要設定身分識別提供者，請遵循下列步驟：

1. 選取 **識別提供者類型**  >  **OpenID Connect (預覽)**
2. 填寫表單以設定身分識別提供者：

   |屬性 | 值 |
   |:-----| :-----------|
   | 名稱   | 無索引鍵 |
   | 中繼資料 URL | 插入託管無索引鍵 Authentication 應用程式的 URI，後面接著特定路徑，例如 https://keyless.auth/.well-known/openid-configuration |
   | 用戶端密碼 | 與無索引鍵 Authentication 實例相關聯的秘密，與之前所設定的不同。 插入您選擇的複雜字串。 稍後會在無索引鍵容器設定中使用此秘密。|
   | 用戶端識別碼 | 用戶端的識別碼。 此識別碼稍後會在無索引鍵容器設定中使用。|
   | 影響範圍 | openid |
   | 回應類型 | id_token |
   | 回應模式 | form_post|

3. 選取 [確定]。

4. 選取 [ **對應此識別提供者的宣告**]。

5. 填寫表單以對應身分識別提供者：

   |屬性 | 值 |
   |:-----| :-----------|
   | UserID    | 透過訂用帳戶 |
   | 顯示名稱 | 透過訂用帳戶 |
   | 回應模式 | 透過訂用帳戶 |

6. 選取 [ **儲存** ] 以完成新的 Open ID CONNECT (OIDC) 身分識別提供者的設定。

### <a name="create-a-user-flow-policy"></a>建立使用者流程原則

您現在應該會看到無索引鍵成為 B2C 身分識別提供者中所列的新 OIDC 識別提供者。

1. 在您的 Azure AD B2C 租使用者中，選取 [ **原則**] 底下的 [ **使用者流程**]。

2. 選取 [ **新增** 使用者流程]。

3. 選取 [ **註冊並登入**]，選取 **版本**，然後選取 [ **建立**]。

4. 輸入原則的 **名稱** 。

5. 在 [識別提供者] 區段中，選取您新建立的無索引鍵身分識別提供者。

6. 設定使用者流程的參數。 插入名稱並選取您已建立的身分識別提供者。 您也可以新增電子郵件地址。 在此情況下，Azure 不會將登入程式直接重新導向至無索引鍵，而是會顯示一個畫面，讓使用者可以選擇他們想要使用的選項。

7. 將 [ **多重要素驗證** ] 欄位保留原狀。

8. 選取 [**強制執行條件式存取原則**]

9. 在 [ **使用者屬性和權杖宣告**] 底下，選取 [收集屬性] 選項中的 [ **電子郵件地址** ]。 您可以將 Azure Active Directory 可針對使用者收集的所有屬性，連同 Azure AD B2C 可傳回用戶端應用程式的宣告一起加入。

10. 選取 [建立]  。

11. 建立成功之後，請選取新的 **使用者流程**。

12. 在左面板中，選取 [ **應用程式宣告**]。 在 [選項] 底下，勾選 [ **電子郵件** ] 核取方塊並選取 [ **儲存**]。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [Identity Experience Framework]。

2. 選取您先前建立的 SignUpSignIn。

3. 選取 [執行使用者流程]，然後選取設定：

   a. 應用程式：選取已註冊的應用程式 (範例為 JWT) 

   b. 回復 URL：選取重新導向 URL

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 建立使用者屬性之後，會在流程期間呼叫無索引鍵。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
