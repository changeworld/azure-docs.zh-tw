---
title: 教學課程：使用 Azure Active Directory 設定 Webroot Security Awareness Training 來自動佈建使用者 | Microsoft Docs
description: 了解如何從 Azure AD 對 Webroot Security Awareness Training 自動佈建及取消佈建使用者帳戶。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741888"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>教學課程：設定 Webroot Security Awareness Training 來自動佈建使用者

本教學課程將說明您在 Webroot Security Awareness Training 與 Azure Active Directory (Azure AD) 中設定自動使用者佈建所需的步驟。 設定後，Azure AD 就會使用 Azure AD 佈建服務，自動對 [Webroot Security Awareness Training](https://www.webroot.com/) 佈建及取消佈建使用者和群組。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Webroot Security Awareness Training 中建立使用者
> * 當使用者不再需要存取權時，移除 Webroot Security Awareness Training 中的使用者
> * 讓 Azure AD 與 Webroot Security Awareness Training 之間的使用者屬性保持同步
> * 在 Webroot Security Awareness Training 中佈建群組和群組成員資格

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中具有設定佈建[權限](../roles/permissions-reference.md)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* 已針對至少一個網站啟用 Webroot Security Awareness Training 的受控服務提供者主控台。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃您的佈建部署
1. 了解[佈建服務的運作方式](../app-provisioning/user-provisioning.md)。
2. 判斷誰會在[佈建範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。
3. 判斷哪些資料要[在 Azure AD 與 Webroot Security Awareness Training 之間對應](../app-provisioning/customize-application-attributes.md)。 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Webroot Security Awareness Training 以支援使用 Azure AD 進行佈建

### <a name="obtain-a-secret-token"></a>取得祕密權杖

若要將您的網站連線到 Azure AD，您必須在 Webroot 管理主控台中取得該網站的 **祕密權杖**。

1. 登入您的 [Webroot 管理主控台](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. 從 [網站] 索引標籤中，針對您想要讓其與 Azure AD 連線的網站，按一下 [Security Awareness Training] 欄中的齒輪圖示。

    ![齒輪圖示](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. 按一下按鈕以 **設定 Azure AD 整合**。

    ![設定 Azure AD 整合](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. 複製並儲存 **祕密權杖**。 此值會輸入到 Azure 入口網站中 Webroot Security Awareness Training 應用程式 [佈建] 索引標籤中的 [祕密權杖] 欄位。

5. 按一下 [完成]  。

    ![複製祕密權杖](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 Webroot Security Awareness Training

從 Azure AD 應用程式庫新增 Webroot Security Awareness Training，以開始管理針對 Webroot Security Awareness Training 進行的佈建。 [在此](../manage-apps/add-application-portal.md)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可讓您根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的範圍篩選條件。 

* 將使用者和群組指派給 Webroot Security Awareness Training 時，您必須選取 [預設存取] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是預設存取角色，您可[更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md)以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>步驟 5。 對 Webroot Security Awareness Training 設定自動使用者佈建 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>若要在 Azure AD 中對 Webroot Security Awareness Training 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Webroot Security Awareness Training]。

    ![應用程式清單中的 Webroot Security Awareness Training 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![[管理] 選項的螢幕擷取畫面，並已指出 [佈建] 選項。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建模式] 下拉式清單的螢幕擷取畫面，並已指出 [自動] 選項。](common/provisioning-automatic.png)

5. 在 [管理員認證] 區段下的 [租用戶 URL] 中輸入 `https://awarenessapi.webrootanywhere.com/api/v2/scim`。 輸入稍早在 [祕密權杖] 中所擷取的祕密權杖值。 按一下 [測試連線]，以確保 Azure AD 可連線至 Webroot Security Awareness Training。 如果連線失敗，請確定 Webroot Security Awareness Training 帳戶具有管理員權限，然後再試一次。

    ![顯示 [管理員認證] 對話方塊的螢幕擷取畫面，您可以在其中輸入您的租用戶 URL 和祕密權杖。](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. 在 [對應] 區段底下，選取 [佈建 Azure Active Directory 使用者]。

9. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Webroot Security Awareness Training 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Webroot Security Awareness Training 中的使用者帳戶，以進行更新作業。 如果您選擇變更[比對目標屬性](../app-provisioning/customize-application-attributes.md)，則必須確保 Webroot Security Awareness Training API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. 在 [對應] 區段底下，選取 [佈建 Azure Active Directory 群組]。

11. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Webroot Security Awareness Training 的群組屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute) 會用來比對 Webroot Security Awareness Training 中的群組，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|支援篩選|
      |---|---|---|
      |displayName|String|&check;|
      |members|參考|
      |externalId|String|

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Webroot Security Awareness Training 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定] 區段的 [範圍] 中選擇需要的值，可定義要佈建到 Webroot Security Awareness Training 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和群組，啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md)來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](../app-provisioning/application-provisioning-quarantine-status.md)深入了解隔離狀態。

## <a name="change-log"></a>變更記錄
* 1/21/2021-已新增使用者的核心屬性 "userName" 支援。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)