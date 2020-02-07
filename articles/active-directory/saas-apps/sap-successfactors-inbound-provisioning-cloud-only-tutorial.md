---
title: 教學課程：在 Azure Active Directory 中設定 SuccessFactors 輸入布建 |Microsoft Docs
description: 瞭解如何設定從 SuccessFactors 到 Azure AD 的輸入布建
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063217"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>教學課程：將 SAP SuccessFactors 設定為 Azure AD 使用者布建（預覽）
本教學課程的目的是要說明將背景工作資料從 SuccessFactors 員工中心布建到 Azure Active Directory，並將電子郵件地址的選擇性回寫至 SuccessFactors 所需執行的步驟。 這項整合處於公開預覽狀態，支援從 SuccessFactors Employee Central 抓取超過70個以上的[使用者屬性](../app-provisioning/sap-successfactors-attribute-reference.md)。 

>[!NOTE]
>如果您想要從 SuccessFactors 布建的使用者是不需要內部部署 AD 帳戶的僅限雲端使用者，請使用本教學課程。 如果使用者只需要內部部署 AD 帳戶或 AD 和 Azure AD 帳戶，請參閱[設定 SAP SuccessFactors 以 Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview)使用者布建的教學課程。 

## <a name="overview"></a>概觀

[Azure Active Directory 的使用者](../app-provisioning/user-provisioning.md)布建服務會與[SuccessFactors 員工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)整合，以便管理使用者的身分識別生命週期。 

Azure AD 的使用者布建服務支援的 SuccessFactors 使用者布建工作流程，可自動化下列人力資源和身分識別生命週期管理案例：

* **雇用新員工**-將新員工新增至 SuccessFactors 時，會在 Azure Active Directory 中自動建立使用者帳戶，並選擇性地在 Azure AD 支援的 Office 365 和[其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)中，將電子郵件地址寫回至 SuccessFactors。

