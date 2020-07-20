---
title: 略過刪除超出範圍的使用者
description: 瞭解如何覆寫解除布建超出範圍使用者的預設行為。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 719258933dfadf34b8678bf03ee07ee6cc76e331
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84789900"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>略過刪除超出範圍的使用者帳戶

根據預設，Azure AD 布建引擎會虛刪除或停用超出範圍的使用者。 不過，在某些情況下（例如 Workday 到 AD 使用者輸入布建），這種行為可能不是預期的行為，您可能會想要覆寫此預設行為。  

本文說明如何使用 Microsoft Graph API 和 Microsoft Graph API explorer 來設定旗標***SkipOutOfScopeDeletions*** ，以控制超出範圍的帳戶處理。 
* 如果***SkipOutOfScopeDeletions***設定為0（false），則會在目標中停用超出範圍的帳戶。
* 如果***SkipOutOfScopeDeletions***設為1（true），則不會在目標中停用超出範圍的帳戶。 此旗標會在布建*應用*層級設定，而且可以使用圖形 API 進行設定。 

因為此設定廣泛用於*workday 以 Active Directory 使用者*布建應用程式，所以下列步驟包含 workday 應用程式的螢幕擷取畫面。 不過，此設定也可以與*所有其他應用程式*（例如 ServiceNow、Salesforce 和 Dropbox）搭配使用。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步驟1：取出您的布建 App Service 主體識別碼（物件識別碼）

1. 啟動[Azure 入口網站](https://portal.azure.com)，然後流覽至布建應用程式的 [屬性] 區段。 例如，如果您想要將 Workday 匯出*至 AD 使用者布建應用程式*對應，請流覽至該應用程式的 [屬性] 區段。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼]** 欄位相關的 GUID 值。 此值也稱為您應用程式的 **ServicePrincipalId**，並且會在「Graph 總管」作業中用到。

   ![Workday 應用程式服務主體識別碼](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步驟2：登入 Microsoft Graph Explorer

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Graph 登入](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>步驟3：取得現有的應用程式認證和連線詳細資料

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從[步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![取得作業查詢](./media/skip-out-of-scope-deletions/skip-03.png)

將回應複製到文字檔中。 它看起來會像下面所示的 JSON 文字，並以黃色醒目提示您的部署特定的值。 將以綠色反白顯示的線條新增至結尾，並更新以藍色反白顯示的 Workday 連接密碼。 

   ![取得作業回應](./media/skip-out-of-scope-deletions/skip-04.png)

以下是要新增至對應的 JSON 區塊。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>步驟4：使用 SkipOutOfScopeDeletions 旗標更新秘密端點

在 [圖形瀏覽器] 中，執行下列命令以使用***SkipOutOfScopeDeletions***旗標來更新密碼端點。 

在下列 URL 中，將 [servicePrincipalId] 取代為從[步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)解壓縮的**servicePrincipalId** 。 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
將步驟3中的更新文字複製到 [要求本文]，並在 [要求標頭] 中將標頭 "Content-type" 設定為 "application/json"。 

   ![PUT 要求](./media/skip-out-of-scope-deletions/skip-05.png)

按一下 [執行查詢]。 

您應該會得到輸出為「成功–狀態碼204」。 

   ![PUT 回應](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>步驟5：確認超出範圍的使用者不會遭到停用

您可以藉由更新範圍規則來略過特定的使用者，測試此旗標會產生預期的行為。 在下列範例中，我們會加入新的範圍規則，以排除識別碼為21173（先前在範圍中）的員工： 

   ![範圍範例](./media/skip-out-of-scope-deletions/skip-07.png)

在下一個布建週期中，Azure AD 布建服務會識別使用者21173已超出範圍，如果已啟用 SkipOutOfScopeDeletions 屬性，則該使用者的同步處理規則會顯示如下所示的訊息： 

   ![範圍範例](./media/skip-out-of-scope-deletions/skip-08.png)


