---
title: 教學課程：在 Azure Active Directory 中設定 Workday 輸入布建 |Microsoft Docs
description: 瞭解如何設定從 Workday 到 Azure AD 的輸入布建
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: fac4f61e-d942-4429-a297-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 6fb80af84379a1a0bc174a7318c8150a98bea95e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84026499"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>教學課程：設定 Workday 以 Azure AD 使用者布建
本教學課程的目的是要示範將工作者資料從 Workday 布建到 Azure Active Directory 所需執行的步驟。 

>[!NOTE]
>如果您想要從 Workday 布建的使用者是不需要內部部署 AD 帳戶的僅限雲端使用者，請使用本教學課程。 如果使用者只需要內部部署 AD 帳戶或 AD 和 Azure AD 帳戶，請參閱[設定 Workday 以 Active Directory](workday-inbound-tutorial.md)使用者布建教學課程。 

## <a name="overview"></a>概觀

[Azure Active Directory 使用者佈建服務](../app-provisioning/user-provisioning.md)與 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 整合以佈建使用者帳戶。 Azure AD 使用者佈建服務支援的 Workday 使用者佈建工作流程，可讓下列人力資源和身分識別生命週期管理案例的自動化：

* **雇用新員工**-將新員工新增至 workday 時，系統會自動在 Azure Active Directory 中建立使用者帳戶，並選擇性地在 Azure AD 支援的 Office 365 和[其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)中，將電子郵件地址寫回至 Workday。

* **員工屬性和設定檔更新**-員工記錄在 Workday 中更新時（例如其名稱、標題或管理員），其使用者帳戶將會自動更新 Azure Active Directory 並可選擇 Office 365 和[Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **員工終止**-當員工離職時，其使用者帳戶會在 Azure Active Directory 中自動停用，並可選擇 Office 365 和[其他 Azure AD 支援的 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **Employee rehires** -員工在 Workday 中 workday 時，其舊帳戶可以自動重新開機或重新布建（視您的喜好設定而定），以 Azure Active Directory 和選擇性的 Office 365 和[其他 Azure AD 支援的 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此 Workday Azure Active Directory 使用者布建解決方案最適合下列情況：

* 需要以預先建置的雲端式解決方案進行 Workday 使用者佈建的組織

* 需要將使用者從 Workday 直接布建到 Azure Active Directory 的組織

* 需要使用者使用從 Workday 取得的資料進行布建的組織

* 使用 Office 365 處理電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節說明僅限雲端使用者的端對端使用者布建解決方案架構。 有兩個相關的流程：

* **授權 HR 資料流程–從 Workday 到 Azure Active Directory：** 在此流程中，背景工作事件（例如新進員工、轉移、終止）會先發生在 Workday 中，然後事件資料會流入 Azure Active Directory。 視事件而定，它可能會導致在 Azure AD 中建立/更新/啟用/停用作業。
* **回寫流程–從內部部署 Active Directory 到 Workday：** 在 Active Directory 中完成帳戶建立之後，它會透過 Azure AD Connect 與 Azure AD 同步處理，而電子郵件、使用者名稱和電話號碼等資訊則可回寫到 Workday。

  ![概觀](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 小組在 Workday 員工中部執行背景工作交易（權變/移動/權變或新進/轉移/終止）
2. Azure AD 布建服務會從 Workday EC 執行排程的識別同步處理，並識別需要針對與內部部署 Active Directory 同步的變更。
3. Azure AD 布建服務會決定變更，並在 Azure AD 中叫用使用者的建立/更新/啟用/停用作業。
4. 如果已設定[Workday 回寫](workday-writeback-tutorial.md)應用程式，它會從 Azure AD 抓取電子郵件、使用者名稱和電話號碼等屬性。 
5. Azure AD 布建服務會在 Workday 中設定電子郵件、使用者名稱和電話號碼。

## <a name="planning-your-deployment"></a>規劃您的部署

將雲端 HR 驅動的使用者布建從 Workday 設定為 Azure AD 需要大量規劃，涵蓋不同層面，例如：

* 判斷相符的識別碼 
* 屬性對應
* 屬性轉換 
* 範圍篩選器

如需有關這些主題的完整指導方針，請參閱[雲端人力資源部署計畫](../app-provisioning/plan-cloud-hr-provision.md)。 

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中設定整合系統使用者

請參閱[設定整合系統使用者](workday-inbound-tutorial.md#configure-integration-system-user-in-workday)以建立 Workday 整合系統使用者帳戶一節，其具有取得背景工作資料的許可權。 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>設定從 Workday 到 Azure AD 的使用者布建

下列各節說明針對僅限雲端部署，設定將使用者從 Workday 佈建至 Azure AD 的步驟。

* [新增 Azure AD 佈建連接器應用程式和建立 Workday 連線](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [設定 Workday 與 Azure AD 的屬性對應](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增 Azure AD 佈建連接器應用程式和建立 Workday 連接

**若要針對僅限雲端使用者設定 Workday 至 Azure Active Directory 佈建：**

1. 移至 <https://portal.azure.com>。

2. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋**Workday 以 Azure AD 使用者**布建，並從資源庫新增該應用程式。

6. 新增應用程式且出現應用程式詳細資料畫面之後，請選取 [佈建]。

7. 將 [佈建模式]  設定為 [自動]。

8. 完成 [系統管理員認證] 區段，如下所示：

   * **Workday 使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **Workday 密碼** – 輸入 Workday 整合系統帳戶的密碼

   * **Workday Web 服務 API URL –** 輸入租使用者 Workday web 服務端點的 URL。 URL 會決定連接器所使用的 Workday Web 服務 API 版本。 
   
     | URL 格式 | 使用的 WWS API 版本 | 需要 XPATH 變更 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21。1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21。1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #。# | Yes |

      > [!NOTE]
     > 如果 URL 中未指定任何版本資訊，則應用程式會使用 Workday Web 服務（WWS） v 21.1，而且應用程式隨附的預設 XPATH API 運算式不需要任何變更。 若要使用特定的 WWS API 版本，請在 URL 中指定版本號碼 <br>
     > 範例：`https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> 如果您使用 WWS API v 30.0 +，在開啟布建作業之前，請先更新 [屬性對應->] [設定] [選項] 下的 [ **XPATH API 運算式**]-請參閱[管理您](workday-inbound-tutorial.md#managing-your-configuration)的設定和[workday 屬性參考](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)一節中的**Workday > 編輯屬性清單**。  

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。

   * 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>第 2 部分：設定 Workday 與 Azure AD 的屬性對應

在本節中，您會針對僅限雲端使用者設定使用者資料從 Workday 流動至 Azure Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 下，按一下 [將人員同步至 Azure AD]。

2. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 Azure AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅約聘人員和非正式員工

      * 屬性：ContingentID

      * 運算子：IS NOT NULL

3. 在 [目標物件動作] 欄位中，您可以全域篩選在 Azure AD 上執行的動作。 最常見的動作是 [建立] 和 [更新]。

4. 在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

   * **對應類型**

      * **直接** – 將 Workday 屬性的值原封不動地寫入至 AD 屬性

      * **常數** – 將靜態的常數字串值寫入至 AD 屬性

      * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

   * **來源屬性** - 來自 Workday 的使用者屬性。 如果您要尋找的屬性不存在，請參閱[自訂 Workday 使用者屬性的清單](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)。

   * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

   * **目標屬性** – Azure AD 中的使用者屬性。

   * **使用此屬性比對物件** – 是否應該使用此屬性來唯一識別 Workday 與 Azure AD 之間的使用者。 此值通常是在 Workday 的 [Worker ID] \(人員識別碼\) 欄位上設定的，這通常與 Azure AD 中的 [員工識別碼] 屬性 (新的) 或延伸屬性對應。

   * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

   * **套用此對應**

     * **一律** – 將此對應套用於使用者建立和更新動作

     * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。


## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

在 Workday 佈建應用程式設定完成之後，您可以在 Azure 入口網站中開啟佈建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 此作業會啟動初始同步，所需花費的時數會視 Workday 租用戶中的使用者人數而定。 您可以檢查進度列以追蹤同步處理週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 Audit 記錄會列出布建服務所執行的所有個別同步事件，例如哪些使用者從 Workday 讀取，然後新增或更新為 Azure Active Directory。 

5. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![布建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [深入瞭解支援的 Workday 屬性以進行輸入布建](../app-provisioning/workday-attribute-reference.md)
* [瞭解如何設定 Workday 回寫](workday-writeback-tutorial.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何設定 Workday 與 Azure Active Directory 之間的單一登入](workday-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和匯入您的布建設定](../app-provisioning/export-import-provisioning-configuration.md)


