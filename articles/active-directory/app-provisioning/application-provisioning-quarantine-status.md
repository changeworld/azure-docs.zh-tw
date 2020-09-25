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
ms.openlocfilehash: 6a716aef65cc81c5558a214c1ee5f93180810977
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266678"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔離狀態中的應用程式布建

Azure AD 布建服務會監視設定的健康情況，並將狀況不良的應用程式置於「隔離」狀態。 如果對目標系統進行的大部分或所有呼叫一直都因為錯誤而失敗，例如不正確系統管理員認證，則布建作業會標示為隔離中。

在隔離期間，增量迴圈的頻率會逐漸縮減為每天一次。 在所有錯誤都已修正，而且下一個同步處理週期開始時，會從隔離中移除布建作業。 如果布建作業保持隔離超過四周，則會停用布建作業 (停止執行) 。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何? 知道我的應用程式是否在隔離中？

有三種方式可以檢查應用程式是否在隔離中：
  
- 在 Azure 入口網站中，流覽至**Azure Active Directory**的  >  **企業應用**  >  &lt; *程式應用程式名稱*布建 &gt;  >  **Provisioning** ，並查看隔離訊息的進度列。   

  ![顯示隔離狀態的布建狀態列](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 入口網站中，流覽至**Azure Active Directory**  >  **Audit Logs** > 篩選**活動：隔離**並檢查隔離歷程記錄。 如上面所述的進度列中的視圖顯示布建目前是否為隔離，則 audit 記錄檔可讓您查看應用程式的隔離歷程記錄。 

- 使用 Microsoft Graph 要求 [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta) ，以程式設計方式取得布建作業的狀態：

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- 請檢查您的電子郵件。 當應用程式放入隔離時，系統會傳送一封單次通知電子郵件。 如果隔離原因變更，則會傳送更新的電子郵件，以顯示隔離的新原因。 如果您沒有看到電子郵件：

  - 請確定您已在應用程式的布建設定中指定有效的 **通知電子郵件** 。
  - 請確定通知電子郵件收件匣沒有垃圾郵件篩選。
  - 請確定您尚未取消電子郵件的訂閱。
  - 檢查電子郵件 azure-noreply@microsoft.com

## <a name="why-is-my-application-in-quarantine"></a>為什麼我的應用程式是隔離的？

|說明|建議的動作|
|---|---|
|**SCIM 合規性問題：** 傳回 HTTP/404 找不到回應，而非預期的 HTTP/200 確定回應。 在此情況下，Azure AD 布建服務已向目標應用程式提出要求，並收到未預期的回應。|檢查 [系統管理員認證] 區段，查看應用程式是否需要指定租使用者 URL，並確定 URL 是否正確。 如果您沒有看到問題，請洽詢應用程式開發人員，以確保其服務符合 SCIM 規範。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**不正確認證：** 當您嘗試授權存取目標應用程式時，我們收到來自目標應用程式的回應，指出提供的認證無效。|請流覽至布建設定 UI 的 [管理員認證] 區段，然後使用有效的認證再次授權存取權。 如果應用程式在資源庫中，請參閱應用程式設定教學課程，以瞭解所需的任何其他步驟。|
|**重複的角色：** 從 Salesforce 和 Zendesk 等某些應用程式匯入的角色必須是唯一的。 |流覽至 Azure 入口網站中的應用程式 [資訊清單](../develop/reference-app-manifest.md) ，並移除重複的角色。|

 取得布建作業狀態的 Microsoft Graph 要求會顯示下列隔離的原因：

- `EncounteredQuarantineException` 指出提供的認證無效。 布建服務無法建立來源系統與目標系統之間的連接。

- `EncounteredEscrowProportionThreshold` 指出布建超過保管閾值。 當超過60% 的布建事件失敗時，就會發生這種情況。

- `QuarantineOnDemand` 表示我們偵測到您的應用程式發生問題，並已手動將其設定為隔離。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何? 讓我的應用程式不隔離？

首先，請解決導致應用程式放在隔離中的問題。

- 請檢查應用程式的布建設定，以確定您輸入的是有效的系統 [管理員認證](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必須能夠與目標應用程式建立信任關係。 請確定您已輸入有效的認證，而且您的帳戶具有必要的許可權。

- 檢查布建 [記錄](../reports-monitoring/concept-provisioning-logs.md) 檔，以進一步調查造成隔離的錯誤，並解決錯誤。 前往 [活動] 區段中的**Azure Active Directory** &gt; **企業應用程式**布建 &gt; **記錄 (預覽) ** **Activity** ，以存取 Azure 入口網站中的布建記錄。

解決問題之後，請重新開機布建作業。 應用程式布建設定的某些變更，例如屬性對應或範圍篩選器，將會自動為您重新開機布建。 應用程式 [布建 **] 頁面上** 的進度列會指出布建上次啟動的時間。 如果您需要手動重新開機布建作業，請使用下列其中一種方法：  

- 使用 Azure 入口網站重新開機布建作業。 在應用程式布 **建頁面的** [ **設定**] 底下，選取 [ **清除狀態] 並重新啟動同步** 處理，並將布建 **狀態** 設為 **開啟**。 此動作會完全重新開機布建服務，這可能需要一些時間。 完整的初始週期將會再次執行，這會清除 escrows、將應用程式從隔離中移除，並清除任何浮水印。

- 使用 Microsoft Graph [重新開機](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)布建作業。 您將可完全掌控重新開機的內容。 您可以選擇清除 escrows (，以重新開機) 的隔離狀態、明確隔離 (以移除隔離的應用程式) 或清除浮水印。 使用下列要求：
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

以應用程式識別碼的值取代 "{id}"，並以 [同步處理工作的識別碼](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta#list-synchronization-jobs-in-the-context-of-the-service-principal)取代 "{jobId}"。
