---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco Webex Meetings 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco Webex Meetings 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026260"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Cisco Webex Meetings 整合

在本教學課程中，您將了解如何整合 Cisco Webex Meetings 與 Azure Active Directory (Azure AD)。 在整合 Cisco Webex Meetings 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Cisco Webex Meetings 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Cisco Webex Meetings。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Cisco Webex Meetings 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Cisco Webex Meetings 支援由 **SP 和 IDP** 起始的 SSO

* Cisco Webex Meetings 支援 **Just In Time** 使用者佈建

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>從資源庫新增 Cisco Webex Meetings

若要設定將 Cisco Webex Meetings 整合到 Azure AD 中，您必須從資源庫將 Cisco Webex Meetings 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Cisco Webex Meetings**。
1. 從結果面板中選取 [Cisco Webex Meetings]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>設定和測試 Cisco Webex Meetings 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Cisco Webex Meetings 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Cisco Webex Meetings 中相關使用者之間的連結關聯性。

若要設定及測試與 Cisco Webex Meetings 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
2. **[設定 Cisco Webex Meetings SSO](#configure-cisco-webex-meetings-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Cisco Webex Meetings 測試使用者](#create-cisco-webex-meetings-test-user)** - 在 Cisco Webex Meetings 中建立一個與 Azure AD 中代表 B.Simon 之使用者連結的 Britta Simon 對應項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cisco Webex Meetings]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，您可以藉由上傳**服務提供者中繼資料**檔案，在 **IDP** 起始模式中設定應用程式，如下所示：

    a. 按一下 [上傳中繼資料檔案]  。

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    c. 成功完成上傳服務提供者中繼資料檔案之後，[基本 SAML 組態]  區段中會自動填入 [識別碼]  和 [回覆 URL]  值。

    >[!Note]
    >您會在本教學課程稍後說明的＜設定 Cisco Webex Meetings SSO＞  一節中取得「服務提供者中繼資料」檔案。 

1. 如果您想要在 **SP** 起始模式中設定應用程式，請執行下列步驟：  

    a. 在 [基本 SAML 設定]  區段內，按一下編輯/鉛筆圖示。

   ![編輯基本 SAML 組態](common/edit-urls.png)
    
    b. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL：` https://<customername>.my.webex.com`

5. Cisco Webex Meetings 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示，以開啟 [使用者屬性] 對話方塊。

    ![image](common/edit-attribute.png)

6. 除了以上屬性外，Cisco Webex Meetings 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示： 

    | 名稱 | 來源屬性|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   電子郵件       | user.mail |
    |   uid    | user.mail |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，從下拉式清單中選取針對該資料列顯示的屬性值。

    f. 按一下 [檔案]  。

4. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [安裝 Cisco Webex Meetings]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Cisco Webex Meetings 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Cisco Webex Meetings]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-cisco-webex-meetings-sso"></a>設定 Cisco Webex Meetings SSO

1. 使用您的系統管理認證移至 `https://<customername>.webex.com/admin` URL。

2. 移至 [一般網站設定]  ，然後瀏覽至 [SSO 設定]  。
 
    ![設定單一登入](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. 在 [Webex 系統管理]  頁面上，執行下列步驟：

    ![設定單一登入](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. 選取 [SAML 2.0]  作為 [同盟通訊協定]  。

    b. 按一下 [Import SAML Metadata] \(匯入 SAML 中繼資料\)  連結，以上傳從 Azure 入口網站下載的中繼資料檔案。

    c. 按一下 [Export] \(匯出\)  按鈕以下載「服務提供者中繼資料」檔案，然後在 Azure 入口網站的 [基本 SAML 設定]  區段中上傳此檔案。

    d. 在 [AuthContextClassRef]  文字方塊中，輸入 `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`，如果您想要使用 Azure AD 來啟用 MFA，請輸入像 `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` 這兩個值

    e. 選取 [Auto Account Creation] \(自動建立帳戶\)  。

    >[!NOTE]
    >若要啟用 **Just-In-Time** 使用者佈建，您必須勾選 [Auto Account Creation] \(自動建立帳戶\)  。 此外，還必須在 SAML 回應中傳遞 SAML 權杖屬性。

    f. 按一下 [檔案]  。

    >[!NOTE]
    >這項設定僅適用於以電子郵件格式使用 Webex UserID 的客戶。

### <a name="create-cisco-webex-meetings-test-user"></a>建立 Cisco Webex Meetings 測試使用者

本節的目標是要在 Cisco Webex Meetings 中建立一個名為 B.Simon 的使用者。 Cisco Webex Meetings 支援預設會啟用的 **Just-In-Time** 佈建。 在這一節沒有您需要進行的動作項目。 如果 Cisco Webex Meetings 中還沒有任何使用者存在，在您嘗試存取 Cisco Webex Meetings 時就會建立新的使用者。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Cisco Webex Meetings] 圖格時，應該會自動登入您已設定 SSO 的 Cisco Webex Meetings。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [使用 Azure AD 來試用 ServiceNow](https://aad.portal.azure.com)
