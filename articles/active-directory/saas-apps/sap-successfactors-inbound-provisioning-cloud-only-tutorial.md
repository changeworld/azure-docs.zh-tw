---
title: 教程：在 Azure 活動目錄中配置成功因素入站預配 |微軟文檔
description: 瞭解如何配置從成功因數到 Azure AD 的入站預配
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063217"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>教程：將 SAP 成功因數配置為 Azure AD 使用者預配（預覽）
本教程的目標是顯示需要執行的步驟，以將工作輔助因素員工中心的工作資料預配到 Azure 活動目錄中，並選擇性地將電子郵件地址寫回到成功因數。 此集成處於公共預覽版中，支援從 SuccessFactors 員工中心檢索[70 多個使用者屬性](../app-provisioning/sap-successfactors-attribute-reference.md)。 

>[!NOTE]
>如果要從 SuccessFactors 預配的使用者是不需要本地 AD 帳戶的僅雲使用者，請使用本教程。 如果使用者只需要本地 AD 帳戶或 AD 和 Azure AD 帳戶，請參閱有關將 SAP[成功因數配置為活動目錄](sap-successfactors-inbound-provisioning-tutorial.md#overview)使用者預配的教程。 

## <a name="overview"></a>總覽

[Azure 活動目錄使用者預配服務](../app-provisioning/user-provisioning.md)與[成功因素員工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理使用者的標識生命週期。 

Azure AD 使用者預配服務支援的 SuccessFactors 使用者預配工作流支援以下人力資源和身份生命週期管理方案的自動化：

* **雇用新員工**- 當新員工添加到成功因數時，將自動在 Azure 活動目錄中創建使用者帳戶，並可以選擇 Office 365 和[Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)，並將電子郵件地址寫回到成功因數。

* **員工屬性和設定檔更新**- 在成功因數中更新員工記錄（如其名稱、標題或管理器）時，其使用者帳戶將自動更新 Azure 活動目錄，並可以選擇 Office 365[和 Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **員工終止**- 當員工在成功因數中終止時，其使用者帳戶將自動在 Azure 活動目錄中禁用，並且可以選擇 Office 365 和[Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

* **員工重新雇用**- 當員工在 SuccessFactors 中重新雇用時，其舊帳戶可以自動重新啟動或重新預配（具體取決於您的偏好）到 Azure 活動目錄，並可以選擇 Office 365[和 Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此成功因素到 Azure 活動目錄使用者預配解決方案非常適合：

* 希望為成功因素使用者預配構建的預構建基於雲的解決方案的組織

* 需要從成功因數直接使用者預配到 Azure 活動目錄的組織

* 需要使用從[成功因素員工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)獲得的資料預配使用者的組織

* 使用 Office 365 處理電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節介紹僅雲使用者的端到端使用者預配解決方案體系結構。 有兩個相關的流程：

* **權威 HR 資料流程 – 從成功因素到 Azure 活動目錄：** 在此流工作執行緒事件（如新員工、轉移、終止）首先發生在雲成功因素員工中心，然後事件資料流程入 Azure 活動目錄。 根據事件的不同，它可能導致在 Azure AD 中創建/更新/啟用/禁用操作。
* **電子郵件回寫流程 – 從本地活動目錄到成功因素：** 在 Azure 活動目錄中完成帳戶創建後，在 Azure AD 中生成的電子郵件屬性值或 UPN 可以編寫回成功因數。

  ![總覽](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 團隊在成功因素員工中心執行員工事務（加入者/移動者/離職者或新員工/調動/終止）
2. Azure AD 預配服務運行來自成功因數 EC 的標識的計畫同步，並標識需要處理的更改才能與本地活動目錄同步。
3. Azure AD 預配服務確定更改，並調用 Azure AD 中的使用者的創建/更新/啟用/禁用操作。
4. 如果配置了[成功因數回寫應用](sap-successfactors-writeback-tutorial.md)，則從 Azure AD 檢索使用者的電子郵件地址。 
5. Azure AD 預配服務根據使用的匹配屬性將電子郵件屬性寫回成功因數。

## <a name="planning-your-deployment"></a>規劃您的部署

將雲 HR 驅動的使用者配置從成功因數配置到 Azure AD 需要大量規劃，涵蓋不同方面，例如：

* 確定匹配的 ID 
* 屬性對應
* 屬性轉換 
* 範圍篩選器

有關有關這些主題的全面指南，請參閱[雲 HR 部署計畫](../app-provisioning/plan-cloud-hr-provision.md)。 

## <a name="configuring-successfactors-for-the-integration"></a>為集成配置成功因素

所有成功因數預配連接器的一個常見要求是，它們需要具有正確許可權的 SuccessFactors 帳戶的憑據來調用成功因數 OData API。 本節介紹在成功因數中創建服務帳戶並授予適當許可權的步驟。 

* [在成功因素中創建/標識 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [創建 API 許可權角色](#create-an-api-permissions-role)
* [為 API 使用者創建許可權組](#create-a-permission-group-for-the-api-user)
* [向許可權組授予許可權角色](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在成功因素中創建/標識 API 使用者帳戶
與您的成功因素管理團隊或實施合作夥伴合作，在成功因數中創建或標識將用於調用 OData API 的使用者帳戶。 在 Azure AD 中配置預配應用時，需要此帳戶的使用者名和密碼憑據。 

### <a name="create-an-api-permissions-role"></a>創建 API 許可權角色

* 使用有權訪問管理中心的使用者帳戶登錄到 SAP 成功因數。
* 搜索*管理許可權角色*，然後從搜尋結果中選擇 **"管理許可權角色**"。
  ![管理許可權角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 從"許可權角色清單"中，按一下"**新建**"。
  > [!div class="mx-imgBorder"]
  > ![創建新的許可權角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 為新的許可權角色添加**角色名稱**和**說明**。 名稱和說明應指示該角色用於 API 使用許可權。
  > [!div class="mx-imgBorder"]
  > ![許可權角色詳細資訊](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 在"許可權設置"下，按一下"**許可權..."，** 然後向下滾動許可權清單，然後按一下"**管理集成工具**"。 選中"**允許管理員通過基本驗證訪問 OData API"** 核取方塊。
  > [!div class="mx-imgBorder"]
  > ![管理集成工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 在同一框中向下滾動並選擇 **"員工中心 API**"。 添加許可權，如下所示，以便使用 ODATA API 進行讀取並使用 ODATA API 進行編輯。 如果您計畫對"向成功因素"方案使用相同的帳戶，請選擇編輯選項。 
  > [!div class="mx-imgBorder"]
  > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* 按一下 [完成]****。 按一下 [儲存變更]****。

### <a name="create-a-permission-group-for-the-api-user"></a>為 API 使用者創建許可權組

* 在成功因數管理中心中，搜索*管理許可權組*，然後從搜尋結果中選擇 **"管理許可權組**"。
  > [!div class="mx-imgBorder"]
  > ![管理許可權組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 在"管理許可權組"視窗中，按一下"**新建**"。
  > [!div class="mx-imgBorder"]
  > ![Add new group](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 為新組添加組名稱。 組名稱應指示組適用于 API 使用者。
  > [!div class="mx-imgBorder"]
  > ![許可權組名稱](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 將成員添加到組。 例如，您可以從"人池"下拉式功能表中選擇**使用者名**，然後輸入將用於集成的 API 帳戶的使用者名。 
  > [!div class="mx-imgBorder"]
  > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* 按一下 **"完成"** 以完成創建許可權組。

### <a name="grant-permission-role-to-the-permission-group"></a>向許可權組授予許可權角色

* 在成功因數管理中心中，搜索*管理許可權角色*，然後從搜尋結果中選擇 **"管理許可權角色**"。
* 從**許可權角色清單中**，選擇為 API 使用許可權創建的角色。
* 在 **"將此角色授予..."** 下，按一下"**添加..."** 按鈕。
* 從下拉式功能表中選擇 **"許可權組..."，** 然後按一下"**選擇..."** 以打開"組"視窗以搜索並選擇上面創建的組。 
  > [!div class="mx-imgBorder"]
  > ![添加許可權組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 查看許可權組授予的許可權角色。 
  > [!div class="mx-imgBorder"]
  > ![許可權角色和組詳細資訊](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* 按一下 [儲存變更]****。

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>將使用者預配從成功因數配置到 Azure AD

本節提供從成功因數到 Azure AD 的使用者帳戶預配步驟。

* [添加預配連接器應用並將連接配置為成功因素](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第 1 部分：添加預配連接器應用並將連接配置為成功因素

**要將成功因素配置為 Azure AD 預配：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]****

3. 依序選取 [企業應用程式]**** 和 [所有應用程式]****。

4. 選取 [新增應用程式]****，然後選取 [全部]**** 類別。

5. 搜索**成功因數到 Azure 活動目錄使用者預配**，並從庫中添加該應用。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]****

7. 將 [佈建模式]**** **** 變更為 [自動]****

8. 完成 [系統管理員認證]**** 區段，如下所示：

   * **管理員使用者名**– 輸入成功因素 API 使用者帳戶的使用者名，並附上公司 ID。 它有格式：**\@使用者名公司ID**

   * **管理員密碼 |** 輸入成功因素 API 使用者帳戶的密碼。 

   * **租戶 URL |** 輸入成功因素 OData API 服務終結點的名稱。 僅輸入沒有 HTTP 或 HTTPs 的伺服器主機名稱。 此值應如下所示 **：api-server-name.successfactors.com**。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線]**** 按鈕。 如果連線測試成功，請按一下頂端的 [儲存]**** 按鈕。 如果失敗，請仔細檢查成功因數憑據和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 成功保存憑據後，"**映射"** 部分將顯示預設映射**將成功因數使用者同步到 Azure 活動目錄**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您將配置使用者資料如何從成功因數流向活動目錄。

1. 在 **"映射**"下的預配選項卡上，按一下"**將成功因數使用者同步到 Azure 活動目錄**"。

1. 在 **"源物件範圍"** 欄位中，可以通過定義一組基於屬性的篩選器，選擇 SuccessFactors 中哪些使用者集應位於預配到 Azure AD 的範圍內。 預設範圍為"成功因數中的所有使用者"。 範例篩選：

   * 示例：在 1000000 到 200000 之間（不包括 20000000）之間的使用者 Id 外部範圍

      * 屬性：人 Id 外部

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議使用**源物件作用域**下的範圍篩選器與成功因數中的幾個測試使用者一起測試映射。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

   > [!CAUTION] 
   > 預配引擎的預設行為是禁用/刪除超出範圍的使用者。 在"成功因素"到 Azure AD 集成中，這可能不可取。 要重寫此預設行為，請參閱跳過[刪除超出範圍的使用者帳戶](../app-provisioning/skip-out-of-scope-deletions.md)的文章
  
1. 在 [目標物件動作]**** 欄位中，您可以全域篩選在 Active Directory 上執行的動作。 **創建**和**更新**是最常見的。

1. 在 **"屬性對應"** 部分中，您可以定義各個成功因素屬性如何映射到活動目錄屬性。

  >[!NOTE]
  >有關應用程式支援的成功因數屬性的完整清單，請參閱[成功因素屬性參考](../app-provisioning/sap-successfactors-attribute-reference.md)


1. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應]**** 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **直接**= 將成功因數屬性的值寫入 AD 屬性，無需更改

         * **常數** – 將靜態的常數字串值寫入至 AD 屬性

         * **運算式**– 允許您根據一個或多個成功因數屬性為 AD 屬性寫入自訂值。 [如需詳細資訊，請參閱這篇有關運算式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **源屬性**- 成功因數中的使用者屬性

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **匹配使用此屬性的物件**– 是否應使用此映射在成功因數和活動目錄之間唯一標識使用者。 此值通常設置在成功因數的工作 ID 欄位中，該欄位通常映射到活動目錄中的員工 ID 屬性之一。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估任何進一步的比對屬性。

      * **套用此對應**

         * **始終**= 在使用者創建和更新操作上應用此映射

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

1. 要保存映射，請按一下屬性對應部分頂部的 **"保存**"。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

成功因數預配應用配置完成後，可以在 Azure 門戶中打開預配服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍]**** 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建]**** 索引標籤中，將 [佈建狀態]**** 設定為 [開啟]****。

2. 按一下 [儲存]****。

3. 此操作將啟動初始同步，這可能需要可變小時數，具體取決於成功因數租戶中的使用者數。 您可以檢查進度條以跟蹤同步週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄]**** 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 

5. 在初始同步完成之後，它會在 [佈建]**** 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![預配進度條](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [瞭解有關支援用於入站預配的成功因素屬性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [瞭解如何將電子郵件回寫配置到成功因素](sap-successfactors-writeback-tutorial.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [瞭解如何在成功因數和 Azure 活動目錄之間配置單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和導入預配配置](../app-provisioning/export-import-provisioning-configuration.md)


