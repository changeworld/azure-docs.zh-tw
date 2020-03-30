---
title: Azure 流分析中的資料保護
description: 本文介紹如何加密 Azure 流分析作業使用的私有資料。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299391"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 流分析中的資料保護 

Azure 流分析是一個完全託管的平臺即服務，允許您構建即時分析管道。 所有繁重的工作（如群集預配、縮放節點以適應您的使用方式和管理內部檢查點）都在幕後進行管理。

## <a name="encrypt-your-data"></a>加密您的資料

流分析可在其基礎架構中自動採用一流的加密標準來加密和保護您的資料。 您可以簡單地信任流分析安全地存儲所有資料，這樣您就不必擔心管理基礎結構。

如果要使用客戶管理的金鑰 （CMK） 加密資料，可以使用自己的存儲帳戶（通用 V1 或 V2）來存儲流分析運行時所需的任何私有資料資產。 您的存儲帳戶可以根據需要進行加密。 流分析基礎結構不會永久存儲任何私有資料資產。 

此設置必須在創建流分析作業時進行配置，並且不能在整個作業生命週期中對其進行修改。 不建議修改或刪除流分析正在使用的存儲。 如果刪除存儲帳戶，將永久刪除所有私有資料資產，這將導致作業失敗。 

無法使用流分析門戶更新或旋轉存儲帳戶的金鑰。 您可以使用 REST API 更新金鑰。


## <a name="configure-storage-account-for-private-data"></a>為私有資料配置存儲帳戶 

使用以下步驟為專用資料資產配置存儲帳戶。 此配置來自流分析作業，而不是來自存儲帳戶。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 選取 Azure 入口網站左上角的 [建立資源]****。 

1. 從結果清單中選擇 **分析** > **流分析作業** 。 

1. 填寫"流分析"作業頁，並填寫名稱、區域和縮放等必要詳細資訊。 

1. 選中"*保護存儲帳戶中此作業所需的所有私有資料資產"核取方塊*。

1. 從訂閱中選擇存儲帳戶。 請注意，無法在整個作業的整個生命週期中修改此設置。 

   ![專用資料存儲帳戶設置](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>存儲的私有資料資產

流分析需要保留的任何私有資料都存儲在您的存儲帳戶中。 私有資料資產的示例包括： 

* 您創作的查詢及其相關配置  

* 使用者自訂函數 

* 流分析運行時所需的檢查點

* 參考資料的快照 

資源的連接詳細資訊（由流分析作業使用）也會存儲。 加密存儲帳戶以保護所有資料。 

為了説明您在任何受監管的行業或環境中履行合規義務，您可以閱讀更多有關[Microsoft 合規產品的產品](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。 

## <a name="next-steps"></a>後續步驟

* [創建 Azure 存儲帳戶](../storage/common/storage-account-create.md)
* [了解 Azure 串流分析的輸入](stream-analytics-add-inputs.md)
* [Azure 串流分析作業中的檢查點和重新執行概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)