* **員工屬性和設定檔更新**-在 SuccessFactors 中更新員工記錄時（例如其名稱、標題或管理員），其使用者帳戶將會自動更新 Azure Active Directory 並可選擇 Office 365 和[Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **員工終止**-當員工在 SuccessFactors 中終止時，其使用者帳戶會在 Azure Active Directory 中自動停用，並可選擇 Office 365 和[其他 Azure AD 支援的 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **Employee rehires** -當員工在 SuccessFactors 中 workday 時，其舊帳戶可以自動重新啟用或重新布建（視您的喜好設定而定），以 Azure Active Directory 和選擇性的 Office 365 和[其他 Azure AD 支援的 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

這 SuccessFactors Azure Active Directory 的使用者布建解決方案非常適合下列情況：

* 想要預先建立的雲端式解決方案來 SuccessFactors 使用者布建的組織

* 需要將使用者從 SuccessFactors 直接布建到 Azure Active Directory 的組織

* 需要使用者使用從[SuccessFactors 員工中心（EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)取得之資料進行布建的組織

* 使用 Office 365 處理電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節說明僅限雲端使用者的端對端使用者布建解決方案架構。 有兩個相關的流程：

* **授權的 HR 資料流程–從 SuccessFactors 到 Azure Active Directory：** 在此流程中，背景工作事件（例如新進員工、轉移、終止）會先發生在雲端 SuccessFactors 員工中心，然後事件資料會流入 Azure Active Directory。 視事件而定，它可能會導致在 Azure AD 中建立/更新/啟用/停用作業。
* **電子郵件回寫流程–從內部部署 Active Directory 到 SuccessFactors：** 在 Azure Active Directory 中完成帳戶建立之後，Azure AD 中產生的電子郵件屬性值或 UPN 就可以寫回 SuccessFactors。

  ![概觀](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 小組會在 SuccessFactors Employee Central 中執行背景工作交易（權變/移動/權變或新進/轉移/終止）
2. Azure AD 布建服務會從 SuccessFactors EC 執行排程的識別同步處理，並識別需要針對與內部部署 Active Directory 同步的變更。
3. Azure AD 布建服務會決定變更，並在 Azure AD 中叫用使用者的建立/更新/啟用/停用作業。
4. 如果已設定[SuccessFactors 回寫應用程式](sap-successfactors-writeback-tutorial.md)，則會從 Azure AD 取出使用者的電子郵件地址。 
5. Azure AD 布建服務會根據所使用的相符屬性，將電子郵件屬性寫回 SuccessFactors。

## <a name="planning-your-deployment"></a>規劃您的部署

將雲端 HR 驅動的使用者布建從 SuccessFactors 設定為 Azure AD 需要大量規劃，涵蓋不同層面，例如：

* 判斷相符的識別碼 
* 屬性對應
* 屬性轉換 
* 範圍篩選器

如需有關這些主題的完整指導方針，請參閱[雲端人力資源部署計畫](../app-provisioning/plan-cloud-hr-provision.md)。 

## <a name="configuring-successfactors-for-the-integration"></a>設定整合的 SuccessFactors

所有 SuccessFactors 布建連接器的常見需求是，它們需要具有適當許可權的 SuccessFactors 帳號憑證，才能叫用 SuccessFactors OData Api。 本節說明在 SuccessFactors 中建立服務帳戶，並授與適當許可權的步驟。 

* [在 SuccessFactors 中建立/識別 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [建立 API 許可權角色](#create-an-api-permissions-role)
* [為 API 使用者建立許可權群組](#create-a-permission-group-for-the-api-user)
* [將許可權角色授與許可權群組](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中建立/識別 API 使用者帳戶
請與您的 SuccessFactors 系統管理員小組或實施合作夥伴合作，在 SuccessFactors 中建立或識別將用來叫用 OData Api 的使用者帳戶。 在 Azure AD 中設定布建應用程式時，將需要此帳戶的使用者名稱和密碼認證。 

### <a name="create-an-api-permissions-role"></a>建立 API 許可權角色

* 使用可存取系統管理中心的使用者帳戶登入 SAP SuccessFactors。
* 搜尋 [*管理許可權角色*]，然後從搜尋結果中選取 [**管理許可權角色**]。
  ![管理許可權角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 從 [許可權角色] 清單中 **，按一下 [新建]** 。
  > [!div class="mx-imgBorder"]
  > ![建立新的許可權角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 為新的許可權角色新增**角色名稱**和**描述**。 [名稱] 和 [描述] 應該表示角色是用於 API 許可權。
  > [!div class="mx-imgBorder"]
  > ![許可權角色詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 在 [許可權設定] 底下，按一下 [**許可權 ...** ]，然後向下流覽許可權清單，再按一下 [**管理整合工具**]。 核取 [**允許系統管理員透過基本驗證存取 ODATA API**] 的核取方塊。
  > [!div class="mx-imgBorder"]
  > ![管理整合工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 在相同的方塊中向下滾動，然後選取 [**員工中心 API**]。 如下所示新增許可權，以閱讀使用 ODATA API 和使用 ODATA API 進行編輯。 如果您打算將相同的帳戶用於回寫至 SuccessFactors 案例，請選取 [編輯] 選項。 
  > [!div class="mx-imgBorder"]
  > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* 按一下 [完成]。 按一下 **[儲存變更]** 。

### <a name="create-a-permission-group-for-the-api-user"></a>為 API 使用者建立許可權群組

* 在 SuccessFactors 系統管理中心中，搜尋 [*管理許可權群組*]，然後從搜尋結果中選取 [**管理許可權群組**]。
  > [!div class="mx-imgBorder"]
  > ![管理許可權群組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 從 [管理許可權群組] 視窗中 **，按一下 [新建]** 。
  > [!div class="mx-imgBorder"]
  > ![新增群組](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 為新群組新增組名。 組名應該表示群組適用于 API 使用者。
  > [!div class="mx-imgBorder"]
  > ![許可權組名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 將成員新增至群組。 例如，您可以從 [人員集區] 下拉式功能表中選取 [使用者**名稱**]，然後輸入將用於整合之 API 帳戶的使用者名稱。 
  > [!div class="mx-imgBorder"]
  > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* 按一下 [**完成**] 以完成建立許可權群組。

### <a name="grant-permission-role-to-the-permission-group"></a>將許可權角色授與許可權群組

* 在 SuccessFactors 系統管理中心中，搜尋 [*管理許可權角色*]，然後從搜尋結果中選取 [**管理許可權角色**]。
* 從 [**許可權角色] 清單**中，選取您為 [API 許可權] 建立的角色。
* 在 **[將此角色授與 ...** ] 底下，按一下 [**新增 ...** ] 按鈕。
* 從下拉式功能表中選取 [**許可權群組 ...** ]，然後按一下 [**選取 ...** ] 開啟 [群組] 視窗，以搜尋並選取上方建立的群組。 
  > [!div class="mx-imgBorder"]
  > ![新增許可權群組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 檢查許可權群組的許可權角色授與。 
  > [!div class="mx-imgBorder"]
  > ![許可權角色和群組詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* 按一下 **[儲存變更]** 。

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>設定從 SuccessFactors 到 Azure AD 的使用者布建

本節提供從 SuccessFactors 到 Azure AD 的使用者帳戶布建步驟。

* [新增布建連接器應用程式並設定 SuccessFactors 的連線能力](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：新增布建連接器應用程式並設定 SuccessFactors 的連線能力

**若要將 SuccessFactors 設定為 Azure AD 布建：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋**SuccessFactors Azure Active Directory 使用者**布建，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. **將布**建**模式**變更為**自動**

8. 完成 [系統管理員認證] 區段，如下所示：

   * **管理員使用者名稱**–輸入 SuccessFactors API 使用者帳戶的使用者名稱，並附上公司識別碼。 其格式為： **username\@companyID**

   * **管理員密碼–** 輸入 SuccessFactors API 使用者帳戶的密碼。 

   * **租使用者 URL –** 輸入 SuccessFactors OData API 服務端點的名稱。 僅輸入不含 HTTP 或 HTTPs 的伺服器主機名稱。 此值應如下所示： **api-server-name.successfactors.com**。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請再次檢查 SuccessFactors 認證和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 成功儲存認證後，對應**區段會**顯示預設的 將**SuccessFactors 使用者同步處理至 Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您將設定使用者資料從 SuccessFactors 流動到 Active Directory 的方式。

1. 在 [對應] 底下的 [布建] 索引標籤**上，按一下**[**同步處理 SuccessFactors Azure Active Directory 使用者**]

1. 在 [**來源物件範圍**] 欄位中，您可以藉由定義一組以屬性為基礎的篩選，來選取 SuccessFactors 中的哪些使用者集合應該在布建到 Azure AD 的範圍中。 預設範圍是「SuccessFactors 中的所有使用者」。 範例篩選：

   * 範例： personIdExternal 介於1000000和2000000之間的使用者範圍（不包括2000000）

      * 屬性： personIdExternal

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議使用 [**來源物件範圍**] 底下的範圍篩選器，透過 SuccessFactors 測試幾個測試使用者的對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

   > [!CAUTION] 
   > 布建引擎的預設行為是停用/刪除超出範圍的使用者。 這可能不適合您的 SuccessFactors Azure AD 整合。 若要覆寫此預設行為，請參閱[略過刪除超出範圍的使用者帳戶一](../app-provisioning/skip-out-of-scope-deletions.md)文
  
1. 在 [目標物件動作] 欄位中，您可以全域篩選在 Active Directory 上執行的動作。 最常見的動作是 [建立] 和 [更新]。

1. 在 [**屬性**對應] 區段中，您可以定義個別 SuccessFactors 屬性對應至 Active Directory 屬性的方式。

  >[!NOTE]
  >如需應用程式所支援 SuccessFactors 屬性的完整清單，請參閱[SuccessFactors 屬性參考](../app-provisioning/sap-successfactors-attribute-reference.md)


1. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **Direct** –將 SuccessFactors 屬性的值寫入 AD 屬性，而不進行任何變更

         * **常數** – 將靜態的常數字串值寫入至 AD 屬性

         * **Expression** –可讓您根據一或多個 SuccessFactors 屬性，將自訂值寫入至 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **來源屬性**-來自 SuccessFactors 的使用者屬性

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **使用此屬性**比對物件–是否應該使用此對應來唯一識別 SuccessFactors 和 Active Directory 之間的使用者。 這個值通常是在 SuccessFactors 的 [背景工作識別碼] 欄位上設定，這通常會對應至 Active Directory 中的其中一個 [員工識別碼] 屬性。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估任何進一步的比對屬性。

      * **套用此對應**

         * **一律** – 將此對應套用於使用者建立和更新動作

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

1. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

SuccessFactors 布建應用程式設定完成之後，您就可以在 Azure 入口網站中開啟布建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案]。

3. 這項作業會啟動初始同步處理，這可能會花費數小時的時間，視 SuccessFactors 租使用者中的使用者人數而定。 您可以檢查進度列以追蹤同步處理週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 

5. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![布建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [深入瞭解輸入布建的支援 SuccessFactors 屬性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [瞭解如何設定電子郵件回寫至 SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [瞭解如何設定 SuccessFactors 與 Azure Active Directory 之間的單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和匯入您的布建設定](../app-provisioning/export-import-provisioning-configuration.md)


