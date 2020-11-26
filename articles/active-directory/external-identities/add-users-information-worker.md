---
title: 將 B2B 共同作業使用者新增為資訊工作者-Azure AD
description: B2B 共同作業可讓資訊工作者和應用程式擁有者將來賓使用者新增到 Azure AD 進行存取 | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352d5218f91fa55134a7515b6aeee49857847468
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173204"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>組織中的使用者如何將來賓使用者邀請到應用程式

在已將來賓使用者新增到 Azure AD 中的目錄之後，應用程式擁有者就可以將他們想要共用的應用程式直接連結傳送給來賓使用者。 Azure AD 系統管理員也可以設定自助管理，來管理資源庫或其 Azure AD 租用戶中的 SAML 應用程式。 如此一來，應用程式擁有者就可以管理自己的來賓使用者，即使尚未將來賓使用者新增至目錄也一樣。 設定應用程式以進行自助服務時，應用程式擁有者會使用其存取面板來將來賓使用者邀請到應用程式，或將來賓使用者新增到有權存取該應用程式的群組。 資源庫和 SAML 型應用程式的自助式應用程式管理需要由系統管理員進行一些初始設定。以下是安裝步驟的摘要 (如需詳細指示，請參閱此頁面稍後的 [必要條件](#prerequisites)) ：

 - 針對您的租用戶啟用自助群組管理
 - 建立要指派給應用程式的群組，並讓使用者成為擁有者
 - 設定應用程式以進行自助服務，並將群組指派給應用程式

> [!NOTE]
> 本文描述如何設定自助管理，來管理資源庫及新增至 Azure AD 租用戶的 SAML 應用程式。 您也可以 [設定自助 Microsoft 365 群組](../enterprise-users/groups-self-service-management.md) ，讓使用者可以管理自己的 Microsoft 365 群組的存取權。 如需更多使用者可以與來賓使用者共用 Office 檔案和應用程式的方式，請參閱 [Microsoft 365 群組中的來賓存取權](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) ，以及 [共用 SharePoint 檔案或資料夾](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)。

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>從存取面板將來賓使用者邀請到應用程式

設定應用程式以進行自助服務之後，應用程式擁有者就可以使用自己的存取面板，將來賓使用者邀請到他們想要共用的應用程式。 不一定要事先將來賓使用者新增到 Azure AD。 

1. 移至 `https://myapps.microsoft.com` 來開啟您的存取面板。
2. 指向應用程式、選取省略符號 (**...**)，然後選取 [管理應用程式]。
 
   ![顯示 Salesforce 應用程式 [管理應用程式] 子功能表的螢幕擷取畫面](media/add-users-iw/access-panel-manage-app.png)
 
3. 在 [使用者] 清單的頂端，選取 [] **+** 。
   
   ![顯示在應用程式中新增成員之加號符號的螢幕擷取畫面](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. 在 [新增成員] 搜尋方塊中，輸入來賓使用者的電子郵件地址。 您也可以選擇納入歡迎訊息。
   
   ![顯示新增來賓的 [新增成員] 視窗的螢幕擷取畫面](media/add-users-iw/access-panel-invitation.png)
   
5. 選取 [新增]，將邀請傳送給來賓使用者。 在傳送邀請之後，系統就會自動將使用者帳戶以來賓身分新增至目錄。

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>邀請某人加入有權存取應用程式的群組
設定應用程式以進行自助服務之後，應用程式擁有者就可以將來賓使用者邀請到他們所管理且有權存取其想要共用之應用程式的群組。 來賓使用者不需要已經存在於目錄中。 應用程式擁有者會遵循下列步驟來將來賓使用者邀請到群組，使其可存取應用程式。

1. 確定您是自助群組的擁有者且有權存取您想要共用的應用程式。
2. 移至 `https://myapps.microsoft.com` 來開啟您的存取面板。
3. 選取 [群組] 應用程式。
   
   ![顯示存取面板中 [群組] 應用程式的螢幕擷取畫面](media/add-users-iw/access-panel-groups.png)
   
4. 在 [我擁有的群組] 底下，選取有權存取您想要共用之應用程式的群組。
   
   ![螢幕擷取畫面，顯示在我擁有的群組底下選取群組的位置](media/add-users-iw/access-panel-groups-i-own.png)
   
5. 在 [群組成員] 清單的頂端，選取 **+** 。
   
   ![顯示新增成員至群組之加號符號的螢幕擷取畫面](media/add-users-iw/access-panel-groups-add-member.png)
   
6. 在 [新增成員] 搜尋方塊中，輸入來賓使用者的電子郵件地址。 您也可以選擇納入歡迎訊息。
   
   ![顯示新增來賓的 [新增成員] 視窗的螢幕擷取畫面](media/add-users-iw/access-panel-invitation.png)
   
7. 選取 [新增] 即可自動傳送邀請給來賓使用者。 在傳送邀請之後，系統就會自動將使用者帳戶以來賓身分新增至目錄。


## <a name="prerequisites"></a>Prerequisites

自助應用程式管理需要由全域系統管理員和 Azure AD 系統管理員進行一些初始設定。 在此設定過程中，您將會設定應用程式來進行自助服務，並將群組指派給應用程式擁有者可管理的應用程式。 您也可以設定群組以允許每個人都可要求成員資格，但需要群組擁有者的核准 (深入了解[自助群組管理](../enterprise-users/groups-self-service-management.md))。 

> [!NOTE]
> 您無法將來賓使用者新增到動態群組或已與內部部署 Active Directory 同步的群組。

### <a name="enable-self-service-group-management-for-your-tenant"></a>針對您的租用戶啟用自助群組管理
1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com) 。
2. 在瀏覽窗格中，選取 [Azure Active Directory]。
3. 選取 [群組]。
4. 在 [設定] 下，選取 [一般]。
5. 在 [自助群組管理] 下的 [擁有者可在存取面板管理群組成員資格要求] 旁，選取 [是]。
6. 選取 [儲存]。

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>建立要指派給應用程式的群組，並讓使用者成為擁有者
1. 以 Azure AD 系統管理員或全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在瀏覽窗格中，選取 [Azure Active Directory]。
3. 選取 [群組]。
4. 選取 [新增群組]。
5. 在 [群組類型] 下，選取 [安全性]。
6. 輸入 **群組名稱** 與 **群組描述**。
7. 在 [成員資格類型] 下，選取 [已指派]。
8. 選取 [建立]，然後關閉 [群組] 頁面。
9. 在 [群組 - 所有群組] 頁面上，開啟群組。 
10. 在 [管理] 下，選取 [擁有者] > [新增擁有者]。 搜尋應該管理應用程式存取權的使用者。 選取使用者，然後按一下 [選取]。

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>設定應用程式以進行自助服務，並將群組指派給應用程式
1. 以 Azure AD 系統管理員或全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 下選取 [企業應用程式] > [所有應用程式]。
4. 在應用程式清單中，尋找並開啟應用程式。
5. 在 [管理] 下，選取 [單一登入]，然後設定應用程式以進行單一登入 (如需詳細資訊，請參閱[管理企業應用程式的單一登入](../manage-apps/add-application-portal-setup-sso.md))。
6. 在 [管理] 下，選取 [自助]，然後設定自助應用程式存取 (如需詳細資訊，請參閱[如何使用自助應用程式存取](../manage-apps/access-panel-manage-self-service-access.md))。 

    > [!NOTE]
    > 針對 [要將指派的使用者新增至哪個群組呢?] 設定，選取您在上一節中建立的群組。
7. 在 [管理] 下，選取 [使用者和群組]，然後確認您所建立的自助群組出現在清單中。
8. 若要將應用程式新增至群組擁有者的存取面板，請選取 [**新增使用者**  >  **和群組**]。 搜尋群組擁有者並選取使用者、按一下 [選取]，然後按一下 [指派] 以將使用者新增到應用程式。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](add-users-administrator.md)
- [B2B 共同作業邀請兌換](redemption-experience.md)
- [外部身分識別定價](external-identities-pricing.md)