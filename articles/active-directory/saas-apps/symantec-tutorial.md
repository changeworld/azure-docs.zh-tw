---
title: 教學課程：Azure Active Directory 與 Symantec Web Security Service (WSS) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Symantec Web Security Service (WSS) 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518359"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>教學課程：Azure Active Directory 與 Symantec Web Security Service (WSS) 整合

在本教學課程中，您將學習如何整合 Symantec Web Security Service (WSS) 帳戶與 Azure Active Directory (Azure AD) 帳戶，讓 WSS 可使用 SAML 驗證來驗證 Azure AD 中所佈建的使用者，並強制執行使用者或群組層級的原則規則。

Symantec Web Security Service (WSS) 與 Azure AD 整合提供下列優點：

- 從 Azure AD 入口網站管理您的 WSS 帳戶使用的所有使用者和群組。

- 讓使用者可以使用其 Azure AD 認證在 WSS 中驗證其自身。

- 強制執行 WSS 帳戶中所定義的使用者和群組層級原則規則。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 Symantec Web Security Service (WSS) 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Symantec Web Security Service (WSS) 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Symantec Web Security Service (WSS) 支援 **SP** 起始的 SSO

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>從資源庫新增 Symantec Web Security Service (WSS)

若要設定 Symantec Web Security Service (WSS) 與 Azure AD 整合，您需要從資源庫將 Symantec Web Security Service (WSS) 新增到受控 SaaS App 清單。

**若要從資源庫新增 Symantec Web Security Service (WSS)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Symantec Web Security Service (WSS)** ，從結果面板選取 [Symantec Web Security Service (WSS)]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Symantec Web Security Service (WSS) 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Symantec Web Security Service (WSS) 中相關使用者之間的連結關聯性。

若要使用 Symantec Web Security Service (WSS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **設定 Symantec Web Security Service (WSS) 單一登入** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Symantec Web Security Service (WSS) 測試使用者](#create-symantec-web-security-service-wss-test-user)** - 使 Symantec Web Security Service (WSS) 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Symantec Web Security Service (WSS) 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/) 的 [Symantec Web Security Service (WSS)]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  對話方塊上，執行下列步驟：

    ![Symantec Web Security Service (WSS) 的網域和 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，鍵入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. 在 [回覆 URL]  文字方塊中，鍵入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > 如果 [識別碼] 和 [回覆 URL] 的值因為某些原因而沒有作用，請連絡 [Symantec Web Security Service (WSS) 客戶支援小組](https://www.symantec.com/contact-us)。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載 **同盟中繼資料 XML** ，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>設定 Symantec Web Security Service (WSS) 單一登入

若要在 Symantec Web Security Service (WSS) 端設定單一登入，請參閱 WSS 線上文件。 所下載的 **同盟中繼資料 XML** 必須匯入到 WSS 入口網站。 如果您需要協助進行 WSS 入口網站設定，請連絡 [Symantec Web Security Service (WSS) 支援小組](https://www.symantec.com/contact-us)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon** 。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Symantec Web Security Service (WSS) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Symantec Web Security Service (WSS)]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入並選取 [Symantec Web Security Service (WSS)]  。

    ![應用程式清單中的 [Symantec Web Security Service (WSS)] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-symantec-web-security-service-wss-test-user"></a>建立 Symantec Web Security Service (WSS) 測試使用者

在本節中，您要在 Symantec Web Security Service (WSS) 中建立名為 Britta Simon 的使用者。 您可以在 WSS 入口網站中手動建立對應的使用者名稱，您也可以等候幾分鐘 (~ 15 分鐘)，讓 Azure AD 中佈建的使用者/群組同步處理至 WSS 入口網站。 您必須先建立和啟動使用者，然後才能使用單一登入。 將用來瀏覽網站的使用者電腦公用 IP 位址也必須佈建到 Symantec Web Security Service (WSS) 入口網站中。

> [!NOTE]
> 請[按一下這裡](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)以取得您機器的公用 IP 位址。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，因為您已將 WSS 帳戶設定為要使用 Azure AD 來進行 SAML 驗證，您將會測試單一登入功能。

在將網頁瀏覽器設定為將流量 Proxy 處理至 WSS 後，如果您開啟網頁瀏覽器並嘗試瀏覽至網站，則系統會將您重新導向至 Azure 的登入頁面。 請輸入已佈建到 Azure AD 之測試使用者 (也就是 BrittaSimon) 的認證以及相關聯的密碼。 通過驗證之後，您就可以瀏覽至您選擇的網站。 如果您在 WSS 端建立原則規則以阻止 BrittaSimon 瀏覽至特定網站，則當您嘗試以 BrittaSimon 這位使用者的身分瀏覽至該網站時，您應該會看見 WSS 封鎖頁面。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)