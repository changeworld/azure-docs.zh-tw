---
title: 教學課程：Azure Active Directory 與 Qlik Sense Enterprise 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Qlik Sense Enterprise 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b878c9e26a1f1f0e1e71e79d8f52b81f12924ef3
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968399"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>教學課程：整合 Qlik Sense Enterprise 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Qlik Sense Enterprise 與 Azure Active Directory (Azure AD)。 在整合 Qlik Sense Enterprise 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 Qlik Sense Enterprise 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Qlik Sense Enterprise。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Qlik Sense Enterprise (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 
* Qlik Sense Enterprise 支援由 **SP** 起始的 SSO。
* Qlik Sense Enterprise 支援 **Just-in-Time 佈建**

* 設定 Qlik Sense Enterprise 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>從資源庫新增 Qlik Sense Enterprise

若要設定將 Qlik Sense Enterprise 整合到 Azure AD 中，您需要從資源庫將 Qlik Sense Enterprise 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Qlik Sense Enterprise**。
1. 從結果面板中選取 [Qlik Sense Enterprise]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 Qlik Sense Enterprise 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Qlik Sense Enterprise 中相關使用者之間的連結關聯性。

若要設定及測試與 Qlik Sense Enterprise 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Qlik Sense Enterprise 測試使用者](#create-qlik-sense-enterprise-test-user)** - 使 Qlik Sense Enterprise 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Qlik Sense Enterprise]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL︰ `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. 在 [識別碼]  文字方塊中，使用下列其中一種模式來輸入 URL：

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. 在 [回覆 URL]  文字方塊中，以下列模式輸入 URL：

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」、「識別碼」和「回覆 URL」來更新這些值 (本教學課程稍後會說明)，或連絡 [Qlik Sense Enterprise 用戶端支援小組](https://www.qlik.com/us/services/support)以取得這些值。 URL 的預設連接埠是 443，但您可以根據組織的需求加以自訂。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，依據您的需求從指定選項中找出 [同盟中繼資料 XML]  ，並將其儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Qlik Sense Enterprise 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Qlik Sense Enterprise]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-qlik-sense-enterprise-sso"></a>設定 Qlik Sense Enterprise SSO

1. 準備同盟中繼資料 XML 檔案，以便將該檔案上傳至 Qlik Sense 伺服器。

    > [!NOTE]
    > 將 IdP 中繼資料上傳至 Qlik Sense 伺服器之前，必須先編輯此檔案才能移除資訊，以確保 Azure AD 與 Qlik Sense 伺服器之間的運作正常。

    ![QlikSense][qs24]

    a. 在文字編輯器中開啟從 Azure 入口網站下載的 FederationMetaData.xml 檔案。

    b. 搜尋 **RoleDescriptor** 值。  共會有四個項目 (兩對開頭和結尾元素標籤)。

    c. 從檔案中刪除 RoleDescriptor 標籤與其間的所有資訊。

    d. 將此檔案存放在附近，以便稍後用於本文件。

2. 以可建立虛擬 Proxy 組態的使用者身分，瀏覽至 Qlik Sense Qlik 管理主控台 (QMC)。

3. 在 QMC 中，按一下 [虛擬Proxy]  功能表項目。

    ![QlikSense][qs6]

4. 按一下畫面底部的 [新建]  按鈕。

    ![QlikSense][qs7]

5. 虛擬 Proxy 編輯畫面隨即出現。  畫面右邊的功能表會顯示組態選項。

    ![QlikSense][qs9]

6. 勾選 [識別] 功能表選項後，輸入 Azure 虛擬 Proxy 組態的識別資訊。

    ![QlikSense][qs8]  

    a. [說明]  欄位是虛擬 Proxy 組態的易記名稱。  輸入說明的值。

    b. [前置詞]  欄位可識別虛擬 Proxy 端點，以便連接到採用 Azure AD 單一登入的 Qlik Sense。  輸入此虛擬 Proxy 的獨特前置詞名稱。

    c. [工作階段閒置逾時 (分鐘)]  是指透過此虛擬 Proxy 連接的逾時值。

    d. [工作階段 cookie 標頭名稱]  是 cookie 名稱，用於儲存使用者在成功驗證後收到的 Qlik Sense 工作階段識別碼。  這個名稱必須是唯一的。

7. 按一下 [驗證] 功能表選項，讓它立即可見。  [驗證] 畫面隨即出現。

    ![QlikSense][qs10]

    a. [匿名存取模式]  下拉式清單可讓您決定匿名使用者是否可透過虛擬 Proxy 存取 Qlik Sense。  預設選項是 [沒有匿名使用者]。

    b. [驗證方法]  下拉式清單可讓您決定虛擬 Proxy 將使用的驗證配置。  從下拉式清單選取 [SAML]。  此時會顯示更多選項。

    c. 在 [SAML 主機 URI]  欄位中，輸入使用者必須輸入才可透過此 SAML 虛擬 Proxy 存取 Qlik Sense 的主機名稱。  主機名稱是 Qlik Sense 伺服器的 URI。

    d. 在 [SAML 實體識別碼]  中，輸入與 [SAML 主機 URI] 欄位相同的值。

    e. [SAML IdP 中繼資料]  是稍早在 [從 Azure AD 組態編輯同盟中繼資料]  區段中編輯的檔案。  **上傳 IdP 中繼資料之前，必須先編輯此檔案**才能移除資訊，以確保 Azure AD 與 Qlik Sense 伺服器之間的運作正常。  **如果檔案尚未進行編輯，請參閱上述指示。**  如果檔案已經過編輯，請按一下 [瀏覽] 按鈕，然後選取已編輯的中繼資料檔案，將它上傳至虛擬 Proxy 組態。

    f. 輸入 SAML 屬性的屬性名稱或結構描述參考，代表 Azure AD 將傳送至 Qlik Sense 伺服器的 **UserID**。  在設定後的 Azure 應用程式畫面中可取得結構描述參考資訊。  若要使用名稱屬性，請輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    g. 輸入**使用者目錄**的值，此值會在使用者透過 Azure AD 向 Qlik Sense 伺服器進行驗證時附加至使用者。  硬式編碼值必須以**方括號 []** 括住。  若要使用 Azure AD SAML 判斷提示中傳送的屬性，請在此文字方塊中輸入屬性名稱 (**不需**方括號)。

    h. [SAML 簽章演算法]  可設定虛擬 Proxy 組態的服務提供者 (在此例中是 Qlik Sense 伺服器) 憑證簽署。  如果 Qlik Sense 伺服器使用透過 Microsoft Enhanced RSA 和 AES 密碼編譯提供者產生的受信任憑證，請將 SAML 簽署演算法變更為 **SHA-256**。

    i. [SAML 屬性對應] 區段可讓其他屬性 (如群組) 傳送到 Qlik Sense，以便用於安全性規則。

8. 按一下 [負載平衡]  功能表選項，讓它立即可見。  [負載平衡] 畫面隨即出現。

    ![QlikSense][qs11]

9. 按一下 [新增伺服器節點]  按鈕，選取 Qlik Sense 為了達到負載平衡而會將工作階段傳送至的引擎節點，然後按一下 [新增]  按鈕。

    ![QlikSense][qs12]

10. 按一下 [進階] 功能表選項，讓它立即可見。 [進階] 畫面隨即出現。

    ![QlikSense][qs13]

    [主機允許清單] 會識別在連線至 Qlik Sense 伺服器時所接受的主機名稱。  **輸入連接到 Qlik Sense 伺服器時使用者會指定的主機名稱。** 主機名稱是與 SAML 主機 uri 相同的值 (不含 https://)。

11. 按一下 [套用]  按鈕。

    ![QlikSense][qs14]

12. 按一下 [確定] 以接受警告訊息，該訊息指出將重新啟動連結至虛擬 Proxy 的 Proxy。

    ![QlikSense][qs15]

13. 畫面右側會出現 [相關聯的項目] 功能表。  按一下 [Proxy]  功能表選項。

    ![QlikSense][qs16]

14. Proxy 畫面隨即出現。  按一下底部的 [連結]  按鈕，將 Proxy 連結到虛擬 Proxy。

    ![QlikSense][qs17]

15. 選取將會支援此虛擬 Proxy 連線的 Proxy 節點，然後按一下 [連結]  按鈕。  連結之後，Proxy 會列在相關聯的 Proxy 之下。

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. 大約五到十秒後，[重新整理 QMC] 訊息會出現。  按一下 [重新整理 QMC]  按鈕。

    ![QlikSense][qs20]

17. 當 QMC 重新整理時，按一下 [虛擬 Proxy]  功能表項目。 新的 SAML 虛擬 Proxy 項目會列在螢幕上的表格中。  按一下此虛擬 Proxy 項目。

    ![QlikSense][qs51]

18. 畫面底部的 [下載 SP 中繼資料] 按鈕將會啟用。  按一下 [下載 SP 中繼資料]  按鈕，將中繼資料儲存至檔案。

    ![QlikSense][qs52]

19. 開啟 SP 中繼資料檔案。  觀察 **entityID** 項目和 **AssertionConsumerService** 項目。  這些值相當於 Azure AD 應用程式組態中的 [識別碼]  、[登入 URL]  和 [回覆 URL]  。 如果這些值不相符，請將其貼在 Azure AD 應用程式組態中的 [Qlik Sense Enterprise 網域與 URL]  區段，然後您應該在 Azure AD App 組態精靈中取代這些值。

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>建立 Qlik Sense Enterprise 測試使用者

Qlik Sense Enterprise 支援 **Just-in-Time 佈建**，使用者會在使用 SSO 功能時自動新增至 Qlik Sense Enterprise 的「使用者」存放庫。 此外，用戶端可以使用 QMC 並建立 UDC (使用者目錄連接器)，從其選擇的 LDAP (例如 Active Directory 和其他) 預先填入 Qlik Sense Enterprise 中的使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Qlik Sense Enterprise] 圖格時，應該會自動登入您已設定 SSO 的 Qlik Sense Enterprise。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
