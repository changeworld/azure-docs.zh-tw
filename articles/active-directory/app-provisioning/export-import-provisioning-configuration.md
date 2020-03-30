---
title: 匯出預配配置並回滾到已知良好的災害復原狀態。微軟文檔
description: 瞭解如何匯出預配配置並回滾到已知良好的災害復原狀態。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051302"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>匯出預配配置並回滾到已知良好狀態

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>從 Azure 門戶匯出和導入預配配置

### <a name="how-can-i-export-my-provisioning-configuration"></a>如何匯出預配配置？
要匯出配置：
1. 在 [Azure 入口網站](https://portal.azure.com/)的左方瀏覽窗格中，選取 [Azure Active Directory]****。
2. 在**Azure 活動目錄**窗格中，選擇**企業應用程式**並選擇應用程式。
3. 在左側功能窗格中，選擇**預配**。 在預配配置頁中，按一下**屬性對應**，然後**顯示高級選項**，最後**查看架構**。 這將帶您到架構編輯器。 
5. 按一下頁面頂部的命令列中的下載以下載架構。

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>災害復原 - 回滾到已知良好的狀態
匯出和保存配置允許您回滾到配置的早期版本。 我們建議您匯出預配配置並將其保存，以便以後對屬性對應或範圍篩選器進行更改。 您只需打開在上述步驟中下載的 JSON 檔，複製 JSON 檔的全部內容，在架構編輯器中替換 JSON 有效負載的全部內容，然後保存。 如果存在活動預配週期，它將完成，下一個週期將使用更新的架構。 下一個週期也將是初始週期，根據新配置重新評估每個使用者和組。 回滾到以前的配置時，請考慮以下事項：
* 將再次評估使用者，以確定使用者是否應在作用域中。 如果範圍篩選器已更改，則使用者不再處於作用域內，則使用者將被禁用。 雖然在大多數情況下，這是所需的行為，但有時您可能希望防止這種情況，並且可以使用[跳出範圍刪除](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions)功能。 
* 更改預配配置將重新開機服務並觸發[初始週期](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)。


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>使用 Microsoft 圖形 API 匯出和導入預配配置
您可以使用 Microsoft 圖形 API 和 Microsoft 圖形資源管理器將使用者預配屬性對應和架構匯出到 JSON 檔並將其導入 Azure AD。 您還可以使用此處捕獲的步驟創建預配配置的備份。 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>第 1 步：檢索預配應用服務主體 ID（物件識別碼）

1. 啟動[Azure 門戶](https://portal.azure.com)，然後導航到預配應用程式的"屬性"部分。 例如，如果要將*工作日匯出到 AD 使用者預配應用程式映射*，請導航到該應用的"屬性"部分。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼]** 欄位相關的 GUID 值。 此值也稱為應用的服務**主體Id，** 它將在 Microsoft 圖形資源管理器操作中使用。

   ![Workday 應用程式服務主體識別碼](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>第 2 步：登錄微軟圖形資源管理器

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![微軟圖形登錄](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>第 3 步：檢索預配應用的預配作業 ID

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從[步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

您會得到如下所示的回應。 請複製回應中的「id 屬性」。 此值是 **ProvisioningJobId**，且將會用來擷取基礎結構描述中繼資料。

   [![預配作業 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>第 4 步：下載預配架構

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

從回應中複製 JSON 物件，然後將它儲存成檔案以建立結構描述備份。

### <a name="step-5-import-the-provisioning-schema"></a>第 5 步：導入預配架構

> [!CAUTION]
> 請只有在您需要為無法使用 Azure 入口網站來變更的設定修改結構描述，或您需要從含有效且可運作之結構描述的先前備份檔案還原設定時，才執行此步驟。

在「Microsoft Graph 總管」中，設定下列 PUT 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在 [要求本文] 索引標籤中，複製 JSON 結構描述檔案的內容。

   [![請求正文](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

在 [要求標頭] 索引標籤中，新增值為 “application/json” 的 Content-Type 標頭屬性

   [![請求標頭](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

按一下 [執行查詢] 按鈕以匯入新的結構描述。
