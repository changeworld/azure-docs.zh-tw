---
title: 教學課程：在 Azure Active Directory 中設定 SuccessFactors 輸入佈建 | Microsoft Docs
description: 了解如何設定從 SuccessFactors 至 Azure AD 的輸入佈建
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a39f7701d0244eff7f914908a6708dfc14873b35
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954849"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>教學課程：設定從 SAP SuccessFactors 到 Azure AD 的使用者佈建
本教學課程的目標是要說明將員工資料從 SuccessFactors 員工中心佈建到 Azure Active Directory 所需的步驟，以及將電子郵件地址回寫至 SuccessFactors 的選用功能。 

>[!NOTE]
>如果您想要從 SuccessFactors 佈建的使用者是不需要內部部署 AD 帳戶的雲端限定使用者，請使用本教學課程。 如果使用者只需要內部部署 AD 帳戶或 AD 和 Azure AD 帳戶，請參閱[設定 SAP SuccessFactors 至 Active Directory 使用者佈建](sap-successfactors-inbound-provisioning-tutorial.md#overview)的教學課程。 

## <a name="overview"></a>概觀

整合 [Azure Active Directory 使用者佈建服務](../app-provisioning/user-provisioning.md)與 [SuccessFactors 員工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)，以管理使用者的身分識別生命週期。 

Azure AD 使用者佈建服務支援的 SuccessFactors 使用者佈建工作流程，可讓下列人力資源和身分識別生命週期管理案例的自動化：

* **雇用新員工** - 將新員工新增至雲端 SuccessFactors 時，系統會在 Azure Active Directory、Microsoft 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)中自動建立使用者帳戶，並將電子郵件地址回寫至 SuccessFactors。

* **員工屬性和設定檔更新** - 在 SuccessFactors 中更新員工記錄時 (例如姓名、職稱或經理)，系統會在 Azure Active Directory、Microsoft 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)中自動更新其使用者帳戶。

* **員工離職** - 在 SuccessFactors 中將員工設定為離職時，系統會在 Azure Active Directory、Microsoft 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)中自動停用其使用者帳戶。

* **重新雇用員工** - 在 SuccessFactors 中重新雇用員工時，系統會自動重新啟用其舊帳戶或將其重新佈建 (取決於您的喜好設定) 至 Azure Active Directory、Microsoft 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此 SuccessFactors 至 Azure Active Directory 使用者佈建解決方案最適合下列對象：

* 需要以預先建置的雲端式解決方案進行 SuccessFactors 使用者佈建的組織

* 需要將使用者從 SuccessFactors 直接佈建到 Azure Active Directory 的組織

* 需要使用從 [SuccessFactors 員工中心 (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 取得的資料來佈建使用者的組織

* 使用 Microsoft 365 來收發電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節將針對雲端限定的使用者，說明端對端使用者佈建方案架構。 有兩個相關的流程：

* **授權 HR 資料流程 – 從 SuccessFactors 到 Azure Active Directory：** 在此流程中，人員事件 (例如新雇用、調動、解雇) 會先發生在雲端 SuccessFactors 員工中心，然後事件資料會流入 Azure Active Directory。 視事件而定，其可能會在 Azure AD 中產生建立/更新/啟用/停用作業。
* **電子郵件回寫流程 – 從內部部署 Active Directory 到 SuccessFactors：** 在 Azure Active Directory 中完成帳戶建立之後，Azure AD 中產生的電子郵件屬性值或 UPN 就可以寫回 SuccessFactors。

  ![概觀](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 小組在 SuccessFactors 員工中心內執行人員異動 (新進人員/異動人員/離職人員或新雇用/異動/解雇)
2. Azure AD 佈建服務會執行排程好的 SuccessFactors EC 身分識別同步處理作業，並找出需要處理以便與內部部署 Active Directory 同步的變更。
3. Azure AD 佈建服務會判斷變更，並在 Azure AD 中叫用使用者的建立/更新/啟用/停用作業。
4. 如果已設定 [SuccessFactors Writeback 應用程式](sap-successfactors-writeback-tutorial.md)，則會從 Azure AD 擷取使用者的電子郵件地址。 
5. Azure AD 佈建服務會根據所使用的比對屬性，將電子郵件屬性寫回 SuccessFactors。

## <a name="planning-your-deployment"></a>規劃您的部署

設定從 SuccessFactors 到 Azure AD 且由雲端 HR 驅動的使用者佈建，需要許多涵蓋不同層面的規劃，例如：

* 判斷比對識別碼 
* 屬性對應
* 屬性轉換 
* 範圍篩選器

如需有關這些主題的完整指導方針，請參閱[雲端 HR 部署計劃](../app-provisioning/plan-cloud-hr-provision.md)。 請參閱 [SAP SuccessFactors 整合參考](../app-provisioning/sap-successfactors-integration-reference.md)以了解支援的實體、處理詳細資料，以及如何自訂不同 HR 案例的整合。 

## <a name="configuring-successfactors-for-the-integration"></a>設定用於整合的 SuccessFactors

所有 SuccessFactors 佈建連接器大多需要具有適當權限的 SuccessFactors 帳戶認證，才能叫用 SuccessFactors OData API。 本節說明在 SuccessFactors 中建立服務帳戶，並授與適當權限的步驟。 

* [在 SuccessFactors 中建立/識別 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [建立 API 權限角色](#create-an-api-permissions-role)
* [為 API 使用者建立權限群組](#create-a-permission-group-for-the-api-user)
* [將權限角色授與權限群組](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中建立/識別 API 使用者帳戶
請與您的 SuccessFactors 系統管理員小組或實作夥伴合作，在 SuccessFactors 中建立或識別使用者帳戶，以用於叫用 OData API。 在 Azure AD 中設定佈建應用程式時，需要用到此帳戶的使用者名稱和密碼認證。 

### <a name="create-an-api-permissions-role"></a>建立 API 權限角色

1. 使用可存取系統管理中心的使用者帳戶登入 SAP SuccessFactors。
1. 搜尋 [管理權限角色]，然後從搜尋結果中選取 [管理權限角色]。
  ![管理權限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. 從 [權限角色] 清單中，按一下 [新建]。
    > [!div class="mx-imgBorder"]
    > ![建立新的權限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. 為新的權限角色新增 **角色名稱** 和 **描述**。 名稱和描述應該指出這是針對 API 使用權限設定的角色。
    > [!div class="mx-imgBorder"]
    > ![權限角色詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. 在 [權限設定] 底下，按一下 [權限...]，然後向下捲動權限清單，再按一下 [管理整合工具]。 核取 [允許系統管理員透過基本驗證存取 OData API] 方塊。
    > [!div class="mx-imgBorder"]
    > ![管理整合工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. 在相同的方塊中向下捲動，然後選取 [員工中心 API]。 如下所示，新增使用 ODATA API 讀取和使用 ODATA API 編輯的權限。 如果您打算在回寫到 SuccessFactors 的案例中使用相同帳戶，請選取編輯選項。 
    > [!div class="mx-imgBorder"]
    > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. 在相同的權限方塊中，移至 [使用者權限] -> [員工資料] 並檢閱服務帳戶可從 SuccessFactors 租用戶讀取的屬性。 例如，若要從 SuccessFactors 擷取 [使用者名稱] 屬性，請確保已針對此屬性授與 [檢視] 權限。 同樣地，請檢閱檢視權限的每個屬性。 

    > [!div class="mx-imgBorder"]
    > ![員工資料權限](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >如需此佈建應用程式所擷取的屬性完整清單，請參閱 [SuccessFactors 屬性參考](../app-provisioning/sap-successfactors-attribute-reference.md)

1. 按一下 [完成]。 按一下 **[儲存變更]** 。

### <a name="create-a-permission-group-for-the-api-user"></a>為 API 使用者建立權限群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [管理權限群組]，然後從搜尋結果中選取 [管理權限群組]。
    > [!div class="mx-imgBorder"]
    > ![管理權限群組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. 從 [管理權限群組] 視窗中，按一下 [新建]。
    > [!div class="mx-imgBorder"]
    > ![Add new group](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. 為新群組新增群組名稱。 群組名稱應指出這是 API 使用者的群組。
    > [!div class="mx-imgBorder"]
    > ![權限群組名稱](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. 將成員新增至群組。 例如，您可以從 [人員集區] 下拉式功能表中選取 [使用者名稱]，然後輸入將用於整合的 API 帳戶使用者名稱。 
    > [!div class="mx-imgBorder"]
    > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. 按一下 [完成] 即可完成建立權限群組。

### <a name="grant-permission-role-to-the-permission-group"></a>將權限角色授與權限群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [管理權限角色]，然後從搜尋結果中選取 [管理權限角色]。
1. 從 **權限角色清單** 中，選取您為 API 使用權限建立的角色。
1. 在 [將此角色授與...] 底下，按一下 [新增...] 按鈕。
1. 從下拉式功能表中選取 [權限群組...]，然後按一下 [選取...] 來開啟 [群組] 視窗，搜尋並選取上方建立的群組。 
    > [!div class="mx-imgBorder"]
    > ![新增權限群組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. 檢閱授與權限群組的權限角色。 
    > [!div class="mx-imgBorder"]
    > ![權限角色和群組詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. 按一下 **[儲存變更]** 。

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>設定從 SuccessFactors 到 Azure AD 的使用者佈建

本節提供從 SuccessFactors 到 Azure AD 的使用者帳戶佈建步驟。

* [新增佈建連接器應用程式並設定 SuccessFactors 的連線](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第 1 部分：新增佈建連接器應用程式並設定 SuccessFactors 的連線

**若要設定 SuccessFactors 到 Azure AD 的佈建：**

1. 移至 <https://portal.azure.com>

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **SuccessFactors 至 Azure Active Directory 使用者佈建**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式]  設定為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 SuccessFactors API 使用者帳戶的使用者名稱，並附上公司識別碼。 其格式為：**username\@companyID**

   * **系統管理員密碼 –** 輸入 SuccessFactors API 使用者帳戶的密碼。 

   * **租用戶 URL –** 輸入 SuccessFactors OData API 服務端點的名稱。 僅輸入不含 http 或 https 的伺服器主機名稱。 此值應如下所示：**api-server-name.successfactors.com**。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](../app-provisioning/application-provisioning-quarantine-status.md)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請再次檢查 SuccessFactors 認證和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 順利儲存認證之後，[對應] 區段會顯示 [將 SuccessFactors 使用者同步至 Azure Active Directory] 預設對應

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您會設定使用者資料從 SuccessFactors 流向 Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 底下，按一下 [將 SuccessFactors 使用者同步至 Azure Active Directory]。

1. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 Azure AD 的 SuccessFactors 使用者集合範圍。 預設範圍是「SuccessFactors 中的所有使用者」。 範例篩選：

   * 範例：將範圍限定為 personIdExternal 介於 1000000 到 2000000 (不含 2000000) 之間的使用者

      * 屬性：personIdExternal

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議您使用 [來源物件範圍] 底下的範圍篩選，利用幾個來自 SuccessFactors 的測試使用者來測試您的對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

   > [!CAUTION] 
   > 佈建引擎的預設行為是停用/刪除超出範圍的使用者。 這可能不適合您的 SuccessFactors 至 Azure AD 整合。 若要覆寫此預設行為，請參閱[略過刪除超出範圍的使用者帳戶](../app-provisioning/skip-out-of-scope-deletions.md)一文
  
1. 在 [目標物件動作] 欄位中，您可以全域篩選在 Active Directory 上執行的動作。 最常見的動作是 [建立] 和 [更新]。

1. 在 [屬性對應] 區段中，您可以定義個別 SuccessFactors 屬性如何對應至 Active Directory 屬性。

     >[!NOTE]
     >如需應用程式所支援的 SuccessFactors 屬性完整清單，請參閱 [SuccessFactors 屬性參考](../app-provisioning/sap-successfactors-attribute-reference.md)

1. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **直接** – 將 SuccessFactors 屬性的值原封不動地寫入至 AD 屬性

         * **常數** – 將靜態的常數字串值寫入至 AD 屬性

         * **運算式** – 可讓您根據一或多個 SuccessFactors 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **來源屬性** - 來自 SuccessFactors 的使用者屬性

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 SuccessFactors 與 Active Directory 之間的使用者。 此值通常是在 SuccessFactors 的 [人員識別碼] 欄位上設定的，這通常與 Active Directory 中的其中一個 [員工識別碼] 屬性對應。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估任何進一步的比對屬性。

      * **套用此對應**

         * **一律** – 將此對應套用於使用者建立和更新動作

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

1. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

在 SuccessFactors 佈建應用程式設定完成之後，您可以在 Azure 入口網站中開啟佈建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 此作業會啟動初始同步，所需花費的時數會視 SuccessFactors 租用戶中的使用者人數而定。 您可以檢查進度列來追蹤同步週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 

5. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![佈建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [深入了解輸入佈建支援的 SuccessFactors 屬性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [了解如何對 SuccessFactors 設定電子郵件回寫](sap-successfactors-writeback-tutorial.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何設定 SuccessFactors 與 Azure Active Directory 之間的單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [了解如何匯出和匯入您的佈建設定](../app-provisioning/export-import-provisioning-configuration.md)