---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 BeyondTrust 遠端支援整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 BeyondTrust 遠端支援之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/12/2020
ms.author: jeedes
ms.openlocfilehash: c11d8aaa578006c7dbd96b457399df5b17fd9bd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95914889"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 BeyondTrust 遠端支援整合

在本教學課程中，您將了解如何整合 BeyondTrust 遠端支援與 Azure Active Directory (Azure AD)。 將 BeyondTrust 遠端支援與 Azure AD 整合後，您可以：

* 在 Azure AD 中控制可存取 BeyondTrust 遠端支援的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 BeyondTrust 遠端支援。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 BeyondTrust 遠端支援單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* BeyondTrust 遠端支援可支援由 **SP** 起始的 SSO
* BeyondTrust 遠端支援可支援 **Just In Time** 使用者佈建

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>從資源庫新增 BeyondTrust 遠端支援

若要設定將 BeyondTrust 遠端支援整合到 Azure AD 中，您需要從資源庫將 BeyondTrust 遠端支援新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **BeyondTrust 遠端支援**。
1. 從結果面板中選取 [BeyondTrust 遠端支援]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>設定及測試適用於 BeyondTrust 遠端支援的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 BeyondTrust 遠端支援搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 BeyondTrust 遠端支援中相關使用者之間的連結關聯性。

若要設定及測試與 BeyondTrust 遠端支援搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 BeyondTrust 遠端支援 SSO](#configure-beyondtrust-remote-support-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 BeyondTrust 遠端支援測試使用者](#create-beyondtrust-remote-support-test-user)** - 使 BeyondTrust 遠端支援中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [BeyondTrust 遠端支援] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<HOSTNAME>.bomgar.com/saml`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`https://<HOSTNAME>.bomgar.com`

    c. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 稍後會在教學課程中知道這些值的說明。

1. BeyondTrust 遠端支援應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML token 屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，BeyondTrust 遠端支援應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 |  來源屬性|
    | ---------------| ----------|
    | 使用者名稱 | user.userprincipalname |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 電子郵件 | user.mail |
    | 群組 | user.groups |

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
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-beyondtrust-remote-support-sso"></a>設定 BeyondTrust 遠端支援 SSO

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入 BeyondTrust 遠端支援。

1. 瀏覽至 [使用者與安全性]   > [安全性提供者]  。

1. 按一下 [SAML 提供者] 中的 [編輯] 圖示。

    ![SAML 提供者編輯圖示](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. 展開 [服務提供者設定] 區段。

1. 按一下 [下載服務提供者中繼資料]，或者，您可以複製 [實體識別碼] 和 [ACS URL] 值，並在 Azure 入口網站的 [基本 SAML 設定] 區段中使用這些值。

    ![下載服務提供者中繼資料](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. 在 [識別提供者設定] 區段底下，按一下 [上傳識別提供者中繼資料]，並找出您從 Azure 入口網站下載的中繼資料 XML 檔案。

1.  [實體識別碼]、[單一登入服務 URL] 和 [伺服器憑證] 會自動上傳，而 [SSO URL 通訊協定繫結] 必須變更為 **HTTP POST**。

    ![顯示 [識別提供者設定] 區段的螢幕擷取畫面，您會在其中執行這些動作。](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. 按一下 [ **儲存**]。

### <a name="create-beyondtrust-remote-support-test-user"></a>建立 BeyondTrust 遠端支援測試使用者

我們將在這裡設定使用者佈建設定。 此區段中使用的值將會從 Azure 入口網站中的 **使用者屬性與宣告** 區段中進行參照。 我們已將此值設定為建立時匯入的預設值，不過，您可以視需要自訂此值。

![顯示 [使用者佈建設定] 的螢幕擷取畫面，您可以在其中設定使用者值。](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> 此實作不需要群組和電子郵件屬性。 如果使用 Azure AD 群組，並將其指派給 BeyondTrust 遠端支援群組原則來取得權限，則群組的物件識別碼必須在 Azure 入口網站中透過其屬性來進行參照，並且要放在 [可用群組] 區段中。 完成此作業之後，物件識別碼/AD 群組即可指派給群組原則來取得權限。

![顯示 IT 區段的螢幕擷取畫面，其中具有成員資格類型、來源、類型和物件識別碼。](./media/bomgarremotesupport-tutorial/config-user2.png)

![顯示群組原則 [基本設定] 頁面的螢幕擷取畫面。](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> 或者，您也可以在 SAML2 安全性提供者上設定預設的群組原則。 藉由定義此選項，這會將群組原則中指定的權限指派給所有透過 SAML 進行驗證的使用者。 BeyondTrust 遠端支援/特殊權限的遠端存取中包含權限有限的「一般成員」原則，可用來測試驗證並將使用者指派給正確的原則。 在第一次驗證嘗試成功之前，使用者不會透過 /login > [使用者與安全性] 來填入SAML2 使用者清單。 您可以在下列連結中可以找到有關群組原則的其他資訊︰`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向您可以在其中起始登入流程的 BeyondTrust 遠端支援登入 URL。 

* 直接移至 BeyondTrust 遠端支援登入 URL，然後從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您按一下「我的應用程式」中的 [BeyondTrust 遠端支援] 圖格時，將會重新導向至 BeyondTrust 遠端支援登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

設定 BeyondTrust 遠端支援後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)
