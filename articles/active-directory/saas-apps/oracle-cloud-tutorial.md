---
title: 教學課程：Azure Active Directory 與 Oracle Cloud Infrastructure Console 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Oracle Cloud Infrastructure Console 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: 90623c6fd1c0eba3042688ae6fd6fdc92b088fd4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096973"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>教學課程：整合 Oracle Cloud Infrastructure Console 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Oracle Cloud Infrastructure Console 與 Azure Active Directory (Azure AD)。 當您整合 Oracle Cloud Infrastructure Console 與 Azure AD 時，可以：

* 在 Azure AD 中控制可存取 Oracle Cloud Infrastructure Console 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Oracle Cloud Infrastructure Console。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 啟用 Oracle Cloud Infrastructure Console 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Oracle Cloud Infrastructure Console 支援 **SP** 起始的 SSO。

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>從資料庫新增 Oracle Cloud Infrastructure Console

若要設定將 Oracle Cloud Infrastructure Console 整合到 Azure AD 中，您需要從資源庫將 Oracle Cloud Infrastructure Console 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資料庫新增]  區段的搜尋方塊中，輸入 **Oracle Cloud Infrastructure Console**。
1. 從結果面板選取 [Oracle Cloud Infrastructure Console]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

以名為 **B. Simon** 的測試使用者，設定及測試與 Oracle Cloud Infrastructure Console 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Oracle Cloud Infrastructure Console 中相關使用者之間的連結關聯性。

若要設定及測試與 Oracle Cloud Infrastructure Console 搭配運作的 Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B. Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B. Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Oracle Cloud Infrastructure Console](#configure-oracle-cloud-infrastructure-console)** 以在應用程式端設定 SSO 設定。
    1. **[建立 Oracle Cloud Infrastructure Console 測試使用者](#create-oracle-cloud-infrastructure-console-test-user)** ，使 Oracle Cloud Infrastructure Console 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [Oracle Cloud Infrastructure Console] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

   > [!NOTE]
   > 您會從本教學課程的 **設定 Oracle Cloud Infrastructure Console 單一登入** 一節中取得服務提供者中繼資料檔案。
    
   1. 按一下 [上傳中繼資料檔案]  。

   1. 按一下 **資料夾圖示** 以選取中繼資料檔案，然後按一下 [上傳]  。

   1. 成功上傳中繼資料檔案後，就會在 [基本 SAML 設定]  區段文字方塊中自動填入 [識別碼]  和 [回覆 URL]  值。
    
      > [!NOTE]
      > 如果 [識別碼]  和 [回覆 URL]  值未自動填入，則請根據您的需求手動填入這些值。

      在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > 這不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [Oracle Cloud Infrastructure Console 用戶端支援小組](https://www.oracle.com/support/advanced-customer-support/products/cloud.html)以取得值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

   ![憑證下載連結](common/metadataxml.png)

1. Oracle Cloud Infrastructure Console 應用程式預期應有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯]  圖示以開啟 [使用者屬性] 對話方塊。

   ![image1](common/edit-attribute.png)

1. 除了以上屬性外，Oracle Cloud Infrastructure Console 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [群組宣告 (預覽)]  對話方塊的 [使用者屬性與宣告]  區段中，執行下列步驟：

   1. 按一下 **名稱識別碼值** 旁邊的 [畫筆]  。

   1. 選取 [永續性]  作為 **選擇名稱識別碼格式**。
 
   1. 按一下 [檔案]  。

      ![image2](./media/oracle-cloud-tutorial/config07.png)
    
      ![image3](./media/oracle-cloud-tutorial/config11.png)

   1. 按一下 [宣告中傳回的群組]  旁的 **筆**。

   1. 選取選項按鈕清單中的 [安全性群組]  。

   1. 選取 [群組識別碼]  的 [來源屬性]  。

   1. 勾選 [自訂群組宣告的名稱]  。

   1. 在 [名稱]  文字方塊中，輸入 **groupName**。

   1. 在 [命名空間 (選擇性)]  文字方塊中，輸入 `https://auth.oraclecloud.com/saml/claims`。

   1. 按一下 [檔案]  。

      ![image4](./media/oracle-cloud-tutorial/config08.png)

1. 在 [設定 Oracle Cloud Infrastructure Console]  區段上，根據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 B. Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B. Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B. Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Oracle Cloud Infrastructure Console 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Oracle Cloud Infrastructure Console]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B. Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-oracle-cloud-infrastructure-console"></a>設定 Oracle Cloud Infrastructure Console

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 Oracle Cloud Infrastructure Console。

1. 按一下功能表左側，按一下 [身分識別]  ，然後瀏覽至 [同盟]  。

   ![設定1](./media/oracle-cloud-tutorial/config01.png)

1. 按一下 [下載此文件]  連結，並且將文件上傳至 Azure 入口網站的 [基本 SAML 組態]  區段，然後按一下 [新增識別提供者]  ，來儲存 [服務提供者中繼資料檔案]  。

   ![設定2](./media/oracle-cloud-tutorial/config02.png)

1. 在 [新增識別提供者]  快顯上，執行下列步驟：

   ![設定3](./media/oracle-cloud-tutorial/config03.png)

   1. 在 [名稱]  文字方塊中，輸入您的名稱。

   1. 在 [描述]  文字方塊中，輸入描述。

   1. 選取 [MICROSOFT ACTIVE DIRECTORY 同盟服務 (ADFS) 或 SAML 2.0 相容識別提供者]  作為 **類型**。

   1. 按一下 [瀏覽]  以上傳您從 Azure 入口網站下載的同盟中繼資料 XML。

   1. 按一下 [編輯識別提供者]  區段中的 [繼續]  ，執行下列步驟：

      ![設定4](./media/oracle-cloud-tutorial/configure-09.png)

   1. [識別提供者群組] 應選取為 Azure AD 群組物件識別碼。 [群組識別碼] 應為來自 Azure Active Directory 的群組 GUID。 群組必須與 [OCI 群組]  欄位中的對應群組相對應。

   1. 您可以依據 Azure 入口網站中的每個設定，或是貴組織的需求，對應多個群組。 按一下 [+ 新增對應]  視需要新增任意數量的群組。

   1. 按一下 [提交]  。
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>建立 Oracle Cloud Infrastructure Console 測試使用者

 Oracle Cloud Infrastructure Console 支援預設的 Just-In-Time 佈建。 在這一節沒有您需要進行的動作項目。 嘗試存取期間不會建立新的使用者，也不需要建立使用者。

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Oracle Cloud Infrastructure Console] 圖格時，系統會將您重新導向至 Oracle Cloud Infrastructure Console 登入頁面。 從下拉式功能表選取 [識別提供者]  ，然後如下所示按一下 [繼續]  以登入。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

![組態](./media/oracle-cloud-tutorial/config10.png)

## <a name="next-steps"></a>後續步驟

設定 Oracle Cloud Infrastructure Console 後，您可以強制執行工作階段控制項，以即時防護貴組織敏感性資料的外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

