---
title: 使用 Zscaler 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 223f8acd6aad7aaf4c37e0b2eae2df882ed2ad1d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629369"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>使用安全混合式存取 Azure Active Directory B2C 設定 Zscaler 私人存取的教學課程

在本教學課程中，您將瞭解如何整合 Azure AD B2C authentication 與 [Zscaler Private Access (ZPA) ](https://www.zscaler.com/products/zscaler-private-access)。 ZPA 提供以原則為基礎、安全的私用應用程式和資產存取，而不會有虛擬私人網路 (VPN) 的成本、麻煩或安全性風險。 Zscaler 的安全混合式存取供應專案可在結合 Azure AD B2C 時，為取用者面向的應用程式提供零攻擊面。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

- [ZPA 訂用帳戶](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>案例描述

ZPA 整合包含下列元件：

- Azure AD B2C-身分識別提供者 (IdP) 負責驗證使用者的認證。 它也會負責註冊新的使用者。

- ZPA-負責強制執行 [零信任存取](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)來保護 web 應用程式的服務。

- Web 應用程式-裝載使用者嘗試存取的服務。

下圖顯示 ZPA 如何與 Azure AD B2C 整合。

![影像顯示 zscaler 架構圖](media/partner-zscaler/zscaler-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達 ZPA 使用者入口網站或 ZPA 瀏覽器存取應用程式。
| 2. | ZPA 需要使用者內容資訊，才能決定是否允許使用者存取 web 應用程式。 若要驗證使用者，ZPA 會執行 SAML 重新導向至 Azure AD B2C 登入頁面。  
| 3. | 使用者抵達 Azure AB B2C 登入頁面。 如果是新的使用者，使用者會註冊以建立新的帳戶。 現有的使用者會使用其現有的認證登入。 Azure AD B2C 會驗證使用者的身分識別。
| 4. | 驗證成功之後，Azure AD B2C 會將使用者重新導向回 ZPA，以及 SAML 判斷提示。 ZPA 會驗證 SAML 判斷提示，並設定使用者的內容。
| 5. | ZPA 會評估使用者的存取原則。 如果允許使用者存取 web 應用程式，則允許連接通過。

## <a name="onboard-to-zpa"></a>上架至 ZPA

本教學課程假設您已有 ZPA 的工作設定。 如果您是 ZPA 入門，請參閱 [ZPA 的逐步設定指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>第1部分-將 Azure AD B2C 設定為 ZPA 上的 IdP

在 ZPA 上將 Azure AD B2C 設定為身分 [識別提供者 (IdP) ](https://help.zscaler.com/zpa/configuring-idp-single-sign)：

1. 登入 [ZPA 系統管理員入口網站](https://admin.private.zscaler.com)

2. 前往 **Administration**  >  **IdP Configuration**

3. 選取 [**新增 IdP** 設定]

4. 如需 **1 IdP 資訊**，請輸入下列內容：

   a. **名稱**： Azure AD B2C

   b. **單一登入**：選取使用者

   c. **網域**：選擇要與此 IdP 建立關聯的驗證網域，然後選取 [**完成**]

   ![顯示 idp 資訊的影像](media/partner-zscaler/add-idp-configuration.png)

5. 選取 [**下一步**]

6. 針對 **2 SP 中繼資料**：

   a. 複製服務提供者 URL，並記下它以供稍後使用。

   b. 複製服務提供者實體識別碼，並記下它以供稍後使用。

   ![影像顯示 sp 中繼資料資訊](media/partner-zscaler/sp-metadata.png)

7. 選取 **暫停**

IdP 設定的其餘部分將會在設定 Azure AD B2C 之後繼續進行。

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>第2部分-在 Azure AD B2C 中設定自訂原則

>[!Note]
>只有當您還沒有自訂原則時，才需要執行此步驟。 如果您已經有一或多個自訂原則，您可以略過此步驟。

在 [Azure Active Directory B2C 中開始使用自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) 的文章提供如何在您的 Azure AD B2C 租使用者上設定自訂原則的指示。

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>第3部分-將 ZPA 註冊為 Azure AD B2C 中的 SAML 應用程式

在 [Azure AD B2C 中註冊 saml 應用程式](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) 的文章會提供如何在 Azure AD B2C 中設定 saml 應用程式的指示。 在 [步驟 3.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)中，您將會提供 Azure AD B2C 所使用的 IdP SAML 中繼資料 URL。 您將需要此，以供稍後使用。

依照 [步驟 4.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest)中的指示進行。 在指示的步驟4.2 中，您必須更新應用程式資訊清單。 更新屬性，如下所示：

- **identifierUris**：使用第1部分中所述的服務提供者實體識別碼 **，步驟 6a**。

- **samlMetadataUrl**：略過此屬性，因為 ZPA 不會裝載 SAML 中繼資料 URL。

- **replyUrlsWithType**：使用第1部分中所述的服務提供者 URL **，步驟 6b**。

- **logoutUrl**：略過此屬性，因為 ZPA 不支援登出 URL。

其餘步驟與本教學課程無關。

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>第4部分-將 IdP 的 SAML 中繼資料從 Azure AD B2C 解壓縮

從上一個步驟中，您必須以下列格式取得 SAML 中繼資料 URL：

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

其中 `<tenant-name>` 是您 Azure AD B2C 租使用者的名稱，而 `<policy-name>` 是您在上一個步驟中建立的自訂 SAML 原則名稱。

例如，" https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata "

開啟網頁瀏覽器並流覽至 SAML 中繼資料 URL。 當頁面載入時，以滑鼠右鍵按一下頁面上的任何位置。 選取 [ **另存** 新檔]，並將檔案儲存在您的電腦上;您將在下一個部分使用此功能。

### <a name="part-5---complete-idp-configuration-on-zpa"></a>第5部分-ZPA 上的完整 IdP 設定

[在 ZPA 系統管理員入口網站中](https://help.zscaler.com/zpa/configuring-idp-single-sign)完成部分設定的 IdP 設定：

1. 登入 [ZPA 系統管理員入口網站](https://admin.private.zscaler.com)

2. 移至 **Administration**  >  **IdP Configuration**。

3. 選取在第1部分中設定的 IdP，然後選取 [ **繼續**]。

4. 針對 **3 建立 IdP**

   a. 移至 **IdP 中繼資料** 檔案  >  **選取** 檔案，並上傳您在第4部分儲存的中繼資料檔案。

   b. 確認已 **啟用** IdP 設定的 **狀態**。

   c. 選取 [儲存]。

      ![影像顯示建立 idp 資訊](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>測試解決方案

移至 ZPA 使用者入口網站或瀏覽器存取應用程式，並測試註冊或登入程式。 它應該會導致 SAML 驗證成功。

## <a name="next-steps"></a>下一步

如需詳細資訊，請參閱下列文章：

- [在 Azure Active Directory B2C 中開始使用自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [在 Azure AD B2C 中註冊 SAML 應用程式](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [適用于 ZPA 的逐步設定指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [設定單一登入的 IdP](https://help.zscaler.com/zpa/configuring-idp-single-sign)
