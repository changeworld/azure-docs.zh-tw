---
title: 教學課程︰以 Azure Active Directory 設定 Salesforce 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Salesforce 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77060516"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>教學課程︰設定 Salesforce 來進行自動佈建使用者

本教學課程旨在說明您需要在 Salesforce 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 Salesforce。

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure Active Directory 租用戶
* Salesforce.com 租用戶

> [!IMPORTANT]
> 如果您使用的是 Salesforce.com 試用帳戶，則將無法設定自動化的使用者佈建。 試用帳戶沒有必要的 API 存取權，必須購買之後才會擁有。 您可以使用免費的[開發人員帳戶](https://developer.salesforce.com/signup) \(英文\)，克服這項限制以完成本教學課程。

如果您使用 Salesforce 沙箱環境，請參閱 [Salesforce 沙箱整合教學課程](https://go.microsoft.com/fwLink/?LinkID=521879)。

## <a name="assigning-users-to-salesforce"></a>將使用者指派給 Salesforce

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者或群組需要存取 Salesforce 應用程式。 做好決定之後，即可依照[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示，將這些使用者指派給 Salesforce 應用程式。

### <a name="important-tips-for-assigning-users-to-salesforce"></a>將使用者指派給 Salesforce 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Salesforce，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 Salesforce 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建

    > [!NOTE]
    > 此應用程式在佈建流程中，會從 Salesforce 匯入設定檔，客戶在 Azure AD.中指派使用者時也可以選取該角色。 請注意，從 Salesforce 匯入的設定檔會顯示為 Azure AD 中的角色。

## <a name="enable-automated-user-provisioning"></a>啟用自動使用者佈建

本節會引導您將 Azure AD 連接到[salesforce 的使用者帳戶布建 API-v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)，以及根據 Azure AD 中的使用者和群組指派，設定布建服務以在 Salesforce 中建立、更新和停用指派的使用者帳戶。

> [!Tip]
> 您也可以選擇啟用 Salesforce 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

本節的目的是要說明如何對 Salesforce 啟用 Active Directory 使用者帳戶的使用者佈建。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至 [ **Azure Active Directory > 企業應用程式] > [所有應用程式**] 區段。

2. 如果您已經設定單一登入的 Salesforce，請使用 [搜尋] 欄位搜尋您的 Salesforce 執行個體。 否則，請選取 [新增]****，並在應用程式庫中搜尋 [Salesforce]****。 從搜尋結果中選取 Salesforce，並將它新增至您的應用程式清單。

3. 選取您的 Salesforce 執行個體，然後選取 [佈建]**** 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建](./media/salesforce-provisioning-tutorial/provisioning.png)

5. 在 [管理員認證]**** 區段下，提供下列組態設定：

    a. 在 [管理員使用者名稱]**** 文字方塊中，輸入已獲指派 Salesforce.com 中 [系統管理員]**** 設定檔的 Salesforce 帳戶名稱。

    b. 在 [管理員密碼]**** 文字方塊中，輸入這個帳戶的密碼。

6. 若要取得您的 Salesforce 安全性權杖，請開啟新索引標籤並登入相同的 Salesforce 系統管理員帳戶。 在頁面右上角，按一下您的名稱，然後按一下 [設定]****。

    ![啟用自動使用者佈建](./media/salesforce-provisioning-tutorial/sf-my-settings.png "啟用自動使用者佈建")

7. 在左方導覽窗格上，按一下 [我的個人資訊]**** 以展開相關的區段，然後按一下 [重設我的安全性權杖]****。
  
    ![啟用自動使用者佈建](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "啟用自動使用者佈建")

8. 在 [重設安全性權杖]**** 頁面上，按一下 [重設安全性權杖]**** 按鈕。

    ![啟用自動使用者佈建](./media/salesforce-provisioning-tutorial/sf-reset-token.png "啟用自動使用者佈建")

9. 檢查與此系統管理員帳戶相關聯的電子郵件收件匣。 尋找來自 Salesforce.com，包含新安全性權杖的電子郵件。

10. 複製該權杖，移至您的 Azure AD 視窗，然後將它貼到 [祕密權杖]**** 欄位。

11. 如果 Salesforce 執行個體是位於 Salesforce 政府雲端上，則應輸入**租用戶 URL**。 否則為選擇性。 使用 "HTTPs://. my.salesforce.com" 格式輸入租 \<your-instance\> 使用者 URL， \<your-instance\> 並將取代為您的 salesforce 實例名稱。

12. 在 Azure 入口網站中，按一下 [測試連接]****，以確保 Azure AD 可以連接到您的 Salesforce 應用程式。

13. 在 [通知電子郵件]**** 欄位中輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選下列核取方塊。

14. 按一下 [儲存]。  

15. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Salesforce]****。

16. 在 [屬性對應]**** 區段中，檢閱從 Azure AD 同步至 Salesforce 的使用者屬性。 請注意，選取為 [比對]**** 屬性的屬性會用來比對 Salesforce 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

17. 若要啟用 Salesforce 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態]**** 變更為 [開啟]****

18. 按一下 [儲存]。

> [!NOTE]
> 在 Salesforce 應用程式中布建使用者之後，系統管理員必須為其設定語言特定設定。 如需語言設定的詳細資訊，請參閱[這](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)篇文章。

這會啟動在 [使用者和群組] 區段中指派給 Salesforce 的任何使用者和/或群組之首次同步處理。 請注意，初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料]**** 區段來監視進度，並依循連結前往佈建活動記錄，此記錄會描述您 Salesforce 應用程式上佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="common-issues"></a>常見問題
* 如果您在授權存取 Salesforce 時遇到問題，請確定下列事項：
    * 使用的認證具有 Salesforce 的系統管理員存取權。
    * 您所使用的 Salesforce 版本支援 Web 存取（例如開發人員、企業版、沙箱和無限制的 Salesforce 版本）。
    * 已為使用者啟用 Web API 存取。
* Azure AD 布建服務支援提供使用者的語言、地區設定和時區。 這些屬性是在預設的屬性對應中，但沒有預設的 source 屬性。 請確定您選取 [預設來源] 屬性，而且 [來源] 屬性採用 SalesForce 所預期的格式。 例如，localeSidKey for 英文（美國）是 en_US。 請參閱[此處](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)提供的指引，以判斷適當的 localeSidKey 格式。 您可以在[這裡](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)找到 languageLocaleKey 格式。 除了確保格式正確，您可能需要確定已為您的使用者啟用語言，如[這裡](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)所述。 
* **SalesforceLicenseLimitExceeded：** 無法在目標應用程式中建立使用者，因為此使用者沒有可用的授權。 針對目標應用程式購買額外的授權，或檢查您的使用者指派和屬性對應設定，以確保使用正確的屬性來指派正確的使用者。
* **SalesforceDuplicateUserName：** 無法布建使用者，因為它具有在另一個 Salesforce.com 租使用者中重複的 Salesforce.com ' Username '。在 Salesforce.com 中，' Username ' 屬性的值在所有 Salesforce.com 租使用者中必須是唯一的。根據預設，使用者在 Azure Active Directory 中的 userPrincipalName 會變成其在 Salesforce.com 中的「使用者名稱」。您有兩個選項。其中一個選項是在另一個 Salesforce.com 租使用者中，尋找並重新命名具有重複的「使用者名稱」的使用者（如果您也要管理該其他租使用者）。另一個選項是移除 Azure Active Directory 使用者對您的目錄整合所在的 Salesforce.com 租使用者的存取權。 我們會在下一次嘗試同步處理時重試此操作。 
* **SalesforceRequiredFieldMissing：** Salesforce 要求使用者必須要有某些屬性，才能成功建立或更新使用者。 此使用者缺少其中一個必要的屬性。 確定在您想要布建至 Salesforce 的所有使用者上，都已填入電子郵件和別名等屬性。 您可以使用以[屬性為基礎的範圍篩選器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)，來界定不具有這些屬性的使用者範圍。 
* 布建至 Salesforce 的預設屬性對應包含 SingleAppRoleAssignments 運算式，可將 Azure AD 中的 appRoleAssignments 對應至 Salesforce 中的 ProfileName。 請確定使用者在 Azure AD 中沒有多個應用程式角色指派，因為屬性對應僅支援布建一個角色。 
* Salesforce 需要先手動核准電子郵件更新，然後再進行變更。 因此，您可能會在布建記錄中看到多個專案來更新使用者的電子郵件（直到電子郵件變更核准為止）。


## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [設定單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
