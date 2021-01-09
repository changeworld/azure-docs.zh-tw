---
title: 教學課程：ThousandEyes 的使用者佈建 - Azure AD
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 ThousandEyes。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 885ee993748a0a571f396cc0dc28f2c0c1a4a0c3
ms.sourcegitcommit: 00aa5afaa9fac91f1059cfed3d8dbc954caaabe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2020
ms.locfileid: "97792512"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>教學課程︰設定 ThousandEyes 來自動佈建使用者

本教學課程旨在說明您需要在 ThousandEyes 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ThousandEyes。 

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure Active Directory 租用戶
* 已啟用[標準方案](https://www.thousandeyes.com/pricing)或更進階方案的 ThousandEyes 租用戶 
* ThousandEyes 中具有管理員權限的使用者帳戶 

> [!NOTE]
> Azure AD 佈建整合仰賴 [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)，這在標準方案或更進階的方案中可供 ThousandEyes 小組使用。

## <a name="assigning-users-to-thousandeyes"></a>將使用者指派給 ThousandEyes

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 ThousandEyes 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 ThousandEyes 應用程式︰

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>將使用者指派給 ThousandEyes 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 ThousandEyes，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 ThousandEyes 時，您必須在 [指派] 對話方塊中選取 [使用者] 角色，或另一個有效的應用程式特有角色 (如果有的話)。 [預設存取] 角色不適用於佈建，系統會略過這些使用者。

## <a name="configuring-user-provisioning-to-thousandeyes"></a>設定使用者佈建至 ThousandEyes 

本節會引導您將 Azure AD 連線至 ThousandEyes 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 ThousandEyes 中建立、更新和停用指派的使用者帳戶。

> [!TIP]
> 您也可以選擇啟用 ThousandEyes 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>在 Azure AD 中設定將使用者帳戶自動佈建至 ThousandEyes

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 如果您已經設定單一登入的 ThousandEyes，使用 [搜尋] 欄位搜尋您的 ThousandEyes 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [ThousandEyes]。 從搜尋結果中選取 ThousandEyes，並將它新增至您的應用程式清單。

    ![應用程式清單中的 ThousandEyes 連結](common/all-applications.png)
    
3. 選取您的 ThousandEyes 執行個體，然後選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

![螢幕擷取畫面顯示 ThousandEyes 的 [佈建] 索引標籤，其中已選取 [自動] 作為佈建模式。](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. 在 [管理員認證] 區段底下，輸入您 ThousandEyes 帳戶所產生的 [OAuth Bearer Token] \(OAuth 持有人權杖\) (您可以在 ThousandEyes 帳戶的 [Profile] \(設定檔\) 區段底下找到或產生權杖)。

    ![螢幕擷取畫面顯示應在何處尋找目前帳戶群組的 [帳戶設定] 連結。](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. 在 Azure 入口網站中，按一下 [測試連線]以確保 Azure AD 可以連線到您的 ThousandEyes 應用程式。 如果連線失敗，請確定您的 ThousandEyes 帳戶具有管理員權限，然後再試一次步驟 5。

7. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 [檔案]  。

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 ThousandEyes]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 ThousandEyes 的使用者屬性。 選取為 [比對] 屬性 (Property) 的屬性 (Attribute)，會用來比對 Parsable 中的使用者帳戶以進行更新作業。 如果您選擇變更[比對目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，則必須確保 Parsable API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

     |屬性|類型|支援篩選|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |作用中|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |name.formatted|String|


11. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

12. 若要啟用 ThousandEyes 的 Azure AD 佈建服務，請在 [設定]區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

13. 藉由在 [設定] 區段的 [範圍] 中選擇所需的值，定義要佈建至 ThousandEyes 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

14. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)深入了解隔離狀態。  

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
