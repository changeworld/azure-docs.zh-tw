---
title: SCIM 2.0 通訊協定合規性的已知問題-Azure AD
description: 如何解決將支援 SCIM 2.0 且不在資源庫的應用程式新增至 Azure AD 時所面臨的常見通訊協定相容性問題
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522929"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 使用者佈建服務 SCIM 2.0 通訊協定相容性的已知問題和解決方法

Azure Active Directory (Azure AD) 會利用 [System for Cross-Domain Identity Management (SCIM) 2.0 通訊協定規格](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定義的介面，自動佈建使用者和群組到 Web 服務前端的任何應用程式或系統。 

[使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式](use-scim-to-provision-users-and-groups.md)中描述 Azure AD 對 SCIM 2.0 通訊協定的支援，並列出它所實作通訊協定的特定部分，以自動將使用者和群組從 Azure AD 佈建到支援 SCIM 2.0 的應用程式。

本文描述 Azure AD 使用者佈建服務在遵循 SCIM 2.0 通訊協定方面的目前和過去問題，並說明如何解決這些問題。

> [!IMPORTANT]
> Azure AD 使用者佈建服務 SCIM 用戶端的最新更新日期為 2018 年 12 月 18 日。 此更新解決了下表中列出的已知相容性問題。 如需此更新的詳細資訊，請參閱常見問題集。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 相容性問題和狀態

| **SCIM 2.0 相容性問題** |  **已修正？** | **修正日期**  |  
|---|---|---|
| Azure AD 要求應用程式的 SCIM 端點 URL 根目錄中必須有 "/scim"  | 是  |  2018 年 12 月 18 日 | 
| 延伸模組屬性在屬性名稱前面使用點 "." 標記法，而不是冒號 ":" 標記法 |  是  | 2018 年 12 月 18 日  | 
|  多重值屬性的修補程式要求包含無效的路徑篩選語法 | 是  |  2018 年 12 月 18 日  | 
|  群組建立要求包含無效的結構描述 URI | 是  |  2018 年 12 月 18 日  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>上述服務修正會自動套用至我既存的 SCIM 應用程式嗎？

No。 由於這會構成 SCIM 應用程式的一項重大變更，並撰寫應用程式程式碼來使用舊版行為，因此這些變更不會自動套用至現有的應用程式。

在修正日期之後，這些變更會套用至 Azure 入口網站中已設定之所有[不在資源庫內的新 SCIM 應用程式](../manage-apps/configure-single-sign-on-non-gallery-applications.md)。

如需如何遷移既存的使用者佈建作業以包含最新修正的資訊，請參閱下一節。

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>我可以遷移現有的 SCIM 使用者佈建作業以包含最新服務修正嗎？

是的。 如果您已使用此應用程式執行個體進行單一登入，並需要遷移現有的佈建作業以包含最新修正，請遵循下列程序。 此程序描述如何使用 Microsoft Graph API 和 Microsoft Graph API 總管，從您現有的 SCIM 應用程式中移除舊佈建作業，並建立新的佈建作業來展示新行為。

> [!NOTE]
> 如果您的應用程式仍在開發中，且尚未針對單一登入或使用者佈建進行部署，則最簡單的解決方法是在 Azure 入口網站的 [Azure Active Directory] > [企業應用程式] 區段中刪除應用程式項目，然後直接使用 [建立應用程式] > [不在資源庫內] 選項新增應用程式項目。 這是執行下列程序的替代方案。
 
1. 登入 Azure 入口網站 https://portal.azure.com。
2. 在 Azure 入口網站的 [Azure Active Directory] > [企業應用程式] 區段中，尋找並選取您現有的 SCIM 應用程式。
3. 在您現有 SCIM 應用程式的 [屬性] 區段中，複製 [物件識別碼]。
4. 在新的網頁瀏覽器視窗中，前往 https://developer.microsoft.com/graph/graph-explorer 並以新增應用程式所在的 Azure AD 租用戶系統管理員身分登入。
5. 在 [Graph 總管] 中，執行下列命令以尋找您佈建作業的識別碼。 將 "[object-id]" 取代為您從第三個步驟複製的服務主體識別碼 (物件識別碼)。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![取得作業](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "取得作業") 


6. 在結果中，複製開頭為 "customappsso" 或 "scim" 的完整「識別碼」字串。
7. 執行下列命令以擷取屬性對應設定，以便進行備份。 使用上述相同的 [object-id]，並將 [job-id] 取代為您從上一個步驟複製的佈建作業識別碼。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![取得架構](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "取得架構") 

8. 複製上一個步驟中的 JSON 輸出，並將其儲存至文字檔。 這包含已新增至舊應用程式的任何自訂屬性對應，大約應為幾千行的 JSON。
9. 執行下列命令以刪除佈建作業：
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 執行下列命令以建立具有最新服務修正的新佈建作業。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 在上一個步驟的結果中，複製開頭為 "scim" 的完整「識別碼」字串。 (選擇性) 執行下列命令以重新套用舊的屬性對應，將 [new-job-id] 取代為您剛才複製的新作業識別碼，然後輸入步驟 #7 中的 JSON 輸出作為要求本文。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 返回第一個網頁瀏覽器視窗，然後針對您的應用程式選取 [佈建] 索引標籤。
13. 驗證您的設定，然後啟動佈建作業。 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>我可以新增具有舊使用者佈建行為且不在資源庫內的應用程式嗎？

是的。 如果您已撰寫應用程式程式碼來使用修正前的舊行為，且需要部署應用程式的新執行個體，請遵循下列程序。 此程序描述如何使用 Microsoft Graph API 和 Microsoft Graph API 總管，建立 SCIM 佈建作業來展示舊行為。
 
1. 登入 Azure 入口網站 https://portal.azure.com。
2. 在 Azure 入口網站的 [Azure Active Directory] > [企業應用程式] > [建立應用程式] 區段中，建立**不在資源庫內**的新應用程式。
3. 在新自訂應用程式的 [屬性] 區段中，複製 [物件識別碼]。
4. 在新的網頁瀏覽器視窗中，前往 https://developer.microsoft.com/graph/graph-explorer 並以新增應用程式所在的 Azure AD 租用戶系統管理員身分登入。
5. 在 [Graph 總管] 中，執行下列命令以初始化應用程式的佈建設定。
   將 "[object-id]" 取代為您從第三個步驟複製的服務主體識別碼 (物件識別碼)。

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 返回第一個網頁瀏覽器視窗，然後針對您的應用程式選取 [佈建] 索引標籤。
7. 如往常般完成使用者佈建設定。


## <a name="next-steps"></a>後續步驟
[深入了解對於 SaaS 應用程式的佈建和取消佈建](user-provisioning.md)

