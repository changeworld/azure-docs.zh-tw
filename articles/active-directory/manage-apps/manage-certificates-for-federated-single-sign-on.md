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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159024"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理同盟單一登入的憑證

在本文中，我們將討論與憑證相關的常見問題和資訊，Azure Active Directory （Azure AD）建立，以便為您的軟體即服務（SaaS）應用程式建立同盟單一登入（SSO）。 從 Azure AD 應用程式資源庫，或使用非資源庫的應用程式範本新增應用程式。 使用同盟 SSO 選項以設定應用程式。

本文只與設定為透過[安全性聲明標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML）同盟使用 Azure AD SSO 的應用程式相關。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>為資源庫和非資源庫應用程式自動產生的憑證

當您從資源庫新增應用程式，並設定 SAML 型登入（從 [應用程式總覽] 頁面選取 [**單一登入**] > **saml** ）時，Azure AD 會為應用程式產生一個有效期限為三年的憑證。 若要下載作用中的憑證做為安全性憑證（ **.cer**）檔案，請回到該頁面（以**saml 為基礎**的登入），然後選取 [ **saml 簽署憑證**] 標題中的下載連結。 您可以在原始（二進位）憑證或 Base64 （base 64 編碼文字）憑證之間做選擇。 針對資源庫應用程式，此區段可能也會顯示一個連結，將憑證下載為同盟中繼資料 XML **（.xml 檔案**），視應用程式的需求而定。

![SAML active 簽署憑證下載選項](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

您也可以選取 [ **Saml 簽署憑證**] 標題的 [**編輯**] 圖示（鉛筆）來下載作用中或非使用中的憑證，這會顯示 [ **saml 簽署憑證**] 頁面。 選取您要下載之憑證旁的省略號（ **...** ），然後選擇您想要的憑證格式。 您可以使用其他選項，以隱私權增強的郵件（PEM）格式下載憑證。 此格式與 Base64 相同，但副檔名為**pem** ，Windows 中無法辨識為憑證格式。

![SAML 簽署憑證下載選項（作用中和非作用中）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自訂同盟憑證的到期日期及變換新的憑證

根據預設，Azure 會將憑證設定為在 SAML 單一登入設定期間自動建立後的三年後到期。 因為您在儲存憑證後無法變更它的日期，所以您必須：

1. 建立具有所需日期的新憑證。
1. 儲存新的憑證。
1. 以正確的格式下載新的憑證。
1. 將新的憑證上傳至應用程式。
1. 在 Azure Active Directory 入口網站中啟用新憑證。

下列兩節可協助您執行這些步驟。

### <a name="create-a-new-certificate"></a>建立新的憑證

首先，建立並儲存具有不同到期日的新憑證：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [ **Azure Active Directory 系統管理中心**] 頁面隨即出現。
1. 在左側窗格中，選取 [企業應用程式]。 您帳戶中的企業應用程式清單隨即出現。
1. 選取受影響的應用程式。 應用程式的 [總覽] 頁面隨即出現。
1. 在 [應用程式總覽] 頁面的左窗格中，選取 [**單一登入**]。
1. 如果出現 [**選取單一登入方法**] 頁面，請選取 [ **SAML**]。
1. 在 [以**SAML-Preview 設定單一登入**] 頁面中，尋找 [ **saml 簽署憑證**] 標題，然後選取 [**編輯**] 圖示（鉛筆）。 [ **SAML 簽署憑證**] 頁面隨即出現，其中顯示每個憑證的狀態（作用中或**非**作用中）、到期日和**指紋（哈**希字串）。
1. 選取 [**新增憑證**]。 [憑證] 清單下方會出現新的資料列，其中到期日預設為目前日期後的三年。 （尚未儲存您的變更，因此您仍可修改到期日）。
1. 在 [新憑證] 列中，將滑鼠停留在 [到期日] 資料行上，然後選取 [**選取日期**] 圖示（行事曆）。 行事曆控制項隨即出現，其中顯示新資料列目前到期日的月份天數。
1. 使用行事曆控制項來設定新的日期。 您可以設定目前日期與目前日期後三年之間的任何日期。
1. 選取 [儲存]。 新憑證現在會顯示為 [**非**作用中] 狀態、您選擇的到期日和 [指紋]。
1. 選取**X**以返回 [以**SAML-Preview 設定單一登入**] 頁面。

### <a name="upload-and-activate-a-certificate"></a>上傳並啟動憑證

接下來，以正確的格式下載新的憑證，並將它上傳至應用程式，並使其在 Azure Active Directory 中生效：

1. 透過下列其中一種方式來查看應用程式的其他 SAML 登入設定指示：

   - 選取設定**指南**連結以在另一個瀏覽器視窗或索引標籤中進行查看，或
   - 移至 [**設定**] 標題，然後選取 [ **view] （逐步指示）** 以在提要欄位中查看。

1. 在指示中，請注意憑證上傳所需的編碼格式。
1. 請遵循稍早[針對圖庫和非資源庫應用程式自動產生的憑證](#auto-generated-certificate-for-gallery-and-non-gallery-applications)一節中的指示。 此步驟會以應用程式上傳所需的編碼格式下載憑證。
1. 當您想要變換至新憑證時，請回到 [ **SAML 簽署憑證**] 頁面，然後在新儲存的憑證資料列中，選取省略號（ **...** ），然後選取 [**讓憑證成為**使用中]。 新憑證的狀態會變更為 [**作用中]** ，而先前的作用中憑證會變更為 [**非**作用中] 狀態。
1. 繼續遵循您稍早顯示的應用程式 SAML 登入設定指示，讓您能夠以正確的編碼格式上傳 SAML 簽署憑證。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>新增憑證到期的電子郵件通知位址

Azure AD 會在 SAML 憑證到期前，傳送電子郵件通知60、30和7天。 您可以新增一個以上的電子郵件地址來接收通知。 若要指定您想要傳送通知的電子郵件地址：

1. 在 [ **SAML 簽署憑證**] 頁面中，移至 [**通知電子郵件地址**] 標題。 根據預設，此標題只會使用新增應用程式之系統管理員的電子郵件地址。
1. 在最後的電子郵件地址底下，輸入應接收憑證到期通知的電子郵件地址，然後按 Enter。
1. 針對您想要新增的每個電子郵件地址，重複上一個步驟。
1. 針對您想要刪除的每個電子郵件地址，選取電子郵件地址旁的 [**刪除**] 圖示（「垃圾可以」）。
1. 選取 [儲存]。

您會收到來自 aadnotification@microsoft.com 的通知電子郵件。 若要避免電子郵件傳送到您的垃圾郵件位置，請將此電子郵件新增至您的連絡人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>更新即將到期的憑證

如果憑證即將到期，您可以使用導致使用者不會嚴重停機的程式來加以更新。 若要更新到期的憑證：

1. 請遵循稍早[建立新憑證](#create-a-new-certificate)一節中的指示，使用與現有憑證重迭的日期。 該日期會限制憑證到期所造成的停機時間。
1. 如果應用程式可以自動變換憑證，請遵循下列步驟，將新憑證設定為作用中：
   1. 返回 [ **SAML 簽署憑證**] 頁面。
   1. 在新儲存的憑證資料列中，選取省略號（ **...** ），然後選取 [**讓憑證成為**使用中]。
   1. 略過接下來的兩個步驟。

1. 如果應用程式一次只能處理一個憑證，請挑選停機時間間隔以執行下一個步驟。 （否則，如果應用程式不會自動挑選新憑證，但是可以處理一個以上的簽署憑證，您可以隨時執行下一個步驟）。
1. 在舊憑證到期之前，請遵循先前的[上傳和啟動憑證](#upload-and-activate-a-certificate)一節中的指示。
1. 登入應用程式以確定憑證運作正常。

## <a name="related-articles"></a>相關文章

- [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](../saas-apps/tutorial-list.md)
- [Azure Active Directory 的應用程式管理](what-is-application-management.md)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [在 Azure Active Directory 中，對應用程式的 SAML 型單一登入進行 Debug](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
