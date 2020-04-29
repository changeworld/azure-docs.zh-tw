---
title: 查看 marketplace 供應專案的狀態 |Azure Marketplace
description: 使用 Cloud Partner 入口網站檢視 Azure 和 AppSource Marketplace 上供應項目的狀態
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275963"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>檢視 Azure Marketplace 和 AppSource 供應項目的發佈狀態

當您建立供應項目之後 (尤其是在發佈程序期間)，您可以在 Cloud Partner 入口網站中檢視您供應項目的狀態。  整體發佈狀態可於入口網站的[**所有供應項目**](../portal-tour/cpp-all-offers-page.md)和[**核准**](../portal-tour/cpp-approvals-page.md)頁面取得。  每個供應項目應該都會顯示下列狀態指標的其中一個。  

|            狀態              |   描述                                                           |
|            ------              |   -----------                                                           |
| **-**                          | 已經建立供應項目，但發佈程序尚未開始。            |
| **正在發行**        | 供應項目正在進行發佈程序的步驟。   |
| **發行失敗**             | 由 Microsoft 驗證或檢閱的期間發現重大問題。 |
| **發行已取消**           | 發行者已取消供應項目發佈程序。  此狀態不會將市集中的現有供應項目取消列入。 | 
| **正在等待發行者登出** | 供應項目已由 Microsoft 檢閱，正在等待發行者進行最終驗證。 |
| **取消列入**                   | 市集中先前發佈的供應項目已經被移除。      | 
|  |  |


## <a name="publishing-status-details"></a>發佈狀態詳細資料 

供應項目在進行發佈程序時，其詳細資料可以在 [新增供應項目]**** 頁面的 [狀態]**** 索引標籤中取得。  此頁面會列出該供應項目類型的所有發佈步驟。  請注意，不同供應項目類型之間的步驟數目與特定步驟通常也不相同。**  此頁面也會指出 Microsoft 驗證和檢閱步驟所發現的任何重要問題，發行者通常需要採取行動，發佈程序才能繼續。  例如，下列影像顯示新虛擬機器供應項目的 [狀態]**** 索引標籤。 

![VM 供應項目的 [狀態] 索引標籤](./media/vm-offer-pub-steps1.png)

下一個範例是諮詢服務的 [狀態]**** 索引標籤，顯示潛在客戶管理中已回報的錯誤。  因為潛在客戶管理對於諮詢服務是必要的，所以必須更正此錯誤才能繼續發佈。

![諮詢服務的 [狀態] 索引標籤顯示錯誤](./media/consulting-service-error.png)

最後一個 Azure 應用程式狀態的範例顯示重大 Microsoft 檢閱問題。  其中包含 Azure DevOps 專案的熱連結，其中包含有關此審查問題的詳細資訊。  如需詳細資訊，請參閱[發佈 Azure 應用程式供應項目](cpp-publish-offer.md)。

![顯示檢閱問題的 Azure 應用程式 [狀態] 索引標籤](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>後續步驟

若要更正重要問題或更新供應項目設定，您必須[更新供應項目](./cpp-update-offer.md)。 
