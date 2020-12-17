---
title: 使用尼維斯設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與尼維斯以進行無密碼 authentication
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 19aa847ce7ce1a6ba727a4733aefcdfad845e8c6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629386"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>使用無密碼 authentication 的 Azure Active Directory B2C 設定尼維斯的教學課程

在此範例教學課程中，您將瞭解如何使用  [尼維斯](https://www.nevis.net/solution/authentication-cloud) 擴充 Azure AD B2C，以啟用無密碼 authentication。 尼維斯提供行動優先、功能完整的終端使用者體驗與尼維斯存取應用程式，以提供強式客戶驗證並符合付款服務指示詞 2 (PSD2) 交易需求。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- 尼維斯 [試用帳戶](https://www.nevis-security.com/aadb2c/)

- Azure AD 訂用帳戶。 如果您沒有帳戶，請取得 [免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的 [Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 使用者。

- 如果您想要將尼維斯整合到您的註冊原則流程中，請使用 [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)設定 Azure AD B2C 環境。

## <a name="scenario-description"></a>案例描述

在此案例中，請將完整品牌的存取應用程式新增至您的後端應用程式，以進行無密碼 authentication。 下列元件組成方案：

- Azure AD B2C 的租使用者，並將登入和註冊原則合併至您的後端
- 尼維斯實例及其 REST API 以增強 Azure AD B2C
- 您自己的品牌化存取應用程式

此圖顯示執行。

![使用 Azure AD B2C 和尼維斯的高階密碼登入流程](./media/partner-nevis/nevis-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者嘗試透過 Azure AD B2C 登入和註冊原則登入或註冊應用程式。
| 2. | 在註冊期間，會使用 QR 代碼將尼維斯存取應用程式註冊到使用者裝置。 使用者裝置上會產生私密金鑰，並使用此金鑰來簽署使用者要求。
| 3. |  Azure AD B2C 使用 RESTful 技術設定檔，以尼維斯解決方案開始登入。
| 4. | 登入要求會傳送至存取應用程式，可以是推播訊息、QR 代碼或深層連結。
| 5. | 使用者使用其生物識別來核准登入嘗試。 接著會將訊息傳回給尼維斯，以使用儲存的公開金鑰來驗證登入。
| 6. | Azure AD B2C 將最後一個要求傳送到尼維斯，以確認登入已成功完成。
| 7. |根據 Azure AD B2C 使用者的成功/失敗訊息被授與/拒絕存取應用程式。

## <a name="integrate-your-azure-ad-b2c-tenant"></a>整合您的 Azure AD B2C 租使用者

### <a name="onboard-to-nevis"></a>上架到尼維斯 

[註冊您的尼維斯帳戶](https://www.nevis-security.com/aadb2c/)。
您將會收到兩封電子郵件：

1. 管理帳戶通知

2. 行動裝置應用程式邀請。

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>將您的 Azure AD B2C 租使用者新增至您的尼維斯帳戶

1. 從尼維斯管理帳戶試用電子郵件中，將您的管理金鑰複製到剪貼簿。

2. https://console.nevis.cloud/在瀏覽器中開啟。

3. 使用您的金鑰登入管理主控台。

4. 選取 [**新增實例**]

5. 選取新建立的實例以開啟它。

6. 在側邊巡覽列中，選取 [**自訂** 整合]

7. 選取 [ **新增自訂整合**]。

8. 在 [整合名稱] 中，輸入您的 Azure AD B2C 租使用者名稱。

9. 針對 [URL/網域]，輸入 `https://yourtenant.onmicrosoft.com`

10. 選取 [**下一步**]

>[!NOTE]
>您稍後將需要尼維斯存取權杖。

11. 選取 [完成]  。

### <a name="install-the-nevis-access-app-on-your-phone"></a>在您的手機上安裝尼維斯存取應用程式

1. 從尼維斯行動裝置應用程式試用電子郵件，開啟 **測試航班應用程式** 邀請。

2. 安裝應用程式。

3. 遵循提供的指示來安裝尼維斯存取應用程式。

### <a name="integrate-azure-ad-b2c-with-nevis"></a>將 Azure AD B2C 與尼維斯整合

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。

2. 切換至您的 Azure AD B2C 租使用者。 請確定您已選取正確的租使用者，因為 Azure AD B2C 租使用者通常位於不同的租使用者中。

3. 在功能表中，選取 [ **Identity Experience Framework (IEF)**

4. 選取 **原則金鑰**

5. 選取 **[新增]** ，並使用下列設定建立新的金鑰：

      a. 在選項中選取 **手動**

      b. 將名稱設定為 **AuthCloudAccessToken**

      c. 在 [秘密] 欄位中貼上先前儲存的 **尼維斯存取權杖**

      d. 針對 [金鑰使用方法] 選取 [**加密**]

      e. 選取 [建立] 

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>設定 nevis.html 並上傳至 Azure blob 儲存體

1. 在您的身分識別環境 (IDE) 中，移至 [ [**原則**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) ] 資料夾。

2. 開啟  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) 檔案。

3. 將  **authentication_cloud_url** 取代為您的尼維斯管理員主控台的 url `https://<instance_id>.mauth.nevis.cloud` 。

4. **將變更儲存** 至檔案。

5. 遵循 [指示](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#2-create-an-azure-blob-storage-account) ，並將 **nevis.html** 檔案上傳至您的 Azure blob 儲存體。

6. 依照 [指示](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#3-configure-cors) 執行，並啟用此檔案的跨原始來源資源分享 (CORS) 。

7. 上傳完成並啟用 CORS 之後，請選取清單中的 **nevis.html** 檔案。

8. 在 [ **總覽** ] 索引標籤的 [ **URL**] 旁，選取 [ **複製連結** ] 圖示。

9. 在新的瀏覽器索引標籤中開啟連結，以確定它會顯示灰色方塊。

>[!NOTE]
>您稍後將需要 blob 連結。

### <a name="customize-your-trustframeworkbasexml"></a>自訂您的 TrustFrameworkBase.xml

1. 在 IDE 中，移至 [ [**原則**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) ] 資料夾。

2. 開啟 [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) 檔案。

3. 將 **>yourtenant.msappproxy.net** 取代為 **TenantId** 中的 Azure 租使用者帳戶名稱。

4. 將 **>yourtenant.msappproxy.net** 取代為您在 **>publicpolicyuri** 中的 Azure 租使用者帳戶名稱。

5. 以您的尼維斯管理主控台 URL 取代所有 **authentication_cloud_url** 實例

6. **將變更儲存** 至您的檔案。

### <a name="customize-your-trustframeworkextensionsxml"></a>自訂您的 TrustFrameworkExtensions.xml

1. 在 IDE 中，移至 [ [**原則**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) ] 資料夾。

2. 開啟 [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) 檔案。

3. 將 **>yourtenant.msappproxy.net** 取代為 **TenantId** 中的 Azure 租使用者帳戶名稱。

4. 將 **>yourtenant.msappproxy.net** 取代為您在 **>publicpolicyuri** 中的 Azure 租使用者帳戶名稱。

5. 在 **BasePolicy** 下的 **TenantId** 中，也要以您的 Azure 租使用者帳戶名稱取代 _>yourtenant.msappproxy.net_ 。

6. 在 **BuildingBlocks** 下，將 **LoadUri** 取代為 blob 儲存體帳戶中 _nevis.html_ 的 blob 連結 URL。

7. **儲存** 盤案。

### <a name="customize-your-signuporsigninxml"></a>自訂您的 SignUpOrSignin.xml

1. 在 IDE 中，移至 [ [**原則**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) ] 資料夾。

2. 開啟 [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) 檔案。

3. 將 **>yourtenant.msappproxy.net** 取代為 **TenantId** 中的 Azure 租使用者帳戶名稱。

4. 將 **>yourtenant.msappproxy.net** 取代為您在 **>publicpolicyuri** 中的 Azure 租使用者帳戶名稱。

5. 在 **BasePolicy** 下的 **TenantId** 中，也要以您的 Azure 租使用者帳戶名稱取代 **>yourtenant.msappproxy.net** 。

6. **儲存** 盤案。

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>將您的自訂原則上傳至 Azure AD B2C

1. 開啟您的 [Azure AD B2C 租](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 使用者首頁。

2. 選取 [識別體驗架構]。

3. 選取 [上傳自訂原則]。

4. 選取您修改的 **TrustFrameworkBase.xml** 檔案。

5. 選取 [ **覆寫自訂原則（如果已存在** ）] 核取方塊。
6. 選取 [上傳] 。

7. 針對 **TrustFrameworkExtensions.xml** 重複步驟5和6。

8. 針對 **SignUpOrSignin.xml** 重複步驟5和6。

## <a name="test-the-user-flow"></a>測試使用者流程

### <a name="test-account-creation-and-nevis-access-app-setup"></a>測試帳戶建立和尼維斯存取應用程式設定

1. 開啟您的 [Azure AD B2C 租](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 使用者首頁。

2. 選取 [識別體驗架構]。

3. 向下滾動至自訂原則，然後選取 [ **B2C_1A_signup_signin**]。

4. 選取 [立即執行]。

5. 在快顯視窗中，選取 [ **立即註冊**]。

6. 新增您的電子郵件地址。

7. 選取 [ **傳送驗證碼**]。

8. 複製電子郵件中的驗證碼。

9. 選取 [驗證]。

10. 在表單中填入新的密碼和顯示名稱。

11. 選取 [建立]。

12. 您將會進入 [QR 代碼掃描] 頁面。

13. 在您的手機上，開啟 **尼維斯存取應用程式**。

14. 選取 **臉部識別碼**。

15. 當畫面顯示 **驗證器註冊成功** 時，請選取 [ **繼續**]。

16. 在您的手機上，再次向您的臉部進行驗證。

17. 系統會將您帶往 [jwt.ms](https://jwt.ms) 登陸頁面，以顯示已解碼的權杖詳細資料。

### <a name="test-the-pure-passwordless-sign-in"></a>測試純無密碼登入

1. 在 [ **Identity Experience Framework**] 下，選取 **B2C_1A_signup_signin**。

2. 選取 [立即執行]。

3. 在快顯視窗中，選取 [ **無密碼 Authentication**]。

4. 輸入您的電子郵件地址。

5. 選取 [繼續]。

6. 在您的手機上，于 [通知] 中選取 [ **尼維斯存取代理程式更新**]。

7. 使用您的臉部進行驗證。

8. 您將會自動進入 [jwt.ms](https://jwt.ms) 登陸頁面，以顯示您的權杖。

## <a name="next-steps"></a>下一步

如需詳細資訊，請參閱下列文章

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
