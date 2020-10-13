---
title: Azure 應用程式供應項目的檢閱意見反應 - Microsoft 商業市集
description: 如何從 Microsoft Azure Marketplace 檢閱小組處理 Azure 應用程式供應項目的意見反應。 您可以使用合作夥伴中心認證來存取 Azure DevOps 中的意見反應。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: aecc64c5fa923bc29d34efd3969c2c12f30153c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320097"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>處理 Azure 應用程式供應項目的檢閱意見反應

本文說明如何存取 [Azure DevOps](https://azure.microsoft.com/services/devops/) 中來自 Microsoft Azure Marketplace 檢閱小組的意見反映。 如果在 **Microsoft 檢閱**步驟中，您的 Azure 應用程式供應項目中發現了嚴重問題，則可以登入此系統以檢視這些問題的詳細資訊 (檢閱意見反應)。 修正所有問題之後，您必須重新提交供應項目，才能繼續將其發佈到 Azure Marketplace。 下圖說明此意見反應程序如何與發佈程序相關。

![檢閱意見反應程序](./media/review-feedback-process.png)

一般而言，檢閱問題會作為提取要求 (PR) 來參考。 每個 PR 都會連結到線上 Azure DevOps 項目，其中包含問題詳細資料。 下圖顯示在檢閱期間發現問題時，合作夥伴中心體驗的範例。 

![發佈狀態](./media/publishing-status.png)

包含特定提交相關詳細資料的 PR 會在「檢閱認證報告」連結中提及。 對於更複雜的情況，檢閱與支援小組可能也會透過電子郵件連絡您。

## <a name="azure-devops-access"></a>Azure DevOps 存取

在合作夥伴中心具有「開發人員」角色存取權的所有使用者，都有權查看檢閱意見反應中所參考的 PR 項目。

## <a name="reviewing-the-pull-request"></a>檢閱提取要求

您可以使用下列程序來檢閱提取要求中所述的問題。

1. 在 [發佈步驟] 表單的 [Microsoft 檢閱] 區段中，選取 PR 連結以啟動您的瀏覽器並瀏覽至此 PR 的 [概觀 (首頁)] 頁面。 下圖說明 Contoso 範例應用程式供應項目的嚴重問題首頁範例。 此頁面包含有關在 Azure 應用程式中找到的檢閱問題的實用摘要資訊。

    [![](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)提取要求首頁
    <br/> 按一下以展開此影像。

1. (選擇性) 在視窗右側的 [原則] 區段中，選取問題訊息 (在此範例中：**原則驗證失敗**) 調查問題的低層級詳細資料，包括相關的記錄檔。 錯誤通常會顯示在記錄檔的底部。

1. 在首頁左側的功能表中，選取 [檔案] 以顯示包含此供應項目之技術資產的清單檔案。 Microsoft 檢閱者應該加上描述發現的重大問題的註解。 在下列範例中，已發現兩個問題。

    [![提取要求首頁](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 按一下以展開此影像。

1. 選取左側樹狀目錄中的每個註解節點，以瀏覽至周圍程式碼內容中的註解。 修正小組專案中的原始程式碼，以更正註解中所述的問題。

>[!Note]
>您無法在檢閱小組的 Azure DevOps 環境內編輯供應項目的技術資產。 對於發行者，這是包含的原始程式碼的唯讀環境。 但是，您可以留下對註解的回覆，以便 Microsoft 檢閱小組可從中獲益。

   在下列範例中，發行者已檢閱、更正並回覆第一個問題。

   ![首先修正和註解回覆](./media/first-comment-reply.png)

## <a name="next-steps"></a>後續步驟

在更正檢閱 PR 中記錄的重大問題之後，您必須[重新發行 Azure 應用程式供應項目](./create-new-azure-apps-offer.md#publish)。
