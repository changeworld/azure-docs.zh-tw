---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 iProva 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 iProva 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552831"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 iProva 整合

在本教學課程中，您將了解如何整合 iProva 與 Azure Active Directory (Azure AD)。 在整合 iProva 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 iProva 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 iProva。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 iProva 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* iProva 支援 **SP** 起始的 SSO

* 設定 iProva 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-iprova-from-the-gallery"></a>從資源庫新增 iProva

若要進行將 iProva 整合到 Azure AD 中的設定，您必須從資源庫將 iProva 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **iProva**。
1. 從結果面板中選取 [iProva]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>設定及測試 iProva 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 iProva 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 iProva 中相關使用者之間的連結關聯性。

若要設定及測試與 iProva 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[從 iProva 擷取組態資訊](#retrieve-configuration-information-from-iprova)** - 以準備執行後續步驟。
1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[建立 iProva 測試使用者](#create-iprova-test-user)** - 使 iProva 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[設定 iProva SSO](#configure-iprova-sso)** - 在應用程式端設定單一登入設定。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="retrieve-configuration-information-from-iprova"></a>從 iProva 擷取組態資訊

在本節中，您會從 iProva 擷取資訊，以設定 Azure AD 單一登入。

1. 開啟網頁瀏覽器，並使用下列 URL 模式移至 iProva 中的 [SAML2 資訊]  頁面：

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![檢視 iProva SAML2 資訊頁面](media/iprova-tutorial/iprova-saml2-info.png)

1. 當您在另一個瀏覽器索引標籤中繼續進行後續步驟時，請讓此瀏覽器索引標籤保持開啟。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [iProva]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 使用 [iProva SAML2 資訊]  頁面上的 [登入 URL]  標籤後面所顯示的值，填入 [登入 URL]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

    b. 使用 [iProva SAML2 資訊]  頁面上的 [EntityID]  標籤後面所顯示的值，填入 [識別碼]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

    c. 使用 [iProva SAML2 資訊]  頁面上的 [回覆 URL]  標籤後面所顯示的值，填入 [回覆 URL]  方塊。 此頁面仍在您的另一個瀏覽器索引標籤中開啟。

1. iProva 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，iProva 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性| 命名空間  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

## <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 iProva 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取[iProva]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="create-iprova-test-user"></a>建立 iProva 測試使用者

1. 使用**系統管理員**帳戶登入 iProva。

2. 開啟 [移至]  功能表。

3. 選取 [應用程式管理]  。

4. 選取 [使用者和使用者群組]  面板中的 [使用者]  。

5. 選取 [新增]  。

6. 在 [使用者名稱]  方塊中，輸入使用者的使用者名稱，例如 `B.Simon@contoso.com`。

7. 在 [全名]  方塊中，輸入使用者的全名，例如 **B.Simon**。

8. 選取 [無密碼 (使用單一登入)]  選項。

9. 在 [電子郵件地址]  方塊中，輸入使用者的電子郵件地址，例如 `B.Simon@contoso.com`。

10. 向下捲動至頁面結尾處，然後選取 [完成]  。

## <a name="configure-iprova-sso"></a>設定 iProva SSO

1. 使用**系統管理員**帳戶登入 iProva。

2. 開啟 [移至]  功能表。

3. 選取 [應用程式管理]  。

4. 選取 [系統設定]  面板中的 [一般]  。

5. 選取 [編輯]  。

6. 向下捲動至 [存取控制]  。

    ![iProva 存取控制設定](media/iprova-tutorial/iprova-accesscontrol.png)

7. 找出 [使用者會使用其網路帳戶自動登入]  設定，並將其變更為 [是，透過 SAML 進行驗證]  。 此時會出現其他選項。

8. 選取 [設定]  。

9. 選取 [下一步]  。

10. iProva 此時會詢問您是要從 URL 下載同盟資料，還是將從檔案加以上傳。 請選取 [從 URL]  選項。

    ![下載 Azure AD 中繼資料](media/iprova-tutorial/iprova-download-metadata.png)

11. 貼上您在 [設定 Azure AD 單一登入] 區段的最後一個步驟中儲存的中繼資料 URL。

12. 選取箭號按鈕，從 Azure AD 下載中繼資料。

13. 下載完成後，便會出現確認訊息**已下載有效的同盟資料檔案**。

14. 選取 [下一步]  。

15. 暫且略過 [測試登入]  選項，然後選取 [下一步]  。

16. 在 [要使用的宣告]  下拉式方塊中，選取 [windowsaccountname]  。

17. 選取 [完成]  。

18. 現在您會回到 [編輯一般設定]  畫面。 向下捲動至頁面底部，然後選取 [確定]  以儲存您的設定。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [iProva] 圖格時，應該會自動登入您已設定 SSO 的 iProva。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 iProva](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 iProva](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)