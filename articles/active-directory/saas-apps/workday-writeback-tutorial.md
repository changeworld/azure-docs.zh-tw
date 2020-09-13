---
title: 教學課程：在 Azure Active Directory 中設定 Workday 回寫 |Microsoft Docs
description: 瞭解如何設定從 Azure AD 到 Workday 的屬性回寫
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 8c76bddc0fae024b0dd2bdd27d6b1e10d71dec71
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017467"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>教學課程：設定從 Azure AD 到 Workday 的屬性回寫
本教學課程的目的是要說明從 Azure AD 到 Workday 的回寫屬性所需執行的步驟。 Workday 回寫布建應用程式支援將值指派給下列 Workday 屬性：
* 公司電子郵件 
* Workday 使用者名稱
* 公司有線電話電話號碼 (包括國家/地區代碼、區碼、數位和分機) 
* Work 有線電話電話號碼主要旗標
* 工作行動電話號碼 (包括國家/地區代碼、區碼、數位) 
* 工作移動主要旗標

## <a name="overview"></a>概觀

將使用 Workday 的輸入布建整合設定為內部 [部署 AD 布建](workday-inbound-tutorial.md) 應用程式或 [workday 來 Azure AD](workday-inbound-cloud-only-tutorial.md) 布建應用程式之後，您可以選擇性地設定 workday 回寫應用程式，以將公司電子郵件和電話號碼等連絡人資訊寫入 Workday。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

這項 Workday 回寫使用者布建解決方案最適合用於：

* 使用 Microsoft 365 的組織想要回寫受 IT 管理的授權屬性 (例如電子郵件地址、使用者名稱和電話號碼) 回 Workday

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中設定整合系統使用者

請參閱 [設定整合系統使用者](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) ，以建立具有取得背景工作資料許可權的 Workday 整合系統使用者帳戶。 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>設定 Azure AD 屬性回寫至 Workday

請遵循下列指示，設定將使用者電子郵件地址和使用者名稱，從 Azure Active Directory 回寫至 Workday。

* [新增回寫連接器應用程式和建立 Workday 連線](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [設定回寫屬性對應](#part-2-configure-writeback-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增回寫連接器應用程式和建立 Workday 連線

**設定 Workday 回寫連接器：**

1. 移至 <https://portal.azure.com>。

2. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **Workday Writeback**，並從資源庫新增該應用程式。

6. 新增應用程式且出現應用程式詳細資料畫面之後，請選取 [佈建]。

7. 將 [佈建模式]  設定為 [自動]。

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 看起來應該像這樣：*username\@contoso4*

   * **管理員密碼** – 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 此值應該看起來像這樣：`https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`，其中會以您的正確租用戶名稱取代 *contoso4*，以及以正確的環境字串取代 *wd3-impl* (如有必要)。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-writeback-attribute-mappings"></a>第 2 部分：設定回寫屬性對應

在本節中，您將設定回寫屬性從 Azure AD 流向 Workday 的方式。 

1. 在 [布建] 索引標籤的 [對應] **底下，按一下**[對應名稱]。

2. 在 [ **來源物件範圍** ] 欄位中，您可以選擇性地篩選 Azure Active Directory 應該是回寫一部分的使用者集合。 預設範圍是「Azure AD 中的所有使用者」。

3. 在 [屬性對應] 區段中，更新相符的識別碼，代表 Azure Active Directory 中儲存了 Workday 人員識別碼或員工識別碼的屬性。 常用的比對方法是將 Workday 人員識別碼或員工識別碼同步至 Azure AD 中的 extensionAttribute1-15，然後在 Azure AD 中使用此屬性再次比對 Workday 的使用者。

4. 通常您會將 Azure AD *userPrincipalName* 屬性對應至 Workday *UserID* 屬性，並將 Azure AD *mail* 屬性對應至 Workday *EmailAddress* 屬性。 

     >[!div class="mx-imgBorder"]
     >![Azure 入口網站](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. 使用下面所共用的指導方針，將電話號碼屬性值從 Azure AD 對應到 Workday。 

     | Workday 電話屬性 | 預期值 | 對應指引 |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | 其輸出為 "true" 或 "false" 字串值的常數或運算式對應。 |
     | WorkphoneLandlineCountryCodeName | [三個字母的 ISO 3166-1 國家/地區代碼](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 常數或運算式對應，其輸出為三個字母的國家/地區代碼。 |
     | WorkphoneLandlineCountryCodeNumber | [國際國家/地區呼叫程式碼](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 常數或運算式對應，其輸出為有效的國家/地區代碼 (沒有 + 符號) 。 |
     | WorkphoneLandlineNumber | 完整的電話號碼，包括區碼 | 對應至 *telephoneNumber* 屬性。 使用 RegEx 來移除空格、括弧和國家/地區代碼。 請參閱以下的範例。 |
     | WorkphoneLandlineExtension | 分機號碼 | 如果 *telephoneNumber* 包含擴充功能，請使用 RegEx 來將值解壓縮。 |
     | WorkphoneMobileIsPrimary | true/false | 輸出為 "true" 或 "false" 字串值的常數對應或運算式對應 |
     | WorkphoneMobileCountryCodeName | [三個字母的 ISO 3166-1 國家/地區代碼](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 常數或運算式對應，其輸出為三個字母的國家/地區代碼。 |
     | WorkphoneMobileCountryCodeNumber | [國際國家/地區呼叫程式碼](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 常數或運算式對應，其輸出為有效的國家/地區代碼 (沒有 + 符號) 。 |
     | WorkphoneMobileNumber | 完整的電話號碼，包括區碼 | 對應至 *行動* 裝置屬性。 使用 RegEx 來移除空格、括弧和國家/地區代碼。 請參閱以下的範例。 |

     > [!NOTE]
     > 當叫用 Change_Work_Contact Workday web 服務時，Azure AD 會傳送下列常數值： <br>
     > * **Communication_Usage_Type_ID** 設定為常數位串 "WORK" <br>
     > * **Phone_Device_Type_ID** 設定為行動電話號碼的常數位串 "mobile"，以及有線電話電話號碼的 "有線電話"。 <br>
     > 
     > 如果您的 Workday 租使用者使用不同的 Type_IDs，您將會遇到回寫失敗。 若要避免這類失敗，您可以使用 Workday **維護參考識別碼** 工作，並更新 Type_IDs 以符合 Azure AD 所使用的值。 <br>
     >  

     **參考 RegEx 運算式-範例1**

     如果 Azure AD 中的電話號碼設定為使用自助式密碼重設所需的格式 (SSPR) ，請使用下面的正則運算式。 <br>
     範例：如果電話號碼值為 + 1 1112223333->，則 RegEx 運算式將輸出1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **參考 RegEx 運算式-範例2**

     如果 Azure AD 中的電話號碼是以 (XXX) XXX 的格式設定，請使用下面的正則運算式。 <br>
     範例：如果電話號碼值是 (111) 222-3333-> 則 RegEx 運算式將輸出1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

在 Workday 佈建應用程式設定完成之後，您可以在 Azure 入口網站中開啟佈建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 這項作業會啟動初始同步，視來原始目錄中的使用者數目而定，這可能需要幾小時的時間。 您可以檢查進度列，以追蹤同步處理週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 Audit 記錄檔會列出布建服務所執行的所有個別同步事件，例如從來源匯入並匯出至目標應用程式的使用者。  

5. 初始同步處理完成後，它 **會在 [布建] 索引** 標籤中寫入摘要報告，如下所示。

     > [!div class="mx-imgBorder"]
     > ![布建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何設定 Workday 與 Azure Active Directory 之間的單一登入](workday-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和匯入布建設定](../app-provisioning/export-import-provisioning-configuration.md)

