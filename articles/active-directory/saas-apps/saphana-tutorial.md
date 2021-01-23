---
title: 教學課程：Azure Active Directory 與 SAP HANA 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP HANA 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727076"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>教學課程：Azure Active Directory 與 SAP HANA 整合

在本教學課程中，您會了解如何整合 SAP HANA 與 Azure Active Directory (Azure AD)。 在整合 SAP HANA 與 Azure AD 時，您可以︰

* 您可以在 Azure AD 中控制可存取 SAP HANA 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP HANA。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 SAP HANA 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用單一登入 (SSO) 的 SAP Hana 訂用帳戶
- 執行於任何公用 IaaS、內部部署、Azure VM 或 Azure 中 SAP 大型執行個體的 HANA 執行個體
- XSA 管理 Web 介面，以及安裝在 HANA 執行個體上的 HANA Studio

> [!NOTE]
> 我們不建議使用 SAP Hana 的生產環境以測試本教學課程中的步驟。 先在應用程式的開發或預備環境中測試整合，然後使用生產環境。

若要測試本教學課程中的步驟，請遵循下列建議：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SAP HANA 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SAP HANA 支援由 **IDP** 起始的 SSO
* SAP HANA 支援 **Just-in-Time** 使用者佈建

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。


## <a name="adding-sap-hana-from-the-gallery"></a>從資源庫新增 SAP HANA

若要設定將 SAP HANA 整合到 Azure AD 中，您需要從資源庫將 SAP HANA 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 [SAP HANA]。
1. 從結果面板選取 [SAP HANA]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>設定和測試 SAP HANA 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP HANA 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP HANA 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP HANA 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[設定 SAP HANA SSO](#configure-sap-hana-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 SAP HANA 測試使用者](#create-sap-hana-test-user)** - 使 SAP HANA 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 **SAP HANA** 應用程式整合頁面上，尋找 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > [回覆 URL] 不是真實的值。 請使用實際的「回覆 URL」來更新此值。 請連絡 [SAP HANA 客戶支援小組](https://cloudplatform.sap.com/contact.html)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. SAP HANA 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![顯示 [使用者屬性] 區段的螢幕擷取畫面，其中已選取 [編輯] 圖示。](common/edit-attribute.png)

6. 在 [使用者屬性與宣告] 對話方塊的 [使用者屬性] 區段中，執行下列步驟：
 
    a. 按一下 [**編輯**] 圖示以開啟 [**管理使用者宣告**] 對話方塊。

    ![顯示 [使用者屬性和宣告] 對話方塊的螢幕擷取畫面，其中已選取 [編輯] 圖示。](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. 從 [轉換]  清單中，選取 [ExtractMailPrefix()]  。

    c. 從 [參數 1]  清單中選取 [user.mail]  。

    d. 按一下 [檔案]  。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP HANA 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP HANA]。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sap-hana-sso"></a>設定 SAP HANA SSO

1. 若要在 SAP Hana 端設定單一登入，請瀏覽至個別的 HTTPS 端點以登入您的 **HANA XSA Web 主控台**。

    > [!NOTE]
    > 在預設組態中，URL 會將要求重新導向至登入畫面，這需要已通過驗證之 SAP Hana 資料庫使用者的認證。 登入的使用者必須擁有執行 SAML 管理工作的權限。

2. 前往 XSA Web 介面中的 [SAML 識別提供者]  。 在畫面底部選取 **+** 按鈕，以顯示 [新增識別提供者資訊]  窗格。 然後採取下列步驟：

    ![新增識別提供者](./media/saphana-tutorial/sap1.png)

    a. 在 [新增識別提供者資訊]  窗格中，將中繼資料 XML 內容 (從 Azure 入口網站下載) 貼到 [中繼資料]  方塊中。

    ![螢幕擷取畫面：顯示 [新增識別提供者資訊] 窗格，並已醒目提示 [中繼資料] 和 [名稱] 方塊。](./media/saphana-tutorial/sap2.png)

    b. 如果 XML 文件的內容有效，剖析程序會擷取 [一般資料] 畫面區域中的 [主旨]、[實體識別碼] 和 [簽發者] 欄位所需的資訊。 該程序也會擷取 [目的地]  畫面區域中 URL 欄位的所需資訊，例如 [基底 URL] 和 [SingleSignOn URL]  \(*) 欄位。

    ![[新增識別提供者] 設定](./media/saphana-tutorial/sap3.png)

    c. 在 [一般資料] 畫面區域的 [名稱] 方塊中，輸入新 SAML SSO 識別提供者的名稱。

    > [!NOTE]
    > SAML IDP 名稱為必填，且不可重複。 當您選取 SAML 作為 SAP Hana XS 應用程式要使用的驗證方法時，該名稱會出現在可用的 SAML IDP 清單中。 例如，您可以在 XS Artifact Administration 工具的 [驗證]  畫面區域中執行此操作。

3. 選取 [儲存]  ，以儲存 SAML 識別提供者的詳細資料，並將新的 SAML IDP 新增至已知 SAML IDP 的清單。

    ![[儲存] 按鈕](./media/saphana-tutorial/sap4.png)

4. 在 HANA Studio 之 [設定]  索引標籤的系統屬性中，依 **saml** 篩選設定。 然後將 **assertion_timeout** 從 **10 秒** 調整為 **120 秒**。

    ![assertion_timeout 設定](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>建立 SAP Hana 測試使用者

若要讓 Azure AD 使用者登入 SAP Hana，您必須在 SAP HANA 中佈建這些使用者。
SAP Hana 支援預設啟用的 **Just-In-Time** 佈建。

如果您需要手動建立使用者，請採取下列步驟：

>[!NOTE]
>您可以變更使用者所使用的外部驗證。 他們可以使用外部系統 (例如 Kerberos) 進行驗證。 如需外部身分識別的詳細資訊，請連絡[網域系統管理員](https://cloudplatform.sap.com/contact.html)。

1. 以系統管理員身分開啟 [SAP Hana Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)，然後為 SAML SSO 啟用 DB-User。

    ![建立使用者](./media/saphana-tutorial/sap5.png)

2. 選取 [SAML]  左邊的不可見核取方塊，並選取 [設定]  連結。

3. 選取 [新增]  以新增 SAML IDP。  選取適當的 SAML IDP，然後選取 [確定]  。

4. 新增 [外部識別]  \(在此情況下為 BrittaSimon)，或選擇 [任何]  。 然後選取 [確定]  。

   > [!Note]
   > 如果未選取 [任何]  核取方塊，則 HANA 中的使用者名稱必須完全符合 UPN 中網域尾碼前面的使用者名稱。 (舉例而言，BrittaSimon@contoso.com 會在 HANA 中變成 BrittaSimon。)

5. 為了進行測試，請對使用者指派所有 **XS** 角色。

    ![指派角色](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 您只應該提供適用於您使用案例的權限。

6. 儲存使用者。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。

* 在 Azure 入口網站中按一下 [測試此應用程式]，您應該會自動登入您已設定 SSO 的 SAP HANA

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [SAP HANA] 圖格時，應該會自動登入您設定 SSO 的 SAP HANA。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>後續步驟

設定 SAP HANA 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。