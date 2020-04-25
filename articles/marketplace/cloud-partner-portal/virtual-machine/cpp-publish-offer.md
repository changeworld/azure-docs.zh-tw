---
title: 在 Azure Marketplace 中發佈虛擬機器供應專案
description: 列出將現有虛擬機器供應項目發佈至 Azure Marketplace 的必要步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147665"
---
# <a name="publish-a-virtual-machine-offer"></a>發佈虛擬機器供應項目

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 Azure 虛擬機器供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)專案中的指示來管理您的已遷移優惠。

 在入口網站設定供應項目，並建立相關技術資產後，最後一步就是提交供應項目以供發佈。 下圖說明「上線」發佈流程中的主要步驟：

![虛擬機器供應項目發佈步驟](./media/publishvm_013.png)

下表說明這些步驟，並提供完成所需預估最多時間：
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **發行步驟**           | **時間**    | **描述**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | 15 分鐘   | 檢查供應項目資訊和供應項目設定。                        |
| 檢查試用產品 (選擇性) | 2 小時 | 如果已選取啟用試用產品，Microsoft 會透過選取區域，檢查試用產品設定、部署及複寫作業。 |
| 認證                  | 3 天 | Azure 認證團隊會分析供應項目。 此步驟會掃描病毒、惡意程式碼，並檢查安全合規性和安全性問題。 若發現問題，將提供意見反應。 |
| 佈建                   | 4 天   | 在市集生產系統複寫 VM 供應項目。               |
| 封裝和潛在客戶開發註冊 | \< 1 小時  | 供應專案的技術資產會封裝供客戶使用，並設定並設定潛在客戶系統。 |
|  發行者簽核             |  -        | 供應項目上線前發行者最終檢閱與確認。 您可以在所選訂用帳戶部署供應項目 (供應項目資訊步驟)，確認是否符合您所有要求。  |
| 佈建                   | 4 天 | 在市集生產系統和區域中，複寫最終完成的 VM 供應項目。 | 
| 即時                           | 4 天 | VM 供應項目發行、複寫到必要區域，並公開上架。 |
|  |  |

這個流程最多需 16 天才能完成。  完成這些發佈步驟後，您的 VM 供應項目會上架至 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)。 

