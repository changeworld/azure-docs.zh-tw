---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco Webex 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco Webex 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4fd6b46353cdeb4493c1a767c92e75d602e52843
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528848"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco Webex 整合

在本教學課程中，您將了解如何整合 Cisco Webex 與 Azure Active Directory (Azure AD)。 在整合 Cisco Webex 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Cisco Webex 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Cisco Webex。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Cisco Webex 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Cisco Webex 支援由 **SP** 起始的 SSO。
* Cisco Webex 支援**自動**使用者佈建。
* 設定 Cisco Webex 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>從資源庫新增 Cisco Webex

若要設定將 Cisco Webex 整合到 Azure AD 中，您需要將 Cisco Webex 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Cisco Webex**。
1. 從結果面板中選取 [Cisco Webex]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>設定和測試 Cisco Webex 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Cisco Webex 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Cisco Webex 中相關使用者之間的連結關聯性。

若要設定及測試與 Cisco Webex 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Cisco Webex](#configure-cisco-webex)** ，以在應用程式端設定 SSO 設定。
    1. **[建立 Cisco Webex 測試使用者](#create-cisco-webex-test-user)** ，使 Cisco Webex 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cisco Webex]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟，以上傳已下載的**服務提供者中繼資料**檔案並設定應用程式：

    >[!Note]
    >您將會從 [設定 Cisco Webex]  區段取得服務提供者中繼資料檔案，本教學課程稍後會說明。 

    a. 按一下 [上傳中繼資料檔案]  。

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    c. 成功完成上傳服務提供者中繼資料檔案之後，[基本 SAML 組態]  區段中會自動填入 [識別碼]  和 [回覆 URL]  值：

    在 [登入 URL]  文字方塊中，貼上透過 SP 中繼資料檔案上傳而自動填入的 [回覆 URL]  值。

1. Cisco Webex 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 SAML 權杖屬性設定中。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Cisco Webex 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
  
    | 名稱 |  來源屬性|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Cisco Webex]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Cisco Webex 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Cisco Webex]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-cisco-webex"></a>設定 Cisco Webex

1. 若要自動執行 Cisco Webex 內的設定，您必須按一下 [安裝擴充功能]  來安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Cisco Webex]  便會將您導向到 Cisco Webex 應用程式。 請從該處提供用以登入 Cisco Webex 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 8。

    ![設定組態](common/setup-sso.png)

3. 如果您要手動設定 Cisco Webex，請使用完整系統管理員認證登入 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

4. 選取 [設定]  ，然後按一下 [驗證]  區段下方的 [修改]  。

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. 選取 [整合協力廠商識別提供者  (進階)]，然後移至下一個畫面。

6. 在 [匯入 Idp 中繼資料]  頁面上，將 Azure AD 中繼資料檔案拖放到頁面，或使用檔案瀏覽器選項來找到並上傳 Azure AD 中繼資料檔案。 然後，選取 [需要中繼資料中憑證授權單位所簽署的憑證 (較安全)]  ，然後按 [下一步]  。

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. 選取 [測試 SSO 連接]  ，並在新的瀏覽器索引標籤開啟時，透過登入向 Azure AD 進行驗證。

8. 返回 [Cisco Cloud Collaboration Management]  瀏覽器索引標籤。如果測試成功，請選取 [這項測試成功。  啟用單一登入] 選項，然後按 [下一步]  。

### <a name="create-cisco-webex-test-user"></a>建立 Cisco Webex 測試使用者

在本節中，您會在 Cisco Webex 中建立名為 B.Simon 的使用者。 在本節中，您會在 Cisco Webex 中建立名為 B.Simon 的使用者。

1. 使用完整系統管理員認證，移至 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

2. 按一下 [使用者]  ，然後按一下 [管理使用者]  。
   
    ![設定單一登入](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. 在 [管理使用者]  視窗中，選取 [手動新增或修改使用者]  ，然後按 [下一步]  。

4. 選取 [名稱和電子郵件地址]  。 然後，如下填寫文字方塊︰

    ![設定單一登入](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. 在 [名字]  文字方塊中，輸入使用者的名字，例如 **B**。

    b. 在 [姓氏]  文字方塊中，輸入使用者的姓氏，例如 **Simon**。

    c. 在 [電子郵件地址]  文字方塊中，輸入像是 b.simon@contoso.com 的使用者電子郵件地址。

5. 按一下加號以新增 B.Simon。 然後按 [下一步]  。

6. 在 [新增使用者的服務]  視窗中，按一下 [儲存]  ，然後按一下 [完成]  。

## <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Cisco Webex] 圖格時，應該會自動登入您已設定 SSO 的 Cisco Webex。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試 Cisco Webex 搭配 Azure AD](https://aad.portal.azure.com)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Cisco Webex](https://docs.microsoft.com/cloud-app-security/protect-webex)