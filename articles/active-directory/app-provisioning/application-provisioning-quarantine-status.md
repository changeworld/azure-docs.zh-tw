---
title: 隔離的應用程式布建狀態 |Microsoft Docs
description: 當您設定應用程式來自動布建使用者時，請瞭解隔離的布建狀態是什麼，以及如何將它清除。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: aa0df4e18e89ecf366da0ddf79e5241bca99bcc1
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915261"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔離狀態中的應用程式布建

Azure AD 布建服務會監視您的設定健全狀況。 它也會將狀況不良的應用程式放在「隔離」狀態。 如果對目標系統進行的大部分或所有呼叫都持續失敗，則布建作業會標示為隔離中。 失敗的範例是因為系統管理員認證無效而收到的錯誤。

在隔離中：
- 遞增週期的頻率會逐漸縮減為每天一次。
- 在所有錯誤都已修正，而且下一個同步處理週期開始時，會從隔離中移除布建作業。 
- 如果布建作業保持隔離超過四周，則會停用布建作業 (停止執行) 。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何? 知道我的應用程式是否在隔離中？

有三種方式可以檢查應用程式是否在隔離中：
  
- 在 Azure 入口網站中，流覽至 **Azure Active Directory** 的  >  **企業應用**  >  &lt; *程式應用程式名稱* 布建 &gt;  >   ，並查看隔離訊息的進度列。   

  ![顯示隔離狀態的布建狀態列](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 入口網站中，流覽至 **Azure Active Directory**  >  **Audit Logs** > 篩選 **活動：隔離** 並檢查隔離歷程記錄。 如上所述，進度列中的視圖會顯示布建目前是否為隔離。 Audit 記錄檔會顯示應用程式的隔離歷程記錄。 

- 使用 Microsoft Graph 要求 [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) ，以程式設計方式取得布建作業的狀態：

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- 請檢查您的電子郵件。 當應用程式放入隔離時，系統會傳送一封單次通知電子郵件。 如果隔離原因變更，則會傳送更新的電子郵件，以顯示隔離的新原因。 如果您沒有看到電子郵件：

  - 請確定您已在應用程式的布建設定中指定有效的 **通知電子郵件** 。
  - 請確定通知電子郵件收件匣沒有垃圾郵件篩選。
  - 請確定您尚未取消電子郵件的訂閱。
  - 檢查電子郵件 `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>為什麼我的應用程式是隔離的？

|描述|建議的動作|
|---|---|
|**SCIM 合規性問題：** 傳回 HTTP/404 找不到回應，而非預期的 HTTP/200 確定回應。 在此情況下，Azure AD 布建服務已向目標應用程式提出要求，並收到未預期的回應。|檢查 [系統管理員認證] 區段。 查看應用程式是否需要指定租使用者 URL，以及 URL 是否正確。 如果您沒有看到問題，請洽詢應用程式開發人員，以確保其服務符合 SCIM 規範。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**不正確認證：** 當您嘗試授權存取目標應用程式時，我們收到來自目標應用程式的回應，指出提供的認證無效。|流覽至布建設定 UI 的 [管理員認證] 區段，然後使用有效的認證再次授權存取權。 如果應用程式在資源庫中，請參閱應用程式設定教學課程，以取得不再需要的步驟。|
|**重複的角色：** 從 Salesforce 和 Zendesk 等某些應用程式匯入的角色必須是唯一的。 |流覽至 Azure 入口網站中的應用程式 [資訊清單](../develop/reference-app-manifest.md) ，並移除重複的角色。|

 取得布建作業狀態的 Microsoft Graph 要求會顯示下列隔離的原因：
- `EncounteredQuarantineException` 指出提供的認證無效。 布建服務無法建立來源系統與目標系統之間的連接。
- `EncounteredEscrowProportionThreshold` 指出布建超過保管閾值。 當超過40% 的布建事件失敗時，就會發生這種情況。 如需詳細資訊，請參閱以下的保管閾值詳細資料。
- `QuarantineOnDemand` 表示我們偵測到您的應用程式發生問題，並已手動將其設定為隔離。

**保管閾值**

如果符合比例的配置閾值，布建作業將會進入隔離。 此邏輯可能會變更，但其運作方式大致如下所述： 

無論系統管理員認證或 SCIM 合規性等問題的失敗計數，作業都可以進入隔離狀態。 不過，一般而言，5000失敗是開始評估是否因為失敗太多而隔離的最小值。 例如，具有4000失敗的作業不會進入隔離。 但是，具有5000失敗的作業將會觸發評估。 評估會使用下列準則：  
- 如果超過40% 的布建事件失敗，或超過40000個失敗，布建作業將會進入隔離。 參考失敗不會被視為40% 閾值或40000閾值的一部分。 例如，無法更新管理員或群組成員是參考失敗。
- 45000使用者未成功布建的工作會導致隔離，因為它超過40000閾值。
- 30000使用者布建失敗的工作和5000成功會導致隔離，因為它超過40% 閾值和5000的最小值。
- 具有20000失敗和 100000 success 的作業不會進入隔離，因為它 doe 不超過40% 失敗閾值或40000失敗上限。  
- 有60000失敗的絕對臨界值，可同時說明參考和非參考失敗。 例如，40000使用者無法布建，且 21000 manager 更新失敗。 總計為61000個失敗，超過60000個限制。


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何? 讓我的應用程式不隔離？

首先，請解決導致應用程式放在隔離中的問題。

- 請檢查應用程式的布建設定，以確定您輸入的是有效的系統 [管理員認證](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必須與目標應用程式建立信任關係。 請確定您已輸入有效的認證，而且您的帳戶具有必要的許可權。

- 檢查布建 [記錄](../reports-monitoring/concept-provisioning-logs.md) 檔，以進一步調查造成隔離的錯誤，並解決錯誤。 移至 &gt; [活動] 區段中的 Azure Active Directory **Enterprise Apps** 布建 &gt; **記錄 (preview)** 。 

解決問題之後，請重新開機布建作業。 應用程式布建設定的某些變更，例如屬性對應或範圍篩選器，將會自動為您重新開機布建。 應用程式 [布建 **] 頁面上** 的進度列會指出布建上次啟動的時間。 如果您需要手動重新開機布建作業，請使用下列其中一種方法：  

- 使用 Azure 入口網站重新開機布建作業。 在應用程式布 **建頁面的** [ **設定**] 底下，選取 [ **清除狀態] 並重新啟動同步** 處理，並將布建 **狀態** 設為 **開啟**。 此動作會完全重新開機布建服務，這可能需要一些時間。 完整的初始週期將會再次執行，這會清除 escrows、將應用程式從隔離中移除，並清除任何浮水印。

- 使用 Microsoft Graph [重新開機](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)布建作業。 您將可完全掌控重新開機的內容。 您可以選擇清除 escrows (，以重新開機) 的隔離狀態、明確隔離 (以移除隔離的應用程式) 或清除浮水印。 使用下列要求：
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

以應用程式識別碼的值取代 "{ID}"，並以 [同步處理工作的識別碼](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal)取代 "{jobId}"。
