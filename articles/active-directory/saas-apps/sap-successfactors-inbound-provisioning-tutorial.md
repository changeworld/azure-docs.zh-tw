---
title: 教程：在 Azure 活動目錄中配置成功因素入站預配 |微軟文檔
description: 瞭解如何從成功因數配置入站預配
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249681"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>教程：將 SAP 成功因數配置為活動目錄使用者預配（預覽）
本教程的目標是通過可選的電子郵件地址寫回成功因數，顯示需要執行的步驟，以將使用者從成功因素員工中心預配到活動目錄 （AD） 和 Azure AD。 此集成處於公共預覽版中，支援從 SuccessFactors 員工中心檢索[70 多個使用者屬性](../app-provisioning/sap-successfactors-attribute-reference.md)。

>[!NOTE]
>如果要從 SuccessFactors 預配的使用者需要本地 AD 帳戶，並且可選需要 Azure AD 帳戶，請使用本教程。 如果成功因數的使用者只需要 Azure AD 帳戶（僅限雲的使用者），請參閱有關[將 SAP 成功因數配置為 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)使用者預配的教程。 


## <a name="overview"></a>總覽

[Azure 活動目錄使用者預配服務](../app-provisioning/user-provisioning.md)與[成功因素員工中心](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)集成，以便管理使用者的標識生命週期。 

Azure AD 使用者預配服務支援的 SuccessFactors 使用者預配工作流支援以下人力資源和身份生命週期管理方案的自動化：

* **雇用新員工**- 將新員工添加到成功因數時，將自動在活動目錄、Azure 活動目錄和 Azure AD 支援的其他 Office 365[和其他 SaaS 應用程式中](../app-provisioning/user-provisioning.md)創建使用者帳戶，並將電子郵件地址寫回到成功因數。

* **員工屬性和設定檔更新**- 在成功因數中更新員工記錄（如其名稱、標題或管理器）時，他們的使用者帳戶將自動更新在活動目錄、Azure 活動目錄以及可選的 Office 365 和[Azure AD 支援的其他 SaaS 應用程式中](../app-provisioning/user-provisioning.md)。

* **員工終止**- 當員工在成功因數中終止時，其使用者帳戶將自動禁用在活動目錄、Azure 活動目錄中，以及 Azure AD 支援的其他 Office 365[和其他 SaaS 應用程式中](../app-provisioning/user-provisioning.md)。

