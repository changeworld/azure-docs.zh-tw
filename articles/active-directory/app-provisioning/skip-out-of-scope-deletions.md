---
title: 跳過刪除範圍外使用者 |微軟文檔
description: 瞭解如何覆蓋在作用域外使用者取消預配的預設行為。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522444"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>跳過刪除超出範圍的使用者帳戶

預設情況下，Azure AD 預配引擎虛刪除或禁用超出範圍的使用者。 但是，對於某些方案（如工作日到 AD 使用者入站預配）此行為可能不是預期的，您可能需要重寫此預設行為。  

本指南介紹如何使用 Microsoft 圖形 API 和 Microsoft 圖形 API 資源管理器設置標誌***SkipOutScopeDeletions，*** 用於控制超出範圍的帳戶的處理。 
* 如果***SkipOutScope 刪除***設置為 0（false），則超出範圍的帳戶將在目標中禁用
* 如果***SkipOutScope 刪除***設置為 1（true），則超出範圍的帳戶將不會在目標中禁用此標誌設置在*預配應用*級別，並且可以使用圖形 API 進行配置。 

由於此配置與*工作日到活動目錄使用者預配*應用廣泛應用，以下步驟包括工作日應用程式的螢幕截圖。 但是，這也可以用於**所有其他應用**，如服務現在、銷售隊伍、Dropbox 等）。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>第 1 步：檢索預配應用服務主體 ID（物件識別碼）

1. 啟動[Azure 門戶](https://portal.azure.com)，然後導航到預配應用程式的"屬性"部分。 例如，如果要將*工作日匯出到 AD 使用者預配應用程式映射*，請導航到該應用的"屬性"部分。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼]** 欄位相關的 GUID 值。 此值也稱為您應用程式的 **ServicePrincipalId**，並且會在「Graph 總管」作業中用到。

   ![Workday 應用程式服務主體識別碼](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>第 2 步：登錄微軟圖形資源管理器

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Graph 登入](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>第 3 步：獲取現有應用憑據和連接詳細資訊

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從[步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![獲取作業查詢](./media/skip-out-of-scope-deletions/skip-03.png)

將回應複製到文字檔中。 它將類似于下面的 JSON 文本，以特定于部署的黃色突出顯示值。 將以綠色突出顯示的行添加到末尾，並更新以藍色突出顯示的工作日連接密碼。 

   ![獲取工作回應](./media/skip-out-of-scope-deletions/skip-04.png)

下面是要添加到映射的 JSON 塊。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>第 4 步：使用"跳過退出刪除"標誌更新機密終結點

在圖形資源管理器中，運行下面的命令，使用***SkipOutScope 刪除***標誌更新機密終結點。 

在下面的 URL 中，將 [服務主體Id] 替換為從步驟[1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)中提取**的服務主體Id。** 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
將步驟 3 中更新的文本複製到"請求正文"中，並將標題"內容類型"設置為"請求標題"中的"應用程式/json"。 

   ![PUT 要求](./media/skip-out-of-scope-deletions/skip-05.png)

按一下"執行查詢"。 

您應該將輸出作為"成功 + 狀態碼 204"。 

   ![PUT 回應](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>第 5 步：驗證範圍外使用者未被禁用

您可以通過更新範圍規則來跳過特定使用者來測試此標誌導致預期行為。 在下面的示例中，我們添加了新的範圍規則，從而排除了 ID 21173（在作用域中較早）的員工： 

   ![範圍界定示例](./media/skip-out-of-scope-deletions/skip-07.png)

在下一個預配週期中，Azure AD 預配服務將標識使用者 21173 已退出範圍，如果啟用了 SkipOutScope 刪除屬性，則該使用者的同步規則將顯示如下所示的消息： 

   ![範圍界定示例](./media/skip-out-of-scope-deletions/skip-08.png)


