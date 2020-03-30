---
title: 教程：在 Azure 活動目錄中配置成功因素寫回 |微軟文檔
description: 瞭解如何從 Azure AD 將屬性寫回為成功因數
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060043"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>教程：將屬性寫回從 Azure AD 配置到 SAP 成功因數（預覽）
本教程的目標是顯示從 Azure AD 到成功因素員工中心將屬性寫入所需的步驟。 當前支援寫回的唯一屬性是電子郵件屬性。 

## <a name="overview"></a>總覽

使用[成功因數設置到本地 AD](sap-successfactors-inbound-provisioning-tutorial.md)預配應用或["成功因素"到 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)預配應用的入站預配集成後，可以選擇配置成功因數回寫應用，將電子郵件地址寫回成功因數。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此成功因素回寫使用者預配解決方案非常適合：

* 使用 Office 365 希望將 IT 管理的權威屬性（如電子郵件地址）回至成功因素的組織

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

## <a name="configuring-successfactors-writeback"></a>配置成功因素回寫

本節提供 

* [添加預配連接器應用並將連接配置為成功因素](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第 1 部分：添加預配連接器應用並將連接配置為成功因素

**要配置成功因素回寫：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]****

3. 依序選取 [企業應用程式]**** 和 [所有應用程式]****。

4. 選取 [新增應用程式]****，然後選取 [全部]**** 類別。

5. 搜索**成功因素回寫**，並從庫中添加該應用程式。

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
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 成功保存憑據後，"**映射"** 部分將顯示預設映射 **，將 Azure 活動目錄使用者同步到成功因數**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您將配置使用者資料如何從成功因數流向活動目錄。

1. 在 **"映射**"下的預配選項卡上，按一下 **"將 Azure 活動目錄使用者同步到成功因素**"。

1. 在 **"源物件範圍"** 欄位中，可以通過定義一組基於屬性的篩選器，選擇 Azure AD 中應考慮的哪些使用者集進行回寫。 預設範圍是「Azure AD 中的所有使用者」。 
   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議使用**源物件作用域**下的範圍篩選器與 Azure AD 中的幾個測試使用者一起測試映射。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 目標**物件操作**欄位僅支援**更新**操作。

1. 在 **"屬性對應"** 部分中，只能更改用於將 SuccessFactors 使用者設定檔與 Azure AD 使用者連結的匹配 ID，以及 Azure AD 中的哪個屬性用作電子郵件源。 
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >成功因素回寫僅支援電子郵件屬性。 請不要使用 **"添加新映射**"來添加新屬性。 

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

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [瞭解如何在成功因數和 Azure 活動目錄之間配置單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和導入預配配置](../app-provisioning/export-import-provisioning-configuration.md)

