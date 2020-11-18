---
title: 教學課程：以 Azure Active Directory 設定 NetSuite OneWorld 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 NetSuite OneWorld 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: a5b995fd273fcfa231e101bc77b11d268be728fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359130"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>教學課程：設定 NetSuite 來自動佈建使用者

本教學課程旨在說明您需要在 NetSuite OneWorld 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 NetSuite。

> [!NOTE]
> 此整合目前使用基本驗證 (使用者名稱和密碼) 來進行驗證。 NetSuite 已實作多重要素驗證需求來防止客戶使用此整合 (除非其已豁免這項需求)。 我們正與 NetSuite 合作，將此整合更新為較新的驗證方法，以讓沒有豁免的客戶能夠再次使用。 我們將會使用 ETA (一旦可供使用時) 來更新此文件。

建議的動作：請等候我們發行這項整合驗證行為的更新，或聯繫 NetSuite 支援人員，詢問要如何豁免多重要素驗證需求。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶。
*   NetSuite OneWorld 訂用帳戶。 請注意，自動使用者佈建目前只透過 NetSuite OneWorld 提供支援。
*   Netsuite 中具有系統管理員權限的使用者帳戶。
*   與 Azure AD 的整合需要 2FA 豁免。 請連絡 NetSuite 的支援小組以申請此豁免。

## <a name="assigning-users-to-netsuite-oneworld"></a>將使用者指派給 NetSuite OneWorld

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 NetSuite 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 NetSuite 應用程式︰

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>將使用者指派給 NetSuite OneWorld 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 NetSuite，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 NetSuite 時，您必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="enable-user-provisioning"></a>啟用使用者佈建

本節會引導您將 Azure AD 連線至 NetSuite 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 NetSuite 中建立、更新和停用指派的使用者帳戶。

> [!TIP] 
> 建議您選擇啟用 NetSuite 的 SAML 型單一登入，方法是遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-user-account-provisioning"></a>若要設定使用者帳戶佈建：

本節的目的是要說明如何對 NetSuite 啟用 Active Directory 使用者帳戶的使用者佈建。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

1. 如果您已將 NetSuite 設定為單一登入，請使用 [搜尋] 欄位搜尋您的 NetSuite 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [NetSuite]。 從搜尋結果中選取 NetSuite，並將其新增至您的應用程式清單。

1. 選取您的 NetSuite 執行個體，然後選取 [佈建] 索引標籤。

1. 將 [佈建模式]  設定為 [自動]  。 

    ![螢幕擷取畫面：顯示 [NetSuite 佈建] 頁面，並已將 [佈建模式] 設定為 [自動] 以及其他您可以設定的值。](./media/netsuite-provisioning-tutorial/provisioning.png)

1. 在 [管理員認證] 區段下，提供下列組態設定：
   
    a. 在 [管理員使用者名稱] 文字方塊中，輸入已在 NetSuite.com 指派 **系統管理員** 設定檔的 NetSuite 帳戶名稱。
   
    b. 在 [管理員密碼] 文字方塊中，輸入這個帳戶的密碼。
      
1. 在 Azure 入口網站中，按一下 [測試連線]，以確保 Azure AD 可以連線至您的 NetSuite 應用程式。

1. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

1. 按一下 [儲存]。

1. 在 [對應] 區段底下，選取 [將 Azure Active Directory 使用者同步至 NetSuite]。

1. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 NetSuite 的使用者屬性。 請注意，選取為 [比對] 屬性的屬性會用來比對 NetSuite 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

1. 若要對 NetSuite 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]

1. 按一下 [儲存]。

這會對 [使用者和群組] 區段中指派給 NetSuite 的任何使用者和/或群組，啟動首次同步處理。 請注意，初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並依循連結前往佈建活動記錄，此記錄會描述您 NetSuite 應用程式上佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定單一登入](netsuite-tutorial.md)
