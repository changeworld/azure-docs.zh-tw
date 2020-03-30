---
title: 隔離的應用程式預配狀態 |微軟文檔
description: 為自動使用者預配配置應用程式後，請瞭解隔離預配狀態的含義以及如何清除它。
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154622"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔離狀態中的應用程式預配

Azure AD 預配服務監視配置的運行狀況，並將不正常的應用置於"隔離"狀態。 如果針對目標系統所做的大多數或所有調用始終由於錯誤（例如不正確管理員憑據）而失敗，則預配作業將標記為隔離區。

在隔離期間，增量迴圈的頻率逐漸降低為每天一次。 修復所有錯誤並啟動下一個同步週期後，將從隔離區中刪除預配作業。 如果預配作業在隔離區中停留超過四周，則預配作業將禁用（停止運行）。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何知道我的申請是否處於隔離狀態？

有三種方法可以檢查應用程式是否處於隔離狀態：
  
- 在 Azure 門戶中，導航到**Azure 活動目錄** > **企業** > &lt;*應用程式名稱*&gt; > **預配**，然後滾動到底部的進度列。  

  ![顯示隔離狀態的預配狀態列](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 門戶中，導航到**Azure 活動目錄** > **稽核記錄**>"**活動：隔離**"和"檢查隔離歷史記錄"上的篩選器。 雖然上述進度列中的視圖顯示預配當前是否處於隔離狀態，但稽核記錄允許您查看應用程式的隔離歷史記錄。 

- 使用 Microsoft 圖形請求[獲取同步作業](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http)以程式設計方式獲取預配作業的狀態：

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 請檢查您的電子郵件。 將應用程式置於隔離區時，將發送一次性通知電子郵件。 如果隔離原因發生更改，將發送更新的電子郵件，顯示隔離的新原因。 如果您沒有看到電子郵件：

  - 請確保在為應用程式的預配配置中指定了有效的**通知電子郵件**。
  - 確保通知電子郵件收件匣中沒有垃圾郵件篩選。
  - 請確保您沒有取消訂閱電子郵件。

## <a name="why-is-my-application-in-quarantine"></a>為什麼我的申請是在隔離中？

Microsoft Graph 請求獲取預配作業的狀態，顯示隔離的以下原因：

- `EncounteredQuarantineException`表示提供了無效憑據。 預配服務無法在源系統和目標系統之間建立連接。

- `EncounteredEscrowProportionThreshold`表示預配超過託管閾值。 當超過 60% 的預配事件失敗時，將發生此情況。

- `QuarantineOnDemand`表示我們檢測到您的應用程式出現問題，並手動將其設置為隔離。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何將申請從隔離區獲取？

首先，解決導致應用程式被置於隔離區的問題。

- 檢查應用程式的預配設置，以確保您[輸入了有效的管理員憑據](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必須能夠與目標應用程式建立信任。 確保您已輸入有效憑據，並且您的帳戶具有必要的許可權。

- 查看[預配日誌](../reports-monitoring/concept-provisioning-logs.md)以進一步調查導致隔離的錯誤並解決錯誤。 通過訪問"**活動"** 部分中的**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽）** 來訪問 Azure 門戶中的預配日誌。

解決問題後，重新開機預配作業。 對應用程式預配設置的某些更改（如屬性對應或範圍篩選器）將自動重新開機您的預配。 應用程式**預配**頁上的進度列指示上次開始預配時。 如果需要手動重新開機預配作業，請使用以下方法之一：  

- 使用 Azure 門戶重新開機預配作業。 在 **"設置"** 下的應用程式**預配**頁上，選擇 **"清除狀態並重新啟動同步**"並將**預配狀態**設置為 **"打開**"。 此操作將完全重新開機預配服務，這可能需要一些時間。 完整的初始週期將再次運行，這將清除埃斯庫，從隔離中刪除應用程式，並清除任何浮水印。

- 使用 Microsoft 圖形[重新開機預配作業](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 您將完全控制重新開機的內容。 您可以選擇清除代管（重新開機累積到隔離狀態的代管計數器）、清除隔離（從隔離區中刪除應用程式）或清除浮水印。 使用下列要求：
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
