---
title: 在 Azure AD 中管理同盟憑證 | Microsoft Docs
description: 了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159024"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理同盟單一登入的憑證

在本文中，我們將介紹與 Azure 活動目錄 （Azure AD） 為將軟體作為服務 （SaaS） 應用程式建立聯合單一登入 （SSO） 而創建的證書相關的常見問題和資訊。 從 Azure AD 應用程式資源庫，或使用非資源庫的應用程式範本新增應用程式。 使用同盟 SSO 選項以設定應用程式。

本文僅與配置為通過[安全斷言標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML） 聯合使用 Azure AD SSO 的應用相關。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>為資源庫和非資源庫應用程式自動產生的憑證

從庫中添加新應用程式並配置基於 SAML 的登錄時（通過從應用程式概述頁中選擇**單一登入** > **SAML），Azure** AD 會為該應用程式生成有效期為三年的應用程式的證書。 要將活動證書下載為安全證書 **（.cer**） 檔，請返回到該頁面 （**基於 SAML 的登錄**），並在**SAML 簽章憑證**標題中選擇下載連結。 您可以選擇原始（二進位）證書或 Base64（基本 64 編碼文本）證書。 對於庫應用程式，本節可能還會顯示一個連結，用於下載證書作為聯合中繼資料**XML（.xml**檔），具體取決於應用程式的要求。

![SAML 活動簽章憑證下載選項](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

您還可以通過選擇**SAML 簽章憑證**標題的 **"編輯"** 圖示（鉛筆）來下載活動或非活動證書，該圖示顯示**SAML 簽章憑證**頁面。 選擇要下載的證書旁邊的省略號 （**...），** 然後選擇所需的證書格式。 您可以選擇以隱私增強郵件 （PEM） 格式下載證書。 此格式與 Base64 相同，但具有 **.pem**檔案名副檔名，這在 Windows 中無法識別為證書格式。

![SAML 簽章憑證下載選項（活動和非活動）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自訂同盟憑證的到期日期及變換新的憑證

預設情況下，Azure 將證書配置為在 SAML 單一登入配置期間自動創建證書三年後過期。 由於在保存證書後無法更改證書的日期，因此必須：

1. 創建具有所需日期的新證書。
1. 保存新證書。
1. 以正確的格式下載新證書。
1. 將新證書上載到應用程式。
1. 使新證書在 Azure 活動目錄門戶中處於活動狀態。

以下兩個部分可説明您執行這些步驟。

### <a name="create-a-new-certificate"></a>創建新證書

首先，創建並保存具有不同到期日期的新證書：

1. 登錄到 Azure[活動目錄門戶](https://aad.portal.azure.com/)。 將顯示**Azure 活動目錄管理中心**頁。
1. 在左側窗格中，選取 [企業應用程式]****。 將顯示帳戶中的企業應用程式清單。
1. 選擇受影響的應用程式。 將顯示應用程式的概述頁。
1. 在應用程式概述頁的左側窗格中，選擇 **"單一登入**"。
1. 如果出現 **"選擇單個登錄方法**頁"，請選擇**SAML**。
1. 在 **"使用 SAML 設置單個登錄 - 預覽**"頁中，找到**SAML 簽章憑證**標題並選擇 **"編輯"** 圖示（鉛筆）。 將顯示**SAML 簽章憑證**頁，該頁顯示每個證書的狀態（**活動**或**非活動**）、到期日期和指紋（雜湊字串）。
1. 選擇**新證書**。 證書清單下方有一個新行，到期日期預設為當前日期之後的三年。 （您的更改尚未保存，因此您仍然可以修改到期日期。
1. 在新的證書行中，將滑鼠懸停在到期日期列上，然後選擇 **"選擇日期"** 圖示（日曆）。 將顯示日曆控制項，顯示新行當前到期日期的一個月的天數。
1. 使用日曆控制項設置新日期。 您可以設置當前日期與當前日期後三年之間的任何日期。
1. 選取 [儲存]****。 新證書現在顯示的狀態為 **"非活動"、** 您選擇的到期日期和指紋。
1. 選擇**X**以返回到 **"使用 SAML 設置單一登入 - 預覽**"頁。

### <a name="upload-and-activate-a-certificate"></a>上傳並啟動證書

接下來，以正確的格式下載新證書，將其上載到應用程式，並使它在 Azure 活動目錄中處於活動狀態：

1. 查看應用程式的其他 SAML 登錄配置說明，請執行以下任一操作：

   - 選擇要在單獨的瀏覽器視窗或選項卡中查看的**配置指南**連結，或
   - 前往**設置**標題並選擇 **"查看分步說明**"，在邊欄中查看。

1. 在說明中，請注意證書上載所需的編碼格式。
1. 請按照前面有關[庫和非庫應用程式自動生成的證書](#auto-generated-certificate-for-gallery-and-non-gallery-applications)部分的說明進行操作。 此步驟以應用程式上載所需的編碼格式下載證書。
1. 如果要滾動到新證書，請返回**SAML 簽章憑證**頁面，在新保存的證書行中，選擇省略號 **（...），** 然後選擇 **"使證書處於活動狀態**"。 新證書的狀態將更改為**活動**，以前處於活動狀態的證書將更改為**非活動**狀態。
1. 繼續遵循您之前顯示的應用程式 SAML 登錄配置說明，以便您可以以正確的編碼格式上載 SAML 簽章憑證。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>為證書過期添加電子郵件通知位址

Azure AD 將在 SAML 證書過期前 60、30 和 7 天發送電子郵件通知。 您可以添加多個電子郵件地址來接收通知。 要指定電子郵件地址，您希望將通知發送到：

1. 在**SAML 簽章憑證**頁中，轉到**通知電子郵件地址**標題。 預設情況下，此標題僅使用添加應用程式的管理員的電子郵件地址。
1. 在最終電子郵件地址下方，鍵入應接收證書過期通知的電子郵件地址，然後按 Enter。
1. 對要添加的每個電子郵件地址重複上一步。
1. 對於要刪除的每個電子郵件地址，請選擇電子郵件地址旁邊的 **"刪除**"圖示（垃圾桶）。
1. 選取 [儲存]****。

您會收到來自 aadnotification@microsoft.com 的通知電子郵件。 為避免電子郵件發送到您的垃圾郵件位置，請將此電子郵件添加到您的連絡人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>更新即將到期的憑證

如果證書即將過期，則可以使用不會導致使用者嚴重停機的過程續訂證書。 要續訂過期證書：

1. 使用與現有證書重疊的日期，請先按照"[創建新證書](#create-a-new-certificate)"部分中的說明進行操作。 該日期限制證書過期造成的停機時間量。
1. 如果應用程式可以自動滾動證書，請按照以下步驟將新證書設置為活動：
   1. 返回**SAML 簽章憑證**頁面。
   1. 在新保存的證書行中，選擇省略號 **（...），** 然後選擇 **"使證書處於活動狀態**"。
   1. 跳過接下來的兩個步驟。

1. 如果應用一次只能處理一個證書，則選擇停機時間間隔以執行下一步。 （否則，如果應用程式未自動獲取新證書，但可以處理多個簽章憑證，則可以隨時執行下一步。
1. 在舊證書過期之前，請按照["上傳"中的說明操作，並提前啟動證書](#upload-and-activate-a-certificate)部分。
1. 登錄到應用程式以確保證書正常工作。

## <a name="related-articles"></a>相關文章

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](../saas-apps/tutorial-list.md)
- [搭配 Azure Active Directory 的應用程式管理](what-is-application-management.md)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [調試 Azure 活動目錄中的應用程式基於 SAML 的單一登入](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
