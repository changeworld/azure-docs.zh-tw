---
title: 教學課程：使用 Azure Active Directory 設定 Oracle 融合 ERP 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 Oracle 融合 ERP。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: ad6b24c4bbfc2e117c010b99247cb8eb9925ff3e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543817"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>教學課程：設定 Oracle 融合 ERP 來自動布建使用者

本教學課程的目的是要示範在 Oracle 融合 ERP 和 Azure Active Directory (Azure AD) 設定 Azure AD，以自動布建和取消布建使用者和/或群組至 Oracle 融合 ERP 的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需預覽功能的一般 Microsoft Azure 使用條款的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Oracle 融合 ERP 租](https://www.oracle.com/applications/erp/)使用者。
* 具有系統管理員許可權的 Oracle 融合 ERP 中的使用者帳戶。

## <a name="assign-users-to-oracle-fusion-erp"></a>將使用者指派給 Oracle 融合 ERP 
Azure Active Directory 使用所謂「指派」的概念，決定應該授權哪些使用者存取選取的應用程式。 在自動使用者布建的內容中，只會同步處理在 Azure AD 中指派給應用程式的使用者和/或群組。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者及/或群組需要存取 Oracle 融合 ERP。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 Oracle 融合 ERP：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>將使用者指派給 Oracle 融合 ERP 的重要秘訣 

 * 建議將單一 Azure AD 使用者指派給 Oracle 融合 ERP，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Oracle 融合式 ERP 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色 (如果有) 的話）。 具有預設存取角色的使用者會從佈建中排除。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>設定 Oracle 融合 ERP 以進行布建

使用 Azure AD 設定 Oracle 融合 ERP 來自動布建使用者之前，您必須在 Oracle 融合 ERP 上啟用 SCIM 布建。

1. 登入您的 [Oracle 融合 ERP 管理主控台](https://cloud.oracle.com/sign-in)

2. 按一下左上角的導覽器。 在 [ **工具**] 底下，選取 [ **安全性] 主控台**。

    ![Oracle 融合 ERP 新增 SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. 流覽至 [ **使用者**]。
    
    ![Oracle 融合 ERP 新增 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. 針對您將用來登入 Oracle 融合 ERP 管理主控台的管理使用者帳戶，儲存使用者名稱和密碼。 您必須在 Azure 入口網站中 Oracle 融合 ERP 應用程式的 [布建] 索引標籤中輸入 [ **管理員使用者名稱** ] 和 [ **密碼** ] 欄位中的這些值。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>從資源庫新增 Oracle 融合 ERP

若要設定 Oracle 融合 ERP 以利用 Azure AD 進行自動使用者布建，您需要從 Azure AD 應用程式資源庫將 Oracle 融合 ERP 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Oracle 融合 ERP，請執行下列步驟：**

1. 在 [ **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要加入新的應用程式，請選取窗格頂端的 [ **新增應用程式** ] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Oracle 融合 erp**，在結果面板中選取 [ **oracle 融合 erp** ]。

    ![結果清單中的 Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>設定自動使用者布建至 Oracle 融合 ERP 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Oracle 融合 ERP 中建立、更新及停用使用者和/或群組。

> [!TIP]
> 您也可以遵循《 [Oracle 融合 Erp 單一登入」教學](oracle-fusion-erp-tutorial.md)課程中提供的指示，選擇啟用 ORACLE 融合 ERP 的 SAML 型單一登入。 您可以獨立設定自動使用者布建的單一登入，雖然這兩個功能彼此互補。

> [!NOTE]
> 若要深入瞭解 Oracle 融合 ERP 的 SCIM 端點，請參閱 [Oracle 應用程式雲端中常見功能的 REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>若要在 Azure AD 中為 Fuze 設定自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Oracle Fusion ERP]****。

    ![應用程式清單中的 Oracle Fusion ERP 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段下的 [ `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **租使用者 URL**] 中輸入。 在 [ **管理員** 使用者名稱] 和 [ **密碼** ] 欄位中，輸入先前抓取的系統管理員使用者名稱和密碼。 按一下 [Azure AD 與 Oracle 融合 ERP 之間的 **測試連接** ]。 

    ![Oracle 融合 ERP 新增 SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [檔案] 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Oracle 融合 ERP**]。

    ![Oracle 融合 ERP 新增 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 ORACLE 融合 ERP 的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 ORACLE 融合 ERP 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Oracle 融合 ERP 新增 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 Oracle 融合 ERP**]。

    ![Oracle 融合 ERP 群組對應](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 ORACLE 融合 ERP 的群組屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 ORACLE 融合 ERP 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![Oracle 融合 ERP 群組屬性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Oracle 融合 ERP 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 Oracle 融合 ERP 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動報告，該報告會描述 ORACLE 融合 ERP 上的 Azure AD 布建服務所執行的所有動作。

    如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Oracle 融合 ERP 只支援其 SCIM 端點的基本驗證。
* Oracle 融合 ERP 不支援群組布建。
* Oracle 融合 ERP 中的角色會對應到 Azure AD 中的群組。 若要從 Azure AD 將角色指派給 Oracle 融合 ERP 中的使用者，您必須將使用者指派給以 Oracle 融合 ERP 中的角色命名的所需 Azure AD 群組。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
