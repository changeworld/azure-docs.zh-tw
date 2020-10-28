---
title: 教學課程：使用 Azure Active Directory 設定 NetSuite OneWorld 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 與 NetSuite OneWorld 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8406ee5647d02cc917a0fdb1daf2355611bb781d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792203"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>教學課程：設定 NetSuite 來自動布建使用者

本教學課程的目的是要說明您需要在 NetSuite OneWorld 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動布建和取消布建到 NetSuite。

> [!NOTE]
> 這種整合目前使用基本驗證進行驗證 (使用者名稱和密碼) 。 NetSuite 已實行多重要素驗證需求，以防止客戶使用這項整合，除非他們有此需求的豁免。 我們正在使用 NetSuite 將此整合更新為較新的驗證方法，讓沒有豁免的客戶可以再次使用它。 我們會將此檔更新為可使用的一份檔。

建議動作：請稍候，我們會針對此整合發行驗證行為的更新，或 NetSuite 支援人員，以查詢多因素驗證需求的豁免。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶。
*   NetSuite OneWorld 訂用帳戶。 請注意，自動使用者佈建目前只透過 NetSuite OneWorld 提供支援。
*   Netsuite 中具有系統管理員權限的使用者帳戶。
*   與 Azure AD 的整合需要2FA 豁免。 請聯絡 NetSuite 的支援小組來申請此豁免。

## <a name="assigning-users-to-netsuite-oneworld"></a>將使用者指派給 NetSuite OneWorld

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用布建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 NetSuite 應用程式存取權的使用者。 一旦決定後，您可以遵循此處的指示，將這些使用者指派給您的 NetSuite 應用程式：

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>將使用者指派給 NetSuite OneWorld 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 NetSuite，以測試布建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 NetSuite 時，您必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="enable-user-provisioning"></a>啟用使用者佈建

本節會引導您將 Azure AD 連接至 NetSuite 的使用者帳戶布建 API，以及根據 Azure AD 中的使用者和群組指派，設定布建服務以在 NetSuite 中建立、更新和停用指派的使用者帳戶。

> [!TIP] 
> 您也可以選擇啟用 NetSuite 的 SAML 型單一 Sign-On，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-user-account-provisioning"></a>若要設定使用者帳戶佈建：

本節的目的是要說明如何讓使用者 Active Directory 使用者帳戶布建到 NetSuite。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至 **Azure Active Directory > 企業應用程式 > 所有應用程式** ] 區段。

1. 如果您已經設定 NetSuite 進行單一登入，請使用 [搜尋] 欄位搜尋您的 NetSuite 實例。 否則，請選取 [ **新增** ]，然後在應用程式庫中搜尋 **NetSuite** 。 從搜尋結果中選取 [NetSuite]，並將它新增至您的應用程式清單。

1. 選取您的 NetSuite 實例，然後 **選取 [布** 建] 索引標籤。

1. 將 [佈建模式]  設定為 [自動]  。 

    ![螢幕擷取畫面顯示 [NetSuite 布建] 頁面，其中布建模式設定為 [自動] 和其他您可以設定的值。](./media/netsuite-provisioning-tutorial/provisioning.png)

1. 在 [管理員認證]  區段下，提供下列組態設定：
   
    a. 在 [ **管理使用者名稱** ] 文字方塊中，輸入已指派 NetSuite.com 中 **系統管理員** 設定檔的 NetSuite 帳戶名稱。
   
    b. 在 [管理員密碼]  文字方塊中，輸入這個帳戶的密碼。
      
1. 在 Azure 入口網站中，按一下 [ **測試連接** ]，以確保 Azure AD 可以連線至 NetSuite 應用程式。

1. 在 [通知電子郵件]  欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

1. 按一下 [儲存]。

1. 在 [對應] 區段下，選取 [ **同步處理 Azure Active Directory 使用者至 NetSuite]。**

1. 在 [ **屬性** 對應] 區段中，檢查從 Azure AD 同步處理到 NetSuite 的使用者屬性。 請注意，選取為 [比對 **] 屬性的屬性會** 用來比對 NetSuite 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

1. 若要啟用 NetSuite 的 Azure AD 布建服務，請在 [設定] 區段中，將布建 **狀態** 變更為 [ **開啟** ]

1. 按一下 [儲存]。

它會針對在 [使用者和群組] 區段中指派給 NetSuite 的任何使用者和/或群組，啟動首次同步處理。 請注意，初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [ **同步處理詳細資料** ] 區段來監視進度，並遵循連結來布建活動記錄，此記錄會描述您 NetSuite 應用程式上的布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定單一登入](netsuite-tutorial.md)
