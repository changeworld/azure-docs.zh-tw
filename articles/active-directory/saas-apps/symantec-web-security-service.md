---
title: 教程：配置賽門鐵克 Web 安全服務 （WSS）， 以便使用 Azure 活動目錄自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向賽門鐵克 Web 安全服務 （WSS） 預配和取消預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063113"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置賽門鐵克網路安全服務 （WSS）

本教程的目的是演示在賽門鐵克 Web 安全服務 （WSS） 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向賽門鐵克 Web 安全服務 （WSS） 預配和取消預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [賽門鐵克網路安全服務 （WSS） 租戶](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* 賽門鐵克網路安全服務 （WSS） 中的使用者帳戶，具有管理員許可權。

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>將使用者分配給賽門鐵克網路安全服務 （WSS）

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問賽門鐵克 Web 安全服務 （WSS）。 決定後，您可以按照此處的說明將這些使用者和/或組分配給賽門鐵克網路安全服務 （WSS）：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>將使用者分配給賽門鐵克網路安全服務 （WSS） 的重要提示

* 建議將單個 Azure AD 使用者分配給賽門鐵克 Web 安全服務 （WSS） 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給賽門鐵克 Web 安全服務 （WSS） 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>設置賽門鐵克網路安全服務 （WSS） 進行預配

在配置賽門鐵克 Web 安全服務 （WSS） 以使用 Azure AD 進行自動使用者預配之前，您需要在賽門鐵克 Web 安全服務 （WSS） 上啟用 SCIM 預配。

1. 登錄到您的[賽門鐵克網路安全服務管理主控台](https://portal.threatpulse.com/login.jsp)。 導航到**解決方案** > **服務**。

    ![賽門鐵克網路安全服務 （WSS）](media/symantec-web-security-service/service.png)

2. 導航到**帳戶維護** > **集成** > **新集成**。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  選擇**協力廠商使用者&組同步**。 

    ![Symantec Web Security Service](media/symantec-web-security-service/third-party-users.png)

4.  複製**SCIM URL**和**權杖**。 這些值將在 Azure 門戶中賽門鐵克 Web 安全服務 （WSS） 應用程式的預配選項卡中的 **"租戶 URL"** 和 **"秘密權杖"** 欄位中輸入。

    ![Symantec Web Security Service](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>從庫中添加賽門鐵克網路安全服務 （WSS）

要配置賽門鐵克 Web 安全服務 （WSS） 以自動預配 Azure AD，您需要將賽門鐵克 Web 安全服務 （WSS） 從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加賽門鐵克 Web 安全服務 （WSS），請執行以下步驟：**

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜索框中，輸入**賽門鐵克網路安全服務**，在結果面板中選擇**賽門鐵克網路安全服務**，然後按一下 **"添加**"按鈕以添加應用程式。

    ![結果清單中的 Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>將自動使用者預配配置配置給賽門鐵克網路安全服務 （WSS）

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在賽門鐵克 Web 安全服務 （WSS） 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據[賽門鐵克網路安全服務 （WSS） 單一登入教程](symantec-tutorial.md)中提供的說明，選擇啟用賽門鐵克網路安全服務 （WSS） 基於 SAML 的單一登入。 單一登入可以獨立于自動使用者預配進行配置，儘管這兩個功能相輔相成。

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>要在 Azure AD 中為賽門鐵克 Web 安全服務 （WSS） 配置自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選擇**賽門鐵克網路安全服務**。

    ![應用程式清單中的 [Symantec Web Security Service (WSS)] 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在"管理認證"部分下，分別輸入**在租戶 URL**和**秘密權杖**中檢索的**SCIM URL**和**權杖**值。 按一下 **"測試連接**"以確保 Azure AD 可以連接到賽門鐵克網路安全服務。 如果連接失敗，請確保您的賽門鐵克網路安全服務 （WSS） 帳戶具有管理員許可權，然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **"映射**"部分下，選擇**將 Azure 活動目錄使用者同步到賽門鐵克 Web 安全服務 （WSS）。**

    ![賽門鐵克網路安全服務 （WSS） 使用者映射](media/symantec-web-security-service/usermapping.png)

9. 在**屬性對應**部分中查看從 Azure AD 同步到賽門鐵克 Web 安全服務 （WSS） 的使用者屬性。 選擇為**匹配**屬性的屬性用於匹配賽門鐵克網路安全服務 （WSS） 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![賽門鐵克網路安全服務 （WSS） 使用者映射](media/symantec-web-security-service/userattribute.png)

10. 在 **"映射**"部分下，選擇**將 Azure 活動目錄組同步到賽門鐵克 Web 安全服務**。

    ![賽門鐵克網路安全服務 （WSS） 使用者映射](media/symantec-web-security-service/groupmapping.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到賽門鐵克 Web 安全服務 （WSS） 的組屬性。 選擇為**匹配**屬性的屬性用於匹配賽門鐵克 Web 安全服務 （WSS） 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![賽門鐵克網路安全服務 （WSS） 使用者映射](media/symantec-web-security-service/groupattribute.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 要為賽門鐵克 Web 安全服務啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到賽門鐵克網路安全服務 （WSS） 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步執行的時間比後續同步長。 有關使用者和/或組預配需要多長時間的詳細資訊，請參閱[預配使用者需要多長時間](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可以使用"**目前狀態**"部分監視進度並遵循指向預配活動報告的連結，該報表描述由賽門鐵克 Web 安全服務 （WSS） 上執行的 Azure AD 預配服務執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
