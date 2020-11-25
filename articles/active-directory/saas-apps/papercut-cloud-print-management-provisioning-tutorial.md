---
title: 教學課程：設定 PaperCut 雲端列印管理 (Pocket/Hive) ，以 Azure Active Directory 自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 自動布建並取消布建使用者帳戶， (Pocket/Hive) 的 PaperCut 雲端列印管理。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7e65d727-2951-4aec-a7a3-7bde49ed09e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: d0ecc06cd256dc2fae598e8bc44336d69a9c99df
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031186"
---
# <a name="tutorial-configure-papercut-cloud-print-management-pockethive-for-automatic-user-provisioning"></a>教學課程：設定 PaperCut 雲端列印管理 (Pocket/Hive) 來自動布建使用者

本教學課程說明在 PaperCut 雲端列印管理 (Pocket/Hive) 和 Azure Active Directory (Azure AD) 設定自動使用者布建時所需執行的步驟。 當設定時，Azure AD 會使用 Azure AD 布建服務來自動布建及取消布建使用者和群組，以 [PaperCut 雲端列印管理](https://www.papercut.com/products/papercut-pocket/) 。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="capabilities-supported"></a>支援的功能

> [!div class="checklist"]
> * 在 PaperCut 雲端列印管理中建立使用者 (Pocket/Hive) 
> * 在 PaperCut 雲端列印管理中移除使用者， (Pocket/Hive) 不再需要存取權
> * 在 Azure AD 與 PaperCut 雲端列印管理之間保持使用者屬性同步 (Pocket/Hive) 

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。 
* PaperCut 雲端列印管理中的使用者帳戶 (具有系統管理員許可權的 Pocket/Hive) 


## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署

1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要 [在 Azure AD 與 PaperCut 雲端列印管理之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。

## <a name="step-2-configure-papercut-cloud-print-management-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 PaperCut 雲端列印管理以支援使用 Azure AD 布建

1. 登入 [PaperCut Pocket 管理主控台](https://pocket.papercut.com/) 或 [PaperCut Hive 管理主控台](https://hive.papercut.com/)。

2. 流覽至 **附加** 元件  >  的 **所有附加** 元件，並尋找 Microsoft Azure AD 的 **使用者同步附件**。

3. 按一下 [ **深入瞭解** ] 按鈕，然後按一下 [ **新增** ] 以安裝。



4. 安裝之後，會顯示您的 **租使用者 URL** 和 **秘密權杖** 的增益集詳細資料頁面。 這些值將會在 [租使用者 URL \* ] 欄位和 [秘密權杖] 欄位中，輸入在您 PaperCut 雲端列印管理的 [布建] 索引標籤中， \* (Pocket/Hive) 應用程式 Azure 入口網站。



## <a name="step-3-add-papercut-cloud-print-management-pockethive-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增 PaperCut 雲端列印管理 (Pocket/Hive) 

從 Azure AD 應用程式庫新增 PaperCut 雲端列印管理 (Pocket/Hive) ，開始管理布建 PaperCut 雲端列印管理 (Pocket/Hive) 。 如果您先前已設定 PaperCut 雲端列印管理 (適用于 SSO 的 Pocket/Hive) ，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。

* 將使用者和群組指派給 PaperCut 雲端列印管理 (Pocket/Hive) 時，您必須選取 **預設存取** 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

## <a name="step-5-configure-automatic-user-provisioning-to-papercut-cloud-print-management-pockethive"></a>步驟 5。 設定自動使用者布建以 PaperCut 雲端列印管理 (Pocket/Hive) 

此節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和/或群組指派，在 TestApp 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-papercut-cloud-print-management-in-azure-ad"></a>若要在 Azure AD 中設定 PaperCut 雲端列印管理的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

   ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **PaperCut 雲端列印管理 (Pocket/Hive)**。

   ![應用程式清單中的 PaperCut Cloud 列印管理連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

   ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

   ![佈建索引標籤 [自動]](common/provisioning-automatic.png)

5. 在 [系統 **管理員認證** ] 區段下，輸入您的 PaperCut 雲端列印管理 (Pocket/Hive) 租使用者 URL 和秘密權杖。 按一下 [ **測試連接** ] 以確保 Azure AD 可以連線至 PaperCut 雲端列印管理。 如果連接失敗，請確定您的 PaperCut Cloud 列印管理帳戶具有系統管理員許可權，然後再試一次。

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

   ![通知電子郵件](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者 PaperCut 雲端列印管理**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理至 PaperCut 雲端列印管理的使用者屬性。 選取為 [比對] 屬性 **的屬性會** 用來比對 PaperCut 雲端列印管理中的使用者帳戶，以進行更新作業。 如果您選擇變更相符的 [目標屬性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，您將必須確定 PaperCut Cloud Print 管理 API 支援根據該屬性篩選使用者。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |emails[type eq "work"].value|String|

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用適用于 PaperCut 雲端列印管理的 Azure AD 布建服務 (Pocket/Hive) ，請在 [**設定**] 區段中，將 [布建 **狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 PaperCut 雲端列印管理 (Pocket/Hive) 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

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
