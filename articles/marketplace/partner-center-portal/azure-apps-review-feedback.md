---
title: 在 Microsoft 商業 marketplace 中處理 Azure 應用程式供應專案的審查意見反應
description: 如何使用 Microsoft 合作夥伴中心上的商用 Marketplace 入口網站，處理 Azure 應用程式的審查意見反應，以在 Azure Marketplace、AppSource 或透過雲端解決方案提供者（CSP）方案中進行列出或銷售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 464f4d5434c8c0b8615e24eb1a323ae66a52273c
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744877"
---
# <a name="handling-review-feedback-for-azure-apps-offer-before-publishing-in-the-azure-marketplace"></a>在 Azure Marketplace 發佈之前，請先處理 Azure 應用程式供應專案的審查意見反應

本文說明如何存取 Microsoft Azure Marketplace 檢閱小組所使用的 Azure DevOps 環境。 如果在 **Microsoft 檢閱**步驟中，您的 Azure 應用程式供應項目中發現了嚴重問題，則可以登入此系統以檢視這些問題的的詳細資訊 (檢閱意見反應)。 修正所有問題之後，您必須重新提交供應項目，才能繼續將它發佈到 Azure Marketplace。 下圖說明此意見反應程序如何與發佈程序相關。

![審查意見反應流程](./media/review-feedback-process.png)

一般而言，檢閱問題會以提取要求 (PR) 參考。 每個 PR 都連結到線上 [Azure DevOps](https://azure.microsoft.com/services/devops/) (先前名稱為 Visual Studio Team Services (VSTS)) 項目，其中包含問題詳細資料。 下圖顯示在審查期間發現問題時，合作夥伴中心體驗的範例。 

![發佈狀態](./media/publishing-status.png)

包含提交相關特定詳細資料的 PR 會在「查看認證報告」連結中提及。 對於更複雜的情況，檢閱與支援小組可能也會透過電子郵件連絡您。

## <a name="azure-devops-access"></a>Azure DevOps 存取

在合作夥伴中心具有「開發人員」角色存取權的所有使用者，都可以存取審查意見反應中所參考的 PR 專案。

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

1. 在 [發行步驟] 表單的 [ **Microsoft 審核**] 區段中，按一下 pr 連結以啟動瀏覽器，並流覽至此 PR 的 [**總覽**（首頁）] 頁面。 下圖說明 Contoso 範例應用程式供應專案的 [重大問題] 首頁範例。 此頁面包含有關在 Azure 應用程式中找到的檢閱問題的實用摘要資訊。

    [![提取要求首頁](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *按一下以展開映像。*

1. 選擇性在視窗右側的 [**原則**] 區段中，按一下問題訊息（在此範例中為「**原則驗證失敗**」）來調查問題的低層級詳細資料，包括相關聯的記錄檔。 錯誤通常會顯示在記錄檔的底部。
1. 在首頁左側的功能表中，選取 [檔案]**** 以顯示包含此供應項目之技術資產的清單檔案。 Microsoft 檢閱者應該加上描述發現的重大問題的註解。 在下列範例中，已發現兩個問題。

    [![提取要求首頁](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *按一下以展開映像。*

1. 按一下左側樹狀目錄中的每個註解節點，以瀏覽至周圍程式碼內容中的註解。 修正小組專案中的原始程式碼，以更正註解中所述的問題。

>[!Note]
>您無法在檢閱小組的 Azure DevOps 環境內編輯供應項目的技術資產。 對於發行者，這是包含的原始程式碼的唯讀環境。 但是，您可以留下對註解的回覆，以便 Microsoft 檢閱小組可從中獲益。

   在下列範例中，發行者已檢閱、更正並回覆第一個問題。

   ![首先修正和註解回覆](./media/first-comment-reply.png)

## <a name="next-steps"></a>後續步驟

在更正檢閱 PR 中記錄的重大問題之後，您必須[重新發行 Azure 應用程式供應項目](./create-new-azure-apps-offer.md#publish)。
