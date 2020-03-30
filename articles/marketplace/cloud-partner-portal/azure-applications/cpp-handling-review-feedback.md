---
title: 處理 Azure 應用程式審閱回饋 |Azure 應用商店
description: 說明如何使用 Azure DevOps 處理 Azure Marketplace 的 Azure 應用程式供應項目的檢閱意見反應。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285294"
---
# <a name="handling-review-feedback"></a>處理檢閱意見反應

本文說明如何存取 Microsoft Azure Marketplace 檢閱小組所使用的 Azure DevOps 環境。  如果在 **Microsoft 檢閱**步驟中，您的 Azure 應用程式供應項目中發現了嚴重問題，則可以登入此系統以檢視這些問題的的詳細資訊 (檢閱意見反應)。  修正所有問題之後，您必須重新提交供應項目，才能繼續將它發佈到 Azure Marketplace。  下圖說明此意見反應程序如何與發佈程序相關。

![發佈具有 Azure DevOps 回饋的步驟](./media/pub-flow-vsts-access.png)

一般而言，檢閱問題會以提取要求 (PR) 參考。  每個 PR 都連結到線上 [Azure DevOps](https://azure.microsoft.com/services/devops/) (先前名稱為 Visual Studio Team Services (VSTS)) 項目，其中包含問題詳細資料。  下列影像顯示檢閱 PR 參考的範例。  對於更複雜的情況，檢閱與支援小組可能也會透過電子郵件連絡您。 

![顯示檢閱意見的 [狀態] 索引標籤](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps 訪問

若要檢視檢閱意見中的 PR 項目參考，發行者首先必須被授與適當的授權。  否則，當嘗試檢視 PR 時，新的發行者會收到 `401 - Not Authorized` 回應頁面。  若要要求存取此 Azure DevOps 存放庫，請執行下列步驟：

1. 收集下列資訊：
    - 您的發行者名稱和識別碼
    - 供應項目類型 (Azure 應用程式)、提供名稱和 SKU 識別碼
    - 拉取請求連結，例如：`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`可以從通知訊息或 401 回應頁的位址檢索此 URL。
    - 您的發行組織中您要授與存取權之個人的電子郵件地址。  此清單應該包含在 Cloud Partner 入口網站上註冊為發行者時所提供的擁有者地址。
2. 建立支援事件。  在 Cloud Partner 入口網站的標題列中，選取 [說明]**** 按鈕，然後從功能表選擇 [支援]****。  您預設網頁瀏覽器應該會啟動，並瀏覽至 Microsoft 新的支援事件頁面。  (您可能必須先登入。)
3. 指定 [問題類型]**** 為 [市集上架]****，並指定 [類別]**** 為 [存取問題]****，然後選取 [開始要求]****。

    ![支援票證類別](./media/support-incident1.png)

4. 在 [步驟 2 之 1]**** 頁面中，提供您的連絡資訊並選取 [繼續]****。
5. 在 [步驟 2 之 2]**** 頁面中，指定事件標題 (例如，`Request Azure DevOps access`)，並提供您在第一步中收集的資訊 (上述)。  閱讀並接受合約，然後選取 [提交]****。

如果事件建立成功，就會顯示確認頁面。  儲存此頁面上的確認資訊供您參考。  Microsoft 支援小組應該會在幾個工作天內回覆您的存取要求。


## <a name="reviewing-the-pull-request"></a>檢閱提取要求 

您可以使用下列程序來檢閱提取要求中所述的問題。

1. 在 [發佈步驟]**** 表單的 [Microsoft 檢閱]**** 區段中，按一下 PR 連結以啟動您的瀏覽器並瀏覽至此 PR 的 [概觀 (首頁)]**** 頁面。  下圖說明 Contoso 範例應用程式供應項目的範例嚴重問題首頁。  此頁面包含有關在 Azure 應用程式中找到的檢閱問題的實用摘要資訊。  

    [![拉取請求主頁](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *按一下以展開映像。*
    
2. （可選）在視窗的右側，在 **"策略"** 部分中，按一下問題消息（在此示例中：**策略驗證失敗**），以調查問題的低級詳細資訊，包括關聯的日誌檔。  錯誤通常會顯示在記錄檔的底部。

3. 在首頁左側的功能表中，選取 [檔案]**** 以顯示包含此供應項目之技術資產的清單檔案。  Microsoft 檢閱者應該加上描述發現的重大問題的註解。  在下列範例中，已發現兩個問題。 

    [![拉取請求主頁](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *按一下以展開映像。*

4. 按一下左側樹狀目錄中的每個註解節點，以瀏覽至周圍程式碼內容中的註解。  修正小組專案中的原始程式碼，以更正註解中所述的問題。

> [!Note]
> 您無法在檢閱小組的 Azure DevOps 環境內編輯供應項目的技術資產。  對於發行者，這是包含的原始程式碼的唯讀環境。  但是，您可以留下對註解的回覆，以便 Microsoft 檢閱小組可從中獲益。

   在下列範例中，發行者已檢閱、更正並回覆第一個問題。

   ![首先修正和註解回覆](./media/first-comment-reply.png)


## <a name="next-steps"></a>後續步驟

在更正檢閱 PR 中記錄的重大問題之後，您必須[重新發行 Azure 應用程式供應項目](./cpp-publish-offer.md)。
