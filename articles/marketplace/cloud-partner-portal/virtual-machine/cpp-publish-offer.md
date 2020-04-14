---
title: 在 Azure 應用商店中發佈虛擬機產品/服務
description: 列出將現有虛擬機器供應項目發佈至 Azure Marketplace 的必要步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: bb875a5c4ab1b898b64fe22140414e5d5b7830b8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273778"
---
# <a name="publish-a-virtual-machine-offer"></a>發佈虛擬機器供應項目

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始將 Azure 虛擬機產品的管理移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照創建 Azure[虛擬機器產品/服務](https://aka.ms/CreateAzureVMoffer)中的說明進行操作,以管理遷移的優惠。

 在入口網站設定供應項目，並建立相關技術資產後，最後一步就是提交供應項目以供發佈。 下圖說明「上線」發佈流程中的主要步驟：

![虛擬機器供應項目發佈步驟](./media/publishvm_013.png)

下表說明這些步驟，並提供完成所需預估最多時間：
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **發行步驟**           | **時間**    | **說明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | 15 分鐘   | 檢查供應項目資訊和供應項目設定。                        |
| 檢查試用產品 (選擇性) | 2 小時 | 如果已選取啟用試用產品，Microsoft 會透過選取區域，檢查試用產品設定、部署及複寫作業。 |
| 認證                  | 3 天 | Azure 認證團隊會分析供應項目。 此步驟會掃描病毒、惡意程式碼，並檢查安全合規性和安全性問題。 若發現問題，將提供意見反應。 |
| 佈建                   | 4 天   | 在市集生產系統複寫 VM 供應項目。               |
| 封裝和潛在客戶開發註冊 | \< 1 小時  | 產品/服務的技術資產打包供客戶使用,並配置和設置引線系統。 |
|  發行者簽核             |  -        | 供應項目上線前發行者最終檢閱與確認。 您可以在所選訂用帳戶部署供應項目 (供應項目資訊步驟)，確認是否符合您所有要求。  |
| 佈建                   | 4 天 | 在市集生產系統和區域中，複寫最終完成的 VM 供應項目。 | 
| 即時                           | 4 天 | VM 供應項目發行、複寫到必要區域，並公開上架。 |
|  |  |

這個流程最多需 16 天才能完成。  完成這些發佈步驟後，您的 VM 供應項目會上架至 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)。 

