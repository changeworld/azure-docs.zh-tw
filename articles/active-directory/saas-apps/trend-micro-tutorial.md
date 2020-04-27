---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Trend Micro Web Security (TMWS) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Trend Micro Web Security (TMWS) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083320"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Trend Micro Web Security (TMWS) 整合

在本教學課程中，您將了解如何整合 Trend Micro Web Security (TMWS) 與 Azure Active Directory (Azure AD)。 在整合 Trend Micro Web Security (TMWS) 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Trend Micro Web Security (TMWS) 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Trend Micro Web Security (TMWS)。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Trend Micro Web Security (TMWS) 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Trend Micro Web Security (TMWS) 支援 **SP** 起始的 SSO
* 設定 Trend Micro Web Security (TMWS) 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>從資源庫新增 Trend Micro Web Security (TMWS)

若要設定將 Trend Micro Web Security (TMWS) 整合到 Azure AD 中，您需要從資源庫將 Trend Micro Web Security (TMWS) 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Trend Micro Web Security (TMWS)** 。
1. 從結果面板選取 [Trend Micro Web Security (TMWS)]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>設定及測試 Trend Micro Web Security (TMWS) 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Trend Micro Web Security (TMWS) 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Trend Micro Web Security (TMWS) 中相關使用者之間的連結關聯性。

若要設定及測試與 Trend Micro Web Security (TMWS) 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
    1. **[在 Azure AD 中設定使用者和群組同步處理設定](#configure-user-and-group-synchronization-settings-in-azure-ad)** - 在 Azure AD 中設定使用者和群組同步處理設定
1. **[設定 Trend Micro Web Security (TMWS) SSO](#configure-trend-micro-web-security-sso)** - 在應用程式端設定單一登入設定。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Trend Micro Web Security (TMWS)]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. 在 [回覆 URL]  文字方塊中，鍵入 URL：`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 識別碼值不是實際值。 請使用實際的「識別碼」來更新此值。 您可以從 [系統管理] > [目錄服務]  ，在 Azure AD 的 [驗證方法]  畫面上的 [Azure 系統管理入口網站的服務提供者設定]  區域之下取得這些值。

1. Trend Micro Web Security (TMWS) 應用程式需要特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Trend Micro Web Security (TMWS) 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。
    
    | 名稱 | 來源屬性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Trend Micro Web Security (TMWS)]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Trend Micro Web Security (TMWS) 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Trend Micro Web Security (TMWS)]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>在 Azure AD 中設定使用者和群組同步處理設定

1. 在左側導覽列中，按一下 [Azure Active Directory]  。

1. 在 [管理]  底下按一下 [應用程式註冊]  ，然後在 [所有應用程式]  區域底下按一下您的新企業應用程式。

1. 在 [管理]  下，按一下 [憑證和密碼]  。

1. 在出現的 [用戶端密碼] 區域底下，按一下 [新增用戶端密碼]  。

1. 在出現的 [新增用戶端密碼] 畫面上，選擇性地新增描述並選取此用戶端密碼的到期期間，然後按一下 [新增]  。 剛新增的用戶端密碼就會出現在 [用戶端密碼] 區域底下。

1. 記下該值。 之後，您會在 TMWS 中輸入此資訊。

1. 在 [管理]  底下，按一下 [API 權限]  。 

1. 在出現的 [API 權限] 畫面上，按一下 [新增權限]  。

1. 在所出現 [要求 API 權限] 畫面的 [Microsoft API] 索引標籤上，依序按一下 [Microsoft Graph]  和 [應用程式權限]  。

1. 找出並新增下列權限： 

    * Group.Read.All
    * User.Read.All

1. 按一下 [新增權限]  。 此時會出現訊息來確認系統已成功儲存您的設定。 剛新增的權限會出現在 [API 權限] 畫面上。

1. 在 [授與同意] 區域底下，按一下 [授與管理員同意 (您的系統管理員帳戶) > (預設目錄)]  ，然後按一下 [是]  。 此時會出現訊息來確認您已成功授與所要求權限的管理員同意。

