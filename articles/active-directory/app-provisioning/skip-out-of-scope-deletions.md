---
title: 略過刪除超出範圍的使用者
description: 瞭解如何覆寫超出範圍使用者的解除布建的預設行為。
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
ms.openlocfilehash: f459a804b4c375eea17cbc22ded2f41f808c1b82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041173"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>略過刪除超出範圍的使用者帳戶

Azure AD 布建引擎預設會刪除或停用超出範圍的使用者。 不過，在某些情況下（例如 Workday 到 AD 使用者輸入布建），這種行為可能不是預期的行為，您可能會想要覆寫此預設行為。  

本文說明如何使用 Microsoft Graph API 和 Microsoft Graph API explorer 來設定旗標 * **SkipOutOfScopeDeletions** _，以控制移出範圍的帳戶處理。 _ 如果 * **SkipOutOfScopeDeletions** _ 設定為 0 (false) ，則會在目標中停用超出範圍的帳戶。
_ 如果 * **SkipOutOfScopeDeletions** _ 設定為 1 (true) ，則不會在目標中停用超出範圍的帳戶。 此旗標是設定在 _Provisioning App * 層級，而且可以使用圖形 API 來設定。 

因為此設定廣泛搭配 Workday 使用， *以 Active Directory 使用者* 布建應用程式，所以下列步驟包含 workday 應用程式的螢幕擷取畫面。 不過，此設定也可以搭配 *所有其他應用程式* 使用，例如 ServiceNow、Salesforce 和 Dropbox。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步驟1： (物件識別碼取得布建 App Service 主體識別碼) 

1. 啟動 [Azure 入口網站](https://portal.azure.com)，然後流覽至布建應用程式的 [屬性] 區段。 例如，如果您想要將 Workday 匯出 *至 AD 使用者布建應用程式* 對應，請流覽至該應用程式的屬性區段。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼]  欄位相關的 GUID 值。 此值也稱為您應用程式的 **ServicePrincipalId** ，並且會在「Graph 總管」作業中用到。

   ![Workday 應用程式服務主體識別碼](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步驟2：登入 Microsoft Graph Explorer

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Graph 登入](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>步驟3：取得現有的應用程式認證和連線詳細資料

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從 [步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 擷取的 **ServicePrincipalId** 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![取得作業查詢](./media/skip-out-of-scope-deletions/skip-03.png)

將回應複製到文字檔中。 它看起來類似如下所示的 JSON 文字，其中的值會以黃色醒目提示您部署的特定值。 將以綠色反白顯示的行新增至結尾，並更新以藍色醒目提示的 Workday 連接密碼。 

   ![取得作業回應](./media/skip-out-of-scope-deletions/skip-04.png)

以下是要新增至對應的 JSON 區塊。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>步驟4：使用 SkipOutOfScopeDeletions 旗標更新秘密端點

在 Graph Explorer 中，執行下列命令以使用 * *_SkipOutOfScopeDeletions_* _ 旗標更新秘密端點。 

在下列 URL 中，將 [servicePrincipalId] 取代為從 [步驟 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)解壓縮的 _ *servicePrincipalId* *。 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
將步驟3中的更新文字複製到「要求本文」，並在「要求標頭」中將標頭 "Content-type" 設定為 "application/json"。 

   ![PUT 要求](./media/skip-out-of-scope-deletions/skip-05.png)

按一下 [執行查詢]。 

您應該會取得輸出為「成功–狀態碼204」。 

   ![PUT 回應](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>步驟5：確認未停用超出範圍的使用者

您可以藉由更新範圍規則以略過特定使用者，來測試此旗標會產生預期的行為。 在下列範例中，我們會藉由加入新的範圍規則，將識別碼為21173的員工排除在範圍)  (： 

   ![顯示 [新增範圍篩選器] 區段的螢幕擷取畫面，其中已醒目提示 [範例使用者]。](./media/skip-out-of-scope-deletions/skip-07.png)

在下一個布建週期中，Azure AD 布建服務會識別出使用者21173已超出範圍，如果已啟用 SkipOutOfScopeDeletions 屬性，則該使用者的同步處理規則會顯示如下所示的訊息： 

   ![範圍範例](./media/skip-out-of-scope-deletions/skip-08.png)


