---
title: 發佈 Azure 應用程式產品/服務 |Azure 應用商店
description: 說明在 Azure Marketplace 上發佈 Azure 應用程式供應項目的程序和步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280094"
---
# <a name="publish-azure-application-offer"></a>發佈 Azure 應用程式供應項目

在 [新增供應項目]**** 頁面提供資訊並建立供應項目後，即可發佈供應項目。 選取 [發佈]**** 即可啟動發佈程序。

下圖將展示供應項目「上線」發佈程序中主要的步驟。

![供應項目發佈步驟](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>發佈步驟的詳細說明

下表列出並描述每個發佈步驟，並提供完成每個步驟的時間估計。  以「天」為單位的時間估計，定義為工作天，這會排除週末及假日。

|  **發行步驟**           | **時間**    | **描述**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | < 15 分鐘    | 檢查供應項目資訊和供應項目設定。                        |
| 驗證受影響的營收設定 | < 15 分鐘  | 已檢查供應項目的 Azure 資源使用狀況。             |
| 認證                  | < 1 天     | Azure 認證團隊會分析供應項目。 掃描供應項目中是否有病毒、惡意程式碼、安全合規性和安全性問題。 檢查供應項目以查看它是否符合所有資格條件。 如需詳細資訊，請參閱[必要條件](./cpp-prerequisites.md)。 若發現問題，將提供意見反應。 |
| 試用產品驗證          | < 2 小時   | (選擇性) 如果有試用產品，則 Microsoft 會驗證它是否可以部署及複寫。  |
| 封裝和潛在客戶開發註冊 | < 1 小時  | 產品/服務的技術資產打包供客戶使用，並配置和部署潛在客戶系統。 |
|  發佈者登出             |  manual    | 供應項目上線前發行者最終檢閱與確認。 現在供應項目可供預覽。  您可以在所選訂用帳戶部署供應項目 (供應項目資訊步驟)，確認是否符合您所有要求。  確認該供應項目之後，請選取 [Go Live]****，您的供應項目就可以移至下一個步驟。 |
| Microsoft 檢閱                | 7 - 14 天 | Microsoft 會全面地檢閱您的 Azure 應用程式，如果發現問題就會傳送電子郵件給您。  此步驟的長度，取決於應用程式的複雜度、所發現的問題，以及您回應它們的速度。  |
| 即時                           | < 1 天 | 供應項目推出、複寫到指定的區域，並開放給大眾使用。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

您可以在 Cloud Partner 入口網站內供應項目的 [狀態]**** 索引標籤中，監視發佈程序。

![Azure 應用程式供應項目的 [狀態] 索引標籤](./media/offer-status-tab.png)

完成發佈程序之後，您的供應項目將會列在 [Microsoft Azure Marketplace 應用程式類別](https://azuremarketplace.microsoft.com/marketplace/apps/)中。

>[!Note]
>雲解決方案供應商 （CSP） 合作夥伴管道加入宣告現已可用。  有關通過 Microsoft CSP 合作夥伴管道行銷產品/服務的更多資訊[，請參閱雲解決方案供應商](../../cloud-solution-providers.md)。

## <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

除了顯示供應項目的發佈狀態，[狀態]**** 索引標籤也會顯示來自任何發生問題之發佈步驟的錯誤訊息和意見反應。  如果此問題很危急，則會取消發佈。  您接著必須更正所報告的問題，然後重新發佈該供應項目。  由於 **Microsoft 審核**步驟代表對您供應項目及其相關聯技術資產 (特別是 Azure Resource Manager 範本) 的廣泛審核，因此，通常會將問題顯示為提取要求 (PR) 連結。  如需如何檢視和回應這些 PR 的說明，請參閱[處理審核意見反應](./cpp-handling-review-feedback.md)。


## <a name="next-steps"></a>後續步驟

如果您在一或多個發佈步驟中遇到了錯誤，則必須加以更正，然後重新發佈您的供應項目。  如果在 **Microsoft 審核**步驟中發生重大問題，您必須藉由存取 Microsoft 審核小組的 Azure DevOps 存放庫來[處理審核意見反應](./cpp-handling-review-feedback.md)。

成功發佈 Azure 應用程式之後，您就能[更新現有供應項目](./cpp-update-existing-offer.md)，以反映變更的商務或技術需求。 