1. 按一下 [概觀]  。 

1. 在出現的右窗格中，記下應用程式 (用戶端) 識別碼和目錄 (租用戶) 識別碼。 之後，您會在 TMWS 中輸入此資訊。 您也可以按一下 [Azure Active Directory] > [管理]  底下的 [自訂網域名稱]  ，然後記下右窗格中的網域名稱。

## <a name="configure-trend-micro-web-security-sso"></a>設定 Trend Micro Web Security SSO

1. 登入 TMWS 管理主控台，然後移至 [系統管理]   > [使用者與驗證]   > [目錄服務]  。

1. 按一下畫面上方區域中的這裡。

1. 在出現的 [驗證方法] 畫面上，按一下 [Azure AD]  。

1. 按一下[開啟]  或 [關閉]  以決定是否要允許貴組織的 AD 使用者透過 TMWS 瀏覽網站 (如果其資料未同步處理到 TMWS 的話)。

    > [!NOTE]
    > 未從 Azure AD 同步處理的使用者只能透過已知的 TMWS 閘道或貴組織的專用連接埠進行驗證。

1. 在 [識別提供者設定]  區段上，執行下列步驟：

    a. 在 [服務 URL]  欄位中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 在 [登入名稱屬性]  欄位中，貼上使用者宣告名稱與來自 Azure 入口網站的 **user.onpremisessamaccountname** 來源屬性。

    c. 在 [公用 SSL 憑證]  欄位中，使用從 Azure 入口網站下載的 [憑證 (Base64)]  。

1. 在 [同步處理設定]  區段上，執行下列步驟：

    a. 在 [租用戶]  欄位中，使用 [目錄 (租用戶) 識別碼]  或來自 Azure 入口網站的 [自訂網域名稱]  值。

    b. 在 [應用程式識別碼]  欄位中，使用來自 Azure 入口網站的 [應用程式 (用戶端) 識別碼]  值。

    c. 在 [用戶端秘密]  欄位中，使用來自 Azure 入口網站的 [用戶端秘密]  。

    d. 在 [同步處理排程]  欄位中，選取要以手動方式或根據排程來與 Azure AD 進行同步處理。 如果您選擇 [手動]，每當 Active Directory 使用者資訊有變更時，請記得回到 [目錄服務] 畫面並執行手動同步處理，讓 TMWS 中的資訊保持最新狀態。

    e. 按一下 [測試連接]  ，檢查是否可以成功連線 Azure AD 服務。 
    
    f. 按一下 [檔案]  。
 
 > [!NOTE]
 > 如需有關如何使用 Azure AD 設定 Trend Micro Web Security 的詳細資訊，請參閱[這份](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)文件。

## <a name="test-sso"></a>測試 SSO 

在成功設定 Azure AD 服務並指定 Azure AD 作為使用者驗證方法後，您就可以登入 TMWS Proxy 伺服器來確認您的設定。 在 Azure AD 登入確認您的帳戶後，您就可以造訪網際網路。

> [!NOTE]
> TMWS 不支援從 Azure AD 入口網站測試單一登入，請在 [概觀] > [單一登入] > [以 SAML 設定單一登入] > [測試您的新企業應用程式] 底下進行。

1. 清除瀏覽器的所有 Cookie，然後重新啟動瀏覽器。 

1. 將瀏覽器指向 TMWS Proxy 伺服器。 如需詳細資訊，請參閱[使用 PAC 檔案來轉送流量](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)。

1. 造訪任何網際網路網站。 TMWS 會將您導向至 TMWS 網頁驗證入口。

1. 指定 Active Directory 帳戶 (格式：domain\sAMAccountName 或 sAMAccountName@domain) 或電子郵件地址 (或 UPN)，然後按一下 [登入]  。 TMWS 會將您傳送至 Azure AD 登入。

1. 在 Azure AD 登入中，輸入您的 AD 帳號認證。 您應該就能成功登入 TMWS 了。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Trend Micro Web Security (TMWS)](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Trend Micro Web Security (TMWS)](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

