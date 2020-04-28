---
title: 匯出您的布建設定，並復原至已知的良好狀態以進行嚴重損壞修復。 |Microsoft Docs
description: 瞭解如何匯出您的布建設定，並復原至已知的良好狀態以進行嚴重損壞修復。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051302"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>匯出您的布建設定並復原至已知的良好狀態

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>從 Azure 入口網站匯出和匯入您的布建設定

### <a name="how-can-i-export-my-provisioning-configuration"></a>如何匯出布建設定？
若要匯出您的設定：
1. 在 [Azure 入口網站](https://portal.azure.com/)的左方瀏覽窗格中，選取 [Azure Active Directory]****。
2. 在 [ **Azure Active Directory** ] 窗格中，選取 [**企業應用程式**]，然後選擇您的應用程式。
3. 在左側流覽窗格中 **，選取 [** 布建]。 在 [布建設定] 頁面上，依序按一下 [**屬性**對應] 和 [顯示] [ **advanced options**]，最後再**檢查您的架構**。 這會帶您前往 [架構編輯器]。 
5. 在頁面頂端的命令列中，按一下 [下載] 以下載您的架構。

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>嚴重損壞修復-復原至已知的良好狀態
匯出和儲存您的設定可讓您復原到先前版本的設定。 我們建議您匯出布建設定並加以儲存，以供稍後在變更屬性對應或範圍篩選器時使用。 您只需要開啟您在上述步驟中下載的 JSON 檔案、複製 JSON 檔案的完整內容、在架構編輯器中取代 JSON 承載的完整內容，然後儲存。 如果有作用中的布建週期，它將會完成，而下一個週期會使用更新的架構。 下一個週期也會是初始迴圈，它會根據新的設定重新評估每個使用者和群組。 回復到先前的設定時，請考慮下列事項：
* 系統會再次評估使用者，以判斷它們是否應在範圍內。 如果範圍篩選器已變更，則使用者不在範圍內，將會停用它們。 雖然在大部分情況下，這是想要的行為，但有時您可能會想要避免這種情況，並且可以使用[跳過範圍的刪除](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions)功能。 
* 變更您的布建設定會重新開機服務並觸發[初始迴圈](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)。


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>使用 Microsoft Graph API 匯出和匯入您的布建設定
您可以使用 Microsoft Graph API 和 Microsoft Graph Explorer，將您的使用者布建屬性對應和架構匯出至 JSON 檔案，並將其匯入回 Azure AD。 您也可以使用此處所提供的步驟來建立布建設定的備份。 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步驟1：取出您的布建 App Service 主體識別碼（物件識別碼）

1. 啟動[Azure 入口網站](https://portal.azure.com)，然後流覽至布建應用程式的 [屬性] 區段。 例如，如果您想要將 Workday 匯出*至 AD 使用者布建應用程式*對應，請流覽至該應用程式的 [屬性] 區段。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼]** 欄位相關的 GUID 值。 這個值也稱為應用程式的**ServicePrincipalId** ，它將用於 Microsoft Graph Explorer 作業中。

   ![Workday 應用程式服務主體識別碼](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>步驟2：登入 Microsoft Graph Explorer

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Microsoft Graph 登入](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>步驟3：取出布建應用程式的布建作業識別碼

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從[步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

您會得到如下所示的回應。 請複製回應中的「id 屬性」。 此值是 **ProvisioningJobId**，且將會用來擷取基礎結構描述中繼資料。

   [![布建作業識別碼](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>步驟4：下載布建架構

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

從回應中複製 JSON 物件，然後將它儲存成檔案以建立結構描述備份。

### <a name="step-5-import-the-provisioning-schema"></a>步驟5：匯入布建架構

> [!CAUTION]
> 請只有在您需要為無法使用 Azure 入口網站來變更的設定修改結構描述，或您需要從含有效且可運作之結構描述的先前備份檔案還原設定時，才執行此步驟。

在「Microsoft Graph 總管」中，設定下列 PUT 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在 [要求本文] 索引標籤中，複製 JSON 結構描述檔案的內容。

   [![要求本文](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

在 [要求標頭] 索引標籤中，新增值為 “application/json” 的 Content-Type 標頭屬性

   [![要求標頭](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

按一下 [執行查詢] 按鈕以匯入新的結構描述。
