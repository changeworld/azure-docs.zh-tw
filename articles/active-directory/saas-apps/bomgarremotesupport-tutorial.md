---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 BeyondTrust 遠端支援整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 BeyondTrust 遠端支援之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74082104"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 BeyondTrust 遠端支援整合

在本教學課程中，您將了解如何整合 BeyondTrust 遠端支援與 Azure Active Directory (Azure AD)。 將 BeyondTrust 遠端支援與 Azure AD 整合後，您可以：

* 在 Azure AD 中控制可存取 BeyondTrust 遠端支援的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 BeyondTrust 遠端支援。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 BeyondTrust 遠端支援單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* BeyondTrust 遠端支援可支援由 **SP** 起始的 SSO
* BeyondTrust 遠端支援可支援 **Just In Time** 使用者佈建

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>從資源庫新增 BeyondTrust 遠端支援

若要設定將 BeyondTrust 遠端支援整合到 Azure AD 中，您需要從資源庫將 BeyondTrust 遠端支援新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **BeyondTrust 遠端支援**。
1. 從結果面板中選取 [BeyondTrust 遠端支援]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>設定及測試適用於 BeyondTrust 遠端支援的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 BeyondTrust 遠端支援搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 BeyondTrust 遠端支援中相關使用者之間的連結關聯性。

若要設定及測試與 BeyondTrust 遠端支援搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 BeyondTrust 遠端支援 SSO](#configure-beyondtrust-remote-support-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 BeyondTrust 遠端支援測試使用者](#create-beyondtrust-remote-support-test-user)** - 使 BeyondTrust 遠端支援中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [BeyondTrust 遠端支援]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<HOSTNAME>.bomgar.com/saml`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`https://<HOSTNAME>.bomgar.com`

    c. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 稍後會在教學課程中知道這些值的說明。

1. BeyondTrust 遠端支援應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML token 屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，BeyondTrust 遠端支援應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | 名稱 | user.userprincipalname |
    | 使用者名稱 | user.userprincipalname |
    | 群組 | user.groups |
    | 唯一的使用者識別碼 | user.userprincipalname |

    > [!NOTE]
    > 為 BeyondTrust 遠端支援應用程式指派 Azure AD 群組時，必須將 [宣告中傳回的群組] 選項從 None 修改為 SecurityGroup。 群組會以其物件識別碼的形式匯入應用程式中。 您可以藉由檢查 Azure Active Directory 介面中的屬性，來找到 Azure AD 群組的物件識別碼。 這需要將 Azure AD 群組參照至及指派至正確的群組原則。

1. 設定唯一的使用者識別碼時，此值必須設定為 NameID 格式：**持續性**。 我們要求將此識別碼設為「持續性」，是為了正確識別使用者，並將其與正確的原則產生關聯，以取得權限。 按一下 [編輯] 圖示以開啟 [使用者屬性與宣告]  對話方塊，以編輯唯一的使用者識別碼值。

1. 在 [管理宣告]  區段上，按一下 [選擇名稱識別碼格式]  ，並將值設定為 [持續性]  ，然後按一下 [儲存]  。

    ![使用者屬性與宣告](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 BeyondTrust 遠端支援]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 BeyondTrust 遠端支援的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [BeyondTrust 遠端支援]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-beyondtrust-remote-support-sso"></a>設定 BeyondTrust 遠端支援 SSO

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入 BeyondTrust 遠端支援。

1. 按一下 [狀態]  功能表，然後複製 [識別碼]  、[回覆 URL]  和 [登入 URL]  ，並在 Azure 入口網站的 [基本 SAML 設定]  區段上使用這些值。

    ![設定 BeyondTrust 遠端支援](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. 瀏覽至 `https://support.example.com/login` 上的 BeyondTrust 遠端支援/登入介面 (其中 **support.example.com** 是設備的主要主機名稱)，並使用您的系統管理認證進行驗證。

1. 瀏覽至 [使用者與安全性]   > [安全性提供者]  。

1. 在下拉式功能表中選取 [SAML]  ，然後按一下 [建立提供者]  按鈕。

1. 在 [識別提供者設定] 區段下，有一個可用來上傳身分識別提供者中繼資料的選項。 找到您從 Azure 入口網站下載的中繼資料 XML 檔案，然後按一下 [上傳]  按鈕。 **實體識別碼**、**單一登入服務 URL**和憑證會自動上傳，而 [通訊協定繫結]  必須變更為 **HTTP POST**。 請看下方的螢幕擷取畫面：

    ![設定 BeyondTrust 遠端支援](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>建立 BeyondTrust 遠端支援測試使用者

我們將在這裡設定使用者佈建設定。 此區段中使用的值將會從 Azure 入口網站中的**使用者屬性與宣告**區段中進行參照。 我們已將此值設定為建立時匯入的預設值，不過，您可以視需要自訂此值。

![建立使用者](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> 此實作不需要群組和電子郵件屬性。 如果使用 Azure AD 群組，並將其指派給 BeyondTrust 遠端支援群組原則來取得權限，則群組的物件識別碼必須在 Azure 入口網站中透過其屬性來進行參照，並且要放在 [可用群組] 區段中。 完成此作業之後，物件識別碼/AD 群組即可指派給群組原則來取得權限。

![建立使用者](./media/bomgarremotesupport-tutorial/config-user2.png)

![建立使用者](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> 或者，您也可以在 SAML2 安全性提供者上設定預設的群組原則。 藉由定義此選項，這會將群組原則中指定的權限指派給所有透過 SAML 進行驗證的使用者。 BeyondTrust 遠端支援/特殊權限的遠端存取中包含權限有限的「一般成員」原則，可用來測試驗證並將使用者指派給正確的原則。 在第一次驗證嘗試成功之前，使用者不會透過 /login > [使用者與安全性] 來填入SAML2 使用者清單。 您可以在下列連結中可以找到有關群組原則的其他資訊︰`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [BeyondTrust 遠端支援] 圖格時，應該會自動登入您已設定 SSO 的 BeyondTrust 遠端支援。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 BeyondTrust 遠端支援](https://aad.portal.azure.com/)
