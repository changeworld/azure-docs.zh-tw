---
title: 教學課程：使用 Azure Active Directory 設定 Playvox 來自動布建使用者 |Microsoft Docs
description: 瞭解如何從 Azure AD 將使用者帳戶自動布建和取消布建至 Playvox。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862474"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>教學課程：設定 Playvox 來自動布建使用者

本教學課程說明在 Playvox 和 Azure Active Directory (Azure AD) 設定自動使用者布建時要遵循的步驟。 當設定時，Azure AD 使用 Azure AD 布建服務，自動將使用者或群組布建並取消布建至 [Playvox](https://www.playvox.com) 。 如需此服務的功能及其運作方式的重要詳細資料，以及常見問題的詳細資訊，請參閱使用 Azure Active Directory 自動布建和解除布建 [SaaS 應用程式](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Playvox 中建立使用者。
> * 當使用者不再需要存取權時，請移除 Playvox 中的使用者。
> * 在 Azure AD 和 Playvox 之間保持使用者屬性同步。

## <a name="prerequisites"></a>必要條件

本教學課程中的案例假設您已經具備下列必要條件：

* [Azure AD 租用戶](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中具有[權限](../roles/permissions-reference.md)可設定佈建的使用者帳戶。 例如，帳戶可能具有應用程式系統管理員、雲端應用程式系統管理員、應用程式擁有者或全域管理員角色。
* [Playvox](https://www.playvox.com)中具有超級系統管理員許可權的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟1：規劃布建部署

1. 瞭解布建 [服務的運作方式](../app-provisioning/user-provisioning.md)。

2. 判斷誰將在布建 [範圍](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)內。

3. 判斷要 [在 Azure AD 與 Playvox 之間對應](../app-provisioning/customize-application-attributes.md)的資料。

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>步驟2：使用 Azure AD 設定 Playvox 以支援布建

1. 登入 Playvox 管理主控台，並移至 [ **設定] > API 金鑰**。

2. 選取 [ **建立 API 金鑰**]。

    ![在 Playvox 使用者介面中顯示 [建立 API 金鑰] 按鈕位置的部分螢幕擷取畫面。](media/playvox-provisioning-tutorial/create.png)

3. 輸入有意義的 API 金鑰名稱，然後選取 [ **儲存**]。 產生 API 金鑰之後，請選取 [ **關閉**]。

4. 在您建立的 API 金鑰上，選取 [ **詳細資料** ] 圖示。

    ![部分螢幕擷取畫面，顯示 Playvox 使用者介面中詳細資料圖示的位置，也就是放大鏡。](media/playvox-provisioning-tutorial/api.png)

5. 複製並儲存 **BASE64 金鑰** 值。 稍後，在 Azure 入口網站中，您會在 Playvox 應用程式的 [布 **建] 索引** 標籤中，于 [**秘密權杖**] 文字方塊中輸入此值。

    ![[詳細資料 API 金鑰] 訊息方塊的螢幕擷取畫面，其中反白顯示 [BASE64] 索引鍵值。](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>步驟3：從 Azure AD 應用程式資源庫新增 Playvox

若要開始管理布建至 Playvox，請從應用程式庫將 Playvox 新增至您的 Azure AD 租使用者。 若要深入瞭解，請參閱 [快速入門：將應用程式新增至您的 Azure Active Directory (Azure AD) 租使用者](../manage-apps/add-application-portal.md)。

如果您先前已設定 Playvox 單一登入 (SSO) ，您可以使用相同的應用程式。 不過，我們建議您在一開始測試整合時建立個別的應用程式。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟4：定義將在布建範圍內的人員

您可以根據指派給應用程式或使用者或群組的屬性，使用 Azure AD 布建服務來界定即將布建的人員。 若要根據指派將布建到您應用程式的範圍，請參閱 [Azure Active Directory 中的管理應用程式的使用者指派](../manage-apps/assign-user-or-group-access-portal.md) ，以瞭解如何將使用者或群組指派給應用程式。 若要限定僅以使用者或群組的屬性為基礎布建的使用者，請使用範圍篩選器，如以 [屬性為基礎的應用程式布建與範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中所述。

請記住下列幾點：

* 將使用者指派給 Playvox 時，您必須選取預設存取以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果預設存取是應用程式唯一可用的角色，您可以 [更新應用程式資訊清單](../develop/howto-add-app-roles-in-azure-ad-apps.md) 以新增其他角色。

* 從小規模開始。 先測試一組小型的使用者或群組，再向所有人推出。 當布建範圍是以指派的使用者或群組為基礎時，您可以將一或兩個使用者或群組指派給應用程式，以控制該集合的大小。 當布建範圍包含所有使用者和群組時，您可以指定以 [屬性為基礎的範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 來限制測試集的大小。

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>步驟5：設定自動使用者布建至 Playvox

本節將引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者或群組指派來建立、更新及停用使用者或群組的步驟。

若要在 Azure AD 中為 Playvox 設定自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![Azure 入口網站部分的螢幕擷取畫面，其中已醒目提示 [企業應用程式] 和 [所有應用程式] 專案](common/enterprise-applications.png)

2. 在應用程式清單中，搜尋並選取 [ **Playvox**]。

    ![應用程式清單的部分螢幕擷取畫面，其中已反白顯示應用程式搜尋方塊。](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![顯示 [布建] 功能表項目的部分螢幕擷取畫面。](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![[布建] 索引標籤的部分螢幕擷取畫面，顯示 [布建模式] 下拉式清單方塊中選取的自動選項。](common/provisioning-automatic.png)

5. 在 [ **管理員認證** ] 區段中，輸入您的 Playvox **租使用者 URL** ，如下所示：

    `https://{tenant}.playvox.com/scim/v1`

    輸入您稍早在步驟2中複製的 **秘密權杖** 。 然後，選取 [ **測試連接** ] 以確保 Azure AD 可以連線至 Playvox。 如果連接失敗，請確定您的 Playvox 帳戶具有系統管理員許可權，然後再試一次。

    ![顯示 [系統管理員認證] 區段的部分螢幕擷取畫面，其中包含 [租使用者 URL] 和 [秘密權杖] 文字方塊，並醒目提示 [測試連接] 連結。](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [ **通知電子郵件** ] 文字方塊中，輸入將會收到布建錯誤通知的人員或群組的電子郵件地址。 接著選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![部分螢幕擷取畫面，顯示 [通知電子郵件] 文字方塊和 [電子郵件通知] 核取方塊。](common/provisioning-notification-email.png)

7. 選取 [儲存]。

8. **在 [對應**] 區段中，選取 [**同步處理 Azure Active Directory 使用者至 Playvox**]。

9. 在 [ **屬性對應** ] 區段中，檢查從 Azure AD 同步處理到 Playvox 的使用者屬性。 選取為 [比對 **] 屬性的屬性會** 用來比對 Playvox 中的使用者帳戶以進行更新作業。 如果您選擇變更相符的 [目標屬性](../app-provisioning/customize-application-attributes.md)，請確定 Playvox API 支援根據該屬性篩選使用者。 選取 [儲存] 認可任何變更。

   |屬性|類型|支援篩選|
   |---|---|---|
   |userName|String|&check;|
   |作用中|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. 若要設定範圍篩選條件，請參閱[範圍篩選器教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

11. 若要啟用 Playvox Azure AD 的布建服務，請在 [**設定**] 區段中，將 [布建 **狀態**] 變更為 [**開啟**]。

    ![[設定] 區段的部分螢幕擷取畫面，其中顯示 [布建狀態] 設定為 [開啟]。](common/provisioning-toggle-on.png)

12. 仍在 [ **設定**] 中，選擇您想要的 **範圍** 中的值，以定義要布建到 Playvox 的使用者或群組。

    ![[設定] 區段的部分螢幕擷取畫面，其中顯示 [領域] 下拉式清單方塊。](common/provisioning-scope.png)

13. 準備好要佈建時，請選取 [儲存]。

    ![顯示儲存和捨棄選項的部分螢幕擷取畫面。](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中所定義所有使用者和群組啟動首次同步處理週期。 初始週期所花費的時間比之後的週期長。 如果 Azure AD 布建服務正在執行，則大約每隔40分鐘就會進行一次週期。

## <a name="step-6-monitor-your-deployment"></a>步驟6：監視您的部署

設定布建之後，請使用下列資源來監視您的部署：

* 使用[佈建記錄](../reports-monitoring/concept-provisioning-logs.md)來判斷哪些使用者已佈建成功或失敗。
* 檢查[進度列](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)來查看佈建週期的狀態，以及其接近完成的程度。
* 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 若要深入了解隔離狀態，請參閱[應用程式佈建處於隔離狀態](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)