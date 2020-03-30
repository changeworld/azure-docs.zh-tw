---
title: 處理商業應用商店中 Azure 應用提供的審核回饋
description: 如何使用 Microsoft 合作夥伴中心的商業應用商店門戶處理 Azure 應用商店、AppSource 或通過雲解決方案供應商 （CSP） 計畫列出或銷售的 Azure 應用產品/服務的審核回饋。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279788"
---
# <a name="handling-review-feedback"></a>處理檢閱意見反應

本文說明如何存取 Microsoft Azure Marketplace 檢閱小組所使用的 Azure DevOps 環境。 如果在 **Microsoft 檢閱**步驟中，您的 Azure 應用程式供應項目中發現了嚴重問題，則可以登入此系統以檢視這些問題的的詳細資訊 (檢閱意見反應)。 修正所有問題之後，您必須重新提交供應項目，才能繼續將它發佈到 Azure Marketplace。 下圖說明此意見反應程序如何與發佈程序相關。

![查看回饋流程](./media/review-feedback-process.png)

一般而言，檢閱問題會以提取要求 (PR) 參考。 每個 PR 都連結到線上 [Azure DevOps](https://azure.microsoft.com/services/devops/) (先前名稱為 Visual Studio Team Services (VSTS)) 項目，其中包含問題詳細資料。 下圖顯示了合作夥伴中心體驗的示例，如果在審核期間發現問題。 

![發佈狀態](./media/publishing-status.png)

包含提交詳細資訊的 PR 將在"查看認證報告"連結中提及。 對於更複雜的情況，檢閱與支援小組可能也會透過電子郵件連絡您。

## <a name="azure-devops-access"></a>Azure DevOps 訪問

有權訪問合作夥伴中心中"開發人員"角色的所有使用者都有權訪問審核回饋中引用的 PR 專案。

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>檢閱提取要求

您可以使用下列程序來檢閱提取要求中所述的問題。

1. 在"發佈步驟"表單的**Microsoft 審核**部分中，按一下 PR 連結以啟動瀏覽器並導航到此 PR 的 **"概述**（主頁）"頁面。 下圖描述了 Contoso 示例應用產品/服務的關鍵問題主頁的示例。 此頁面包含有關在 Azure 應用程式中找到的檢閱問題的實用摘要資訊。

    [![拉取請求主頁](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *按一下以展開映像。*

1. （可選）在視窗的右側，在 **"策略"** 部分中，按一下問題消息（在此示例中：**策略驗證失敗**），以調查問題的低級詳細資訊，包括關聯的日誌檔。 錯誤通常會顯示在記錄檔的底部。
1. 在首頁左側的功能表中，選取 [檔案]**** 以顯示包含此供應項目之技術資產的清單檔案。 Microsoft 檢閱者應該加上描述發現的重大問題的註解。 在下列範例中，已發現兩個問題。

    [![拉取請求主頁](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *按一下以展開映像。*

1. 按一下左側樹狀目錄中的每個註解節點，以瀏覽至周圍程式碼內容中的註解。 修正小組專案中的原始程式碼，以更正註解中所述的問題。

>[!Note]
>您無法在檢閱小組的 Azure DevOps 環境內編輯供應項目的技術資產。 對於發行者，這是包含的原始程式碼的唯讀環境。 但是，您可以留下對註解的回覆，以便 Microsoft 檢閱小組可從中獲益。

   在下列範例中，發行者已檢閱、更正並回覆第一個問題。

   ![首先修正和註解回覆](./media/first-comment-reply.png)

## <a name="next-steps"></a>後續步驟

在更正檢閱 PR 中記錄的重大問題之後，您必須[重新發行 Azure 應用程式供應項目](./create-new-azure-apps-offer.md#publish)。
