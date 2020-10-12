---
title: 教學課程：設定 Symantec Web Security Service (WSS) ，以 Azure Active Directory 自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Symantec Web Security Service (WSS) 。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 7b4a5e76ec8dad66bf300cc3e1d55471383f83ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285789"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>教學課程：設定 Symantec Web Security Service (WSS) 來自動布建使用者

本教學課程的目的是要示範如何在 Symantec Web Security Service (WSS) 和 Azure Active Directory (Azure AD) 設定 Azure AD，以將使用者和/或群組自動布建和解除布建到 Symantec Web Security Service (WSS) 。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [ (WSS) 租使用者的 Symantec Web 安全性服務](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Symantec Web Security Service 中的使用者帳戶 (WSS) 具有系統管理員許可權。

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>將使用者指派給 Symantec Web Security Service (WSS) 

Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者及/或群組需要存取 Symantec Web Security Service (WSS) 。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Symantec Web Security Service (WSS) ：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>將使用者指派給 Symantec Web Security Service (WSS) 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Symantec Web Security Service (WSS) 以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Symantec Web Security Service (WSS) 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有) 。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>設定 Symantec Web Security Service (WSS) 以進行布建

在設定 Symantec Web Security Service (WSS) 以 Azure AD 自動布建使用者之前，您必須在 Symantec Web Security Service (WSS) 上啟用 SCIM 布建。

1. 登入您的 [Symantec Web Security Service 管理主控台](https://portal.threatpulse.com/login.jsp)。 流覽至 [**方案**  >  **服務**]。

    ![Symantec Web Security Service (WSS) ](media/symantec-web-security-service/service.png)

2. 流覽至**帳戶維護**  >  **Integrations**  >  **整合的新整合**。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  選取 [ **協力廠商使用者 & 群組同步**]。 

    ![協力廠商使用者 & 群組同步選項的螢幕擷取畫面。](media/symantec-web-security-service/third-party-users.png)

4.  複製 **SCIM URL** 和 **權杖**。 這些值將會在您 Symantec Web Security Service 的 [布建] 索引標籤中輸入至 [ **租使用者 URL** ] 和 [ **秘密權杖** ] 欄位中， (WSS) 應用程式 Azure 入口網站。

    ![[新增整合] 對話方塊的螢幕擷取畫面，其中已將 S C I M U R L 和權杖文字方塊稱為 out。](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>從資源庫新增 Symantec Web Security Service (WSS) 

若要設定 Symantec Web Security Service (WSS) 以 Azure AD 自動布建使用者，您需要從) 應用程式庫將 Symantec Web Security Service (WSS Azure AD 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Symantec Web Security Service (WSS) ，請執行下列步驟：**

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入 **Symantec Web Security service**，在結果窗格中選取 [ **Symantec web security service** ]，然後按一下 [ **新增** ] 按鈕以新增應用程式。

    ![結果清單中的 Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>設定自動使用者布建至 Symantec Web Security Service (WSS) 

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Symantec Web Security Service 中建立、更新及停用使用者和/或群組 (WSS) 的步驟。

> [!TIP]
> 您也可以選擇啟用 Symantec Web Security Service (WSS) 的 SAML 型單一登入，請遵循 [Symantec Web Security service (wss) 單一登入教學](symantec-tutorial.md)課程中提供的指示。 您可以獨立設定自動使用者布建的單一登入，雖然這兩個功能彼此互補。

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>若要設定 Symantec Web Security Service 的自動使用者布建 (WSS) Azure AD：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Symantec Web Security Service**]。

    ![應用程式清單中的 [Symantec Web Security Service (WSS)] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![已呼叫 [布建] 選項的 [管理選項] 螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建模式] 下拉式清單的螢幕擷取畫面，其中已呼叫 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [系統管理員認證] 區段底下，分別輸入**租使用者 url**和**秘密權杖**中稍早抓取的**SCIM URL**和**權杖**值。 按一下 [ **測試連接** ]，以確保 Azure AD 可以連線到 Symantec Web Security Service。 如果連接失敗，請確定您的 Symantec Web Security Service (WSS) 帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 **[儲存]** 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者到 Symantec Web Security SERVICE] (WSS) **。

    ![[對應] 區段的螢幕擷取畫面，其中會將 [同步處理 Azure Active Directory 使用者與 Symantec Web Security Service W] 選項稱為 [已呼叫]。](media/symantec-web-security-service/usermapping.png)

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Symantec Web Security SERVICE (WSS) 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Symantec Web Security SERVICE (WSS) 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![顯示16個相符屬性的 [屬性對應] 區段的螢幕擷取畫面。](media/symantec-web-security-service/userattribute.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Symantec Web Security Service**]。

    ![[對應] 區段的螢幕擷取畫面，其中已將 [同步處理 Azure Active Directory 群組至 [Symantec Web Security Service W s] 選項稱為 [已呼叫]。](media/symantec-web-security-service/groupmapping.png)

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 Symantec Web Security SERVICE (WSS) 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 Symantec Web Security Service 中的群組 (WSS) 以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![顯示三個相符屬性的 [屬性對應] 區段的螢幕擷取畫面。](media/symantec-web-security-service/groupattribute.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Symantec Web Security Service 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Symantec WEB Security Service (WSS) 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行。 如需布建使用者和/或群組所需時間長度的詳細資訊，請參閱布建 [使用者](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)需要多久的時間。

您可以使用 [ **目前狀態** ] 區段來監視進度，並遵循您布建活動報告的連結，其中描述在 Symantec Web Security SERVICE (WSS) 上 Azure AD 布建服務所執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 布建記錄，請參閱 [有關自動布建使用者帳戶的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
