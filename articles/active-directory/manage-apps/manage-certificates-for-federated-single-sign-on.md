---
title: 在 Azure AD 中管理同盟憑證 | Microsoft Docs
description: 了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1731d5ea5d8db9ea1c5855a32d2daca0387c0bf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763205"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理同盟單一登入的憑證

在本文中，我們將討論與憑證相關的常見問題和資訊，Azure Active Directory (Azure AD) 建立來建立對您的軟體即服務 (SaaS) 應用程式的同盟單一登入 (SSO) 。 從 Azure AD 應用程式資源庫，或使用非資源庫的應用程式範本新增應用程式。 使用同盟 SSO 選項以設定應用程式。

本文僅與設定為透過 [安全性聲明標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 同盟使用 Azure AD SSO 的應用程式有關。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>為資源庫和非資源庫應用程式自動產生的憑證

當您從資源庫新增應用程式，並從 [應用程式總覽]) 頁面中選取 [**單一登入**SAML] (設定 saml 登入時  >  **SAML** ，Azure AD 會為應用程式產生三年有效的憑證。 若要下載作用中的憑證做為安全性憑證 (**.cer**) 檔，請返回該頁面 (以 **saml 為基礎** 的登入) ，然後選取 [ **saml 簽署憑證** ] 標題中的下載連結。 您可以選擇原始 (二進位) 憑證或 Base64 (base 64 編碼的文字) 憑證。 針對資源庫應用程式，本節也會顯示一個連結，以根據應用程式的需求，將憑證下載為 (.xml 檔案的同盟中繼資料 XML) **。**

![SAML 主動式簽署憑證下載選項](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

您也可以下載作用中或非使用中的憑證，方法是選取 **Saml 簽署憑證** 標題的 **編輯** 圖示 (鉛筆) ，它會顯示 [ **saml 簽署憑證** ] 頁面。 選取您要下載的憑證旁的省略號 (**...**) ，然後選擇您想要的憑證格式。 您可以使用額外的選項，以隱私權增強的郵件 (PEM) 格式下載憑證。 此格式與 Base64 相同，但副檔名為 **pem** ，在 Windows 中無法辨識為憑證格式。

![SAML 簽署憑證下載選項 (作用中和非使用中) ](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自訂同盟憑證的到期日期及變換新的憑證

根據預設，Azure 會將憑證設定為在「SAML 單一登入」設定期間自動建立的三年之後到期。 因為您無法在儲存憑證之後變更該憑證的日期，所以您必須：

1. 建立具有所需日期的新憑證。
1. 儲存新的憑證。
1. 以正確的格式下載新憑證。
1. 將新憑證上傳至應用程式。
1. 在 Azure Active Directory 入口網站中啟用新的憑證。

下列兩節可協助您執行這些步驟。

### <a name="create-a-new-certificate"></a>建立新的憑證

首先，使用不同的到期日建立並儲存新的憑證：

1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 **Azure Active Directory 系統管理中心**] 頁面隨即出現。
1. 在左側窗格中，選取 [企業應用程式]****。 您帳戶中的企業應用程式清單隨即出現。
1. 選取受影響的應用程式。 應用程式的 [總覽] 頁面隨即出現。
1. 在 [應用程式總覽] 頁面的左窗格中，選取 [ **單一登入**]。
1. 如果出現 [ **選取單一登入方法** ] 頁面，請選取 [ **SAML**]。
1. 在 [ **以 SAML 預覽設定單一 Sign-On** ] 頁面中，尋找 [ **saml 簽署憑證** ] 標題，然後選取 [ **編輯** ] 圖示 (鉛筆) 。 [ **SAML 簽署憑證**] 頁面隨即出現，其中顯示 (作用中或**非**使用中**的狀態) ** 、到期日和指紋 (每個憑證) 的雜湊字串。
1. 選取 [ **新憑證**]。 新的資料列會出現在 [憑證] 清單下方，其中的到期日預設為目前日期後的三年。  (您的變更尚未儲存，所以您仍然可以修改到期日。 ) 
1. 在新的憑證資料列中，將滑鼠停留在 [到期日] 資料行上方，然後選取 (日曆) 的 [ **選取日期** ] 圖示。 行事曆控制項隨即出現，並顯示新資料列目前到期日的月份天數。
1. 使用行事曆控制項來設定新的日期。 您可以設定目前日期與目前日期的三年之間的任何日期。
1. 選取 [儲存]****。 新憑證的狀態會顯示為 [ **非**作用中]、您所選擇的到期日和 [指紋]。
1. 選取 **X** 以返回 [ **設定單一 Sign-On 與 SAML-預覽** ] 頁面。

### <a name="upload-and-activate-a-certificate"></a>上傳並啟用憑證

接下來，以正確的格式下載新憑證、將其上傳至應用程式，並讓它在 Azure Active Directory 中啟用：

1. 查看應用程式的其他 SAML 登入設定指示，方法是：

   - 選取設定 **指南** 連結，以在另一個瀏覽器視窗或索引標籤中進行查看，或
   - 移至 [ **設定** ] 標題，然後選取 [ **view 逐步** 解說] 以在提要欄位中查看。

1. 在指示中，請注意憑證上傳所需的編碼格式。
1. 請遵循稍早 [針對資源庫和非資源庫應用程式的自動產生憑證](#auto-generated-certificate-for-gallery-and-non-gallery-applications) 一節中的指示。 此步驟會下載應用程式上傳所需的編碼格式的憑證。
1. 當您想要變換至新的憑證時，請返回 [ **SAML 簽署憑證** ] 頁面，然後在新儲存的憑證資料列中，選取省略號 (**...**) 然後選取 [ **讓憑證變成**使用中]。 新憑證的狀態會變更為 [ **作用中]**，而先前作用中的憑證會變更為 [ **非**作用中] 狀態。
1. 繼續遵循您稍早所顯示的應用程式 SAML 登入設定指示，讓您可以用正確的編碼格式上傳 SAML 簽署憑證。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>新增憑證到期的電子郵件通知位址

Azure AD 會在 SAML 憑證到期之前傳送電子郵件通知60、30和7天。 您可以新增一個以上的電子郵件地址來接收通知。 若要指定要傳送通知的電子郵件地址 (es) ：

1. 在 [ **SAML 簽署憑證** ] 頁面中，移至 [ **通知電子郵件地址** ] 標題。 依預設，此標題只會使用新增應用程式之系統管理員的電子郵件地址。
1. 在 [最終電子郵件地址] 下，輸入應接收憑證到期通知的電子郵件地址，然後按 Enter。
1. 針對您要新增的每個電子郵件地址重複上述步驟。
1. 針對您想要刪除的每個電子郵件地址，選取 [ **刪除** ] 圖示 (可在電子郵件地址旁) 的垃圾郵件。
1. 選取 [儲存]****。

您會收到來自 aadnotification@microsoft.com 的通知電子郵件。 若要避免電子郵件進入您的垃圾郵件位置，請將這封電子郵件新增至您的連絡人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>更新即將到期的憑證

如果憑證即將到期，您可以使用不會導致使用者嚴重停機的程式來更新憑證。 若要更新即將到期的憑證：

1. 使用與現有憑證重迭的日期，遵循稍早 [建立新憑證](#create-a-new-certificate) 一節中的指示。 該日期會限制憑證到期所造成的停機時間。
1. 如果應用程式可以自動變換憑證，請遵循下列步驟，將新憑證設定為作用中：
   1. 返回至 [ **SAML 簽署憑證** ] 頁面。
   1. 在新儲存的憑證資料列中，選取省略號 (**...**) 然後選取 [ **讓憑證變成**使用中]。
   1. 略過接下來的兩個步驟。

1. 如果應用程式一次只能處理一個憑證，請挑選停機時間間隔以執行下一個步驟。  (否則，如果應用程式不會自動挑選新的憑證，但可以處理一個以上的簽署憑證，您可以隨時執行下一個步驟。 ) 
1. 在舊憑證到期之前，請依照先前的 [上傳和啟用憑證](#upload-and-activate-a-certificate) 一節中的指示進行。
1. 請登入應用程式，以確定憑證正常運作。

## <a name="related-articles"></a>相關文章

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](../saas-apps/tutorial-list.md)
- [使用 Azure Active Directory 的應用程式管理](what-is-application-management.md)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