* **員工重新雇用**- 當員工在 SuccessFactors 中重新雇用時，其舊帳戶可以自動重新啟動或重新預配（具體取決於您的偏好）到活動目錄、Azure 活動目錄以及可選的 Office 365 和[Azure AD 支援的其他 SaaS 應用程式](../app-provisioning/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此成功因數到活動目錄使用者預配解決方案非常適合：

* 希望為成功因素使用者預配構建的預構建基於雲的解決方案的組織

* 需要從成功因數直接使用者預配到活動目錄的組織

* 需要使用從[成功因素員工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)獲得的資料預配使用者的組織

* 需要加入、移動和離開使用者才能僅根據[成功因素員工中心 （EC）](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)中檢測到的更改資訊將使用者同步到一個或多個活動目錄林、域和 O 的組織

* 使用 Office 365 處理電子郵件的組織

## <a name="solution-architecture"></a>方案架構

本節針對常見的混合式環境，說明端對端使用者佈建方案架構。 有兩個相關的流程：

* **權威 HR 資料流程 – 從成功因素到本地活動目錄：** 在此流工作執行緒事件（如新員工、轉移、終止）首先發生在雲成功因素員工中心，然後事件資料通過 Azure AD 和預配代理流入本地活動目錄。 視事件而定，它可能會在 AD 中產生建立/更新/啟用/停用作業。
* **電子郵件回寫流程 – 從本地活動目錄到成功因素：** 在活動目錄中完成帳戶創建後，將通過 Azure AD 連接同步與 Azure AD 同步，電子郵件屬性可以寫回成功因數。

  ![總覽](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 團隊在成功因素員工中心執行員工事務（加入者/移動者/離職者或新員工/調動/終止）
2. Azure AD 預配服務運行來自成功因數 EC 的標識的計畫同步，並標識需要處理的更改才能與本地活動目錄同步。
3. Azure AD 預配服務調用本地 Azure AD 連接預配代理，其中包含 AD 帳戶創建/更新/啟用/禁用操作的請求有效負載。
4. Azure AD Connect 佈建代理程式使用服務帳戶來新增/更新 AD 帳戶資料。
5. Azure AD 連接同步引擎運行增量同步以在 AD 中提取更新。
6. Active Directory 會與 Azure Active Directory 同步更新。
7. 如果配置了[成功因數回寫應用](sap-successfactors-writeback-tutorial.md)，它將基於使用的匹配屬性將電子郵件屬性寫回成功因數。

## <a name="planning-your-deployment"></a>規劃您的部署

將雲 HR 驅動的使用者配置從成功因數配置到 AD 需要大量規劃，涵蓋不同方面，例如：
* Azure AD 連接預配代理的設置 
* 要部署的 AD 使用者預配應用的成功因數數
* 匹配 ID、屬性對應、轉換和範圍篩選器

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

  >[!NOTE]
  >有關此預配應用檢索的屬性的完整清單，請參閱[成功因素屬性引用](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>將使用者預配從成功因數配置到活動目錄

本節提供從成功因數到集成範圍內每個活動目錄域的使用者帳戶預配的步驟。

* [添加預配連接器應用並下載預配代理](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [安裝並設定內部部署佈建代理程式](#part-2-install-and-configure-on-premises-provisioning-agents)
* [配置與成功因數和活動目錄的連接](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [設定屬性對應](#part-4-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>第 1 部分：添加預配連接器應用並下載預配代理

**要將成功因數配置為活動目錄預配：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]****

3. 依序選取 [企業應用程式]**** 和 [所有應用程式]****。

4. 選取 [新增應用程式]****，然後選取 [全部]**** 類別。

5. 搜索**成功因數到活動目錄使用者預配**，並從庫中添加該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]****

7. 將 [佈建模式]**** **** 變更為 [自動]****

8. 按一下顯示的資訊橫幅以下載預配代理。 
   > [!div class="mx-imgBorder"]
   > ![下載代理](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "下載代理螢幕")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>第 2 部分：安裝和配置本地預配代理

要預配到本地活動目錄，必須在具有 .NET 4.7.1+ 框架的伺服器上安裝預配代理，並且對所需的活動目錄域具有網路存取權限。

> [!TIP]
> 您可以使用[這裡](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的指示，檢查您伺服器上的 .NET Framework 版本。
> 如果伺服器未安裝 .NET 4.7.1 或更新版本，您可以從[這裡](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)下載。  

將下載的代理安裝程式傳輸到伺服器主機，然後按照以下步驟完成代理配置。

1. 登錄到要安裝新代理的 Windows 伺服器。

1. 啟動預配代理安裝程式，同意條款，然後按一下 **"安裝**"按鈕。

   ![安裝螢幕](./media/workday-inbound-tutorial/pa_install_screen_1.png "安裝螢幕")
   
1. 安裝完成之後，精靈將會啟動，而您將會看到 [連線 Azure AD]**** 畫面。 按一下 [驗證]**** 按鈕以連線到您的 Azure AD 執行個體。

   ![Connect Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "連線 Azure AD")
   
1. 使用全域管理員認證驗證您的 Azure AD 執行個體。

   ![管理員 Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理員 Auth")

   > [!NOTE]
   > Azure AD 系統管理員認證僅供用來連線至您的 Azure AD 租用戶。 代理程式不會將認證儲存在本機伺服器上。

1. 成功使用 Azure AD 驗證之後，您會看到 [連線 Active Directory]**** 畫面。 在此步驟中，請輸入您的 AD 網域名稱，然後按一下 [Add Directory] \(新增目錄\)**** 按鈕。

   ![添加目錄](./media/workday-inbound-tutorial/pa_install_screen_4.png "新增目錄")
  
1. 現在系統會提示您輸入認證，才能連線至 AD 網域。 在相同畫面中，您可以使用 [選取網域控制站優先權]**** 來指定代理程式應用來傳送佈建要求的網域控制站。

   ![網域認證](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. 設定網域之後，安裝程式會顯示已設定的網域清單。 在此畫面上，您可以重複步驟 #5 和 #6 來新增更多網域，或是按一下 [下一步]**** 來繼續進行代理程式註冊。

   ![已配置的域](./media/workday-inbound-tutorial/pa_install_screen_6.png "已配置的域")

   > [!NOTE]
   > 如果您有多個 AD 網域 (例如 a.contoso.com、emea.contoso.com)，則請將每個網域個別新增至清單。
   > 只新增父系網域 (例如 contoso.com) 是不夠的。 您必須向代理程式註冊每個子網域。
   
1. 檢閱設定詳細資料，並按一下 [確認]**** 以註冊代理程式。
  
   ![確認螢幕](./media/workday-inbound-tutorial/pa_install_screen_7.png "確認螢幕")
   
1. 設定精靈會顯示代理程式註冊的進度。
  
   ![代理程式註冊](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理程式註冊")
   
1. 一旦代理程式註冊成功之後，您可以按一下 [結束]**** 來結束精靈。
  
   ![退出螢幕](./media/workday-inbound-tutorial/pa_install_screen_9.png "退出螢幕")
   
1. 開啟 [服務] 嵌入式管理單元，並尋找名稱為「Microsoft Azure AD Connect 佈建代理程式」的服務，以確認此代理程式的安裝及確定它是否正在執行
  
   ![服務](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>第 3 部分：在預配應用中，配置與成功因數和活動目錄的連接
在此步驟中，我們在 Azure 門戶中建立與成功因數和活動目錄的連接。 

1. 在 Azure 門戶中，返回第[1 部分](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)中創建的"成功因素"到活動目錄使用者預配應用

1. 完成 [系統管理員認證]**** 區段，如下所示：

   * **管理員使用者名**– 輸入成功因素 API 使用者帳戶的使用者名，並附上公司 ID。 它有格式：**\@使用者名公司ID**

   * **管理員密碼 |** 輸入成功因素 API 使用者帳戶的密碼。 

   * **租戶 URL |** 輸入成功因素 OData API 服務終結點的名稱。 僅輸入沒有 HTTP 或 HTTPs 的伺服器主機名稱。 此值應如下所示 **：<api 伺服器名稱>.成功因素.com**。

   * **Active Directory 樹系** – 向代理程式註冊的 Active Directory 網域「名稱」。 請使用下拉式清單來選取用於佈建的目標網域。 此值通常是如下的字串：*contoso.com*

   * **Active Directory 容器 -** 輸入容器 DN，其中是代理程式預設應建立使用者帳戶的位置。
        示例 *：OU=使用者，DC=contoso，DC_com*
        > [!NOTE]
        > 如果並未在屬性對應中設定 *parentDistinguishedName* 屬性，此設定僅適用於使用者帳戶建立。 此設定不適用於使用者搜尋或更新作業。 整個網域的子樹狀會落在搜尋作業的範圍中。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線]**** 按鈕。 如果連線測試成功，請按一下頂端的 [儲存]**** 按鈕。 如果失敗，請仔細檢查成功因素憑據和代理設置上配置的 AD 憑據是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 成功保存憑據後，"**映射"** 部分將顯示預設映射**將成功因數使用者同步到內部活動目錄**

### <a name="part-4-configure-attribute-mappings"></a>第 4 部分：配置屬性對應

在本節中，您將配置使用者資料如何從成功因數流向活動目錄。

1. 在 **"映射**"下的預配選項卡上，按一下 **"將成功因數使用者同步到本地活動目錄**"。

1. 在 **"源物件範圍"** 欄位中，可以通過定義一組基於屬性的篩選器，選擇 SuccessFactors 中哪些使用者組應位於預配 AD 的範圍內。 預設範圍為"成功因數中的所有使用者"。 範例篩選：

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
   > 預配引擎的預設行為是禁用/刪除超出範圍的使用者。 這在您的成功因素到 AD 集成中可能並不可取。 要重寫此預設行為，請參閱跳過[刪除超出範圍的使用者帳戶](../app-provisioning/skip-out-of-scope-deletions.md)的文章
  
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
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果映射或成功因素資料問題有錯誤，則預配作業可能會失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍]**** 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建]**** 索引標籤中，將 [佈建狀態]**** 設定為 [開啟]****。

2. 按一下 [儲存]****。

3. 此操作將啟動初始同步，這可能需要可變小時數，具體取決於成功因數租戶中的使用者數。 您可以檢查進度條以跟蹤同步週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄]**** 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄列出了預配服務執行的所有單個同步事件，例如哪些使用者從成功因數中讀取，然後隨後添加或更新到活動目錄。 

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
