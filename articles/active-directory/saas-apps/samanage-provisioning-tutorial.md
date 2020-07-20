---
title: 教學課程︰以 Azure Active Directory 設定 Samanage 來自動佈建使用者 | Microsoft Docs
description: 瞭解如何從 Azure AD 自動布建和取消布建使用者帳戶至 Samanage。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 182d314b24ce082d996cb692e2a7bb35265abcfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628029"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教學課程︰設定 Samanage 來自動佈建使用者
本教學課程說明您需要在 Samanage 和 Azure Active Directory （Azure AD）中執行的步驟，以設定自動使用者布建。 設定之後，Azure AD 會使用 Azure AD 布建服務，將使用者和群組自動布建和取消布建至[Samanage](https://www.samanage.com/pricing/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="migrate-to-the-new-samange-application"></a>遷移至新的 Samange 應用程式

如果您已經與 Samanage 整合，請參閱下一節，瞭解即將進行的變更。 如果您是第一次設定 Samanage，可以略過本節並移至**支援的功能**。

#### <a name="whats-changing"></a>有哪些變更項目？
* Azure AD 端上的變更：在 Samange 中布建使用者的授權方法在過去是**基本驗證**。很快地，您會看到授權方法已變更為**長時間的密碼權杖**。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>將現有的自訂整合遷移到新的應用程式時，我需要做什麼？
如果您現有的 Samanage 整合具有有效的系統管理員認證，則**不需要採取任何動作**。 我們會自動將客戶遷移至新的應用程式。 此程式會在幕後完全完成。 如果現有的認證過期，或如果您需要再次授權應用程式的存取，您必須產生長時間的密碼權杖。 若要產生新的權杖，請參閱本文的步驟2。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判斷我的應用程式是否已遷移？ 
當您的應用程式遷移時，[管理員**認證**] 區段中的 [**管理員使用者名稱**] 和 [**管理員密碼**] 欄位將會取代為單一 [**秘密權杖**] 欄位。

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Samanage 中建立使用者
> * 當使用者不再需要存取權時，移除 Samanage 中的使用者
> * 在 Azure AD 和 Samanage 之間保持使用者屬性同步
> * 在 Samanage 中布建群組和群組成員資格
> * [單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial)Samanage （建議選項）

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* 具有 Professional 套件的[Samanage 租](https://www.samanage.com/pricing/)使用者。
* Samanage 中具有系統管理員許可權的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要[在 Azure AD 和 Samanage 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Samanage 以支援以 Azure AD 布建

若要產生驗證的秘密權杖，請參閱[此](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)程式。

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Samanage

從 Azure AD 應用程式庫新增 Samanage，以開始管理布建至 Samanage。 如果您先前已設定 SSO 的 Samanage，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 Samanage 時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>步驟 5。 設定自動使用者布建至 Samanage 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>若要在 Azure AD 中為 Samanage 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Samanage] ****。

    ![應用程式清單中的 Samanage 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[佈建] 索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，輸入 [ `https://api.samanage.com` **租使用者 URL**]。  輸入稍早在 [祕密權杖] 中所擷取的祕密權杖值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Samanage。 如果連線失敗，請確定您的 Samanage 帳戶具有系統管理員許可權，然後再試一次

    ![佈建](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Samanage**]。

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Samanage 的使用者屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的[目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，就必須確定 Samanage API 支援根據該屬性來篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

      ![Samange 使用者對應](./media/samanage-provisioning-tutorial/user-attributes.png)

10. 在 [對應]**** 區段中，選取 [將 Azure Active Directory 群組同步處理至 Samanage]****。

11. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 Samanage 的群組屬性。 選取為 [比對]**** 屬性的屬性會用來比對 Samanage 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      ![Samange 群組對應](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Samanage 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定]**** 區段的 [範圍]**** 中選擇需要的值，可定義要佈建到 Samanage 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期會比後續週期花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。

## <a name="connector-limitations"></a>連接器限制

如果您選取 [**同步所有使用者和群組**] 選項，並設定 [Samanage**角色**] 屬性的值，則在 [**如果 null （是選擇性）** ] 方塊中，[預設值] 下的值必須以下列格式表示：

- {"displayName"： "role"}，其中 role 是您想要的預設值。

## <a name="change-log"></a>變更記錄

* 04/22/2020-已將授權方法從基本驗證更新為長期的密碼權杖。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
