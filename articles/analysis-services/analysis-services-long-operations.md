---
title: Azure Analysis Services 中長時間執行作業的最佳作法 |Microsoft Docs
description: 本文描述長時間執行作業的最佳作法。
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428104"
---
# <a name="best-practices-for-long-running-operations"></a>長時間執行作業的最佳作法

在 Azure Analysis Services 中，*節點*代表執行伺服器資源的主機虛擬機器。 如果伺服器資源移至不同的節點，某些作業（例如長時間執行的查詢、重新整理作業和查詢向外延展同步處理）可能會失敗。 此案例中的常見錯誤訊息包括：

- 「嘗試找出長時間執行的 XMLA 要求時發生錯誤。 要求可能已因服務升級或伺服器重新開機而中斷。」
- 「模型 '<database>' 的<guid>ID 為的作業已取消，因為發生服務錯誤（非使用中），訊息為「正在取消重新整理要求，因為它已停滯而沒有任何更新。」 這是內部服務的問題。 如果此問題重複發生，請重新提交作業或提出票證以取得協助。」

有許多原因會造成長時間執行的作業中斷。 例如，Azure 中的更新，例如： 
- 作業系統修補程式 
- 安全性更新
- Azure Analysis Services 服務更新
- Service Fabric 更新。 Service Fabric 是許多 Microsoft 雲端服務所使用的平臺元件，包括 Azure Analysis Services。

除了服務中發生的更新之外，還會在節點之間自然地移動服務，因為負載平衡。 節點移動是雲端服務的預期部分。 Azure Analysis Services 會嘗試將節點移動的影響降至最低，但無法完全排除它們。 

除了節點移動之外，還有其他可能發生的失敗。 例如，資料來源資料庫系統可能已離線，或網路連接中斷。 如果在重新整理期間，分割區有10000000個數據列，而9個百萬分之一秒資料列發生失敗，則無法在失敗點重新開機 refresh。 服務必須從一開始就重新開機。 

## <a name="refresh-rest-api"></a>重新整理 REST API

服務中斷對於長時間執行的作業（例如資料重新整理）來說可能很困難。 Azure Analysis Services 包括一個 REST API，可協助減輕服務中斷的負面影響。 若要深入瞭解，請參閱[使用 REST API 進行非同步](analysis-services-async-refresh.md)重新整理。
 
除了 REST API 以外，還有其他方法可讓您在長時間執行的重新整理作業期間，將潛在的問題降到最低。 主要的目標是要避免從頭開始重新開機更新作業，並改為以可分階段認可的較小批次執行重新整理。 
 
REST API 允許這類重新開機，但它不允許資料分割建立和刪除的完整彈性。 如果案例需要複雜的資料管理作業，您的解決方案應該在其邏輯中包含某種形式的批次處理。 例如，藉由使用交易來處理多個中的資料，個別的批次允許失敗，而不需要從頭開始，而是從中繼檢查點重新開機。 
 
## <a name="scale-out-query-replicas"></a>相應放大查詢複本

不論是使用 REST 還是自訂邏輯，在處理批次時，用戶端應用程式查詢仍然會傳回不一致或中繼結果。 如果在進行處理時需要用戶端應用程式查詢所傳回的一致資料，而且模型資料處於中繼狀態，您可以使用向[外](analysis-services-scale-out.md)延展與唯讀查詢複本。

藉由使用唯讀查詢複本，而重新整理會以批次方式執行，用戶端應用程式使用者可以繼續查詢唯讀複本上的舊資料快照集。 一旦重新整理完成後，就可以執行同步處理作業，讓唯讀複本保持在最新狀態。


## <a name="next-steps"></a>後續步驟

[使用 REST API 進行非同步重新整理](analysis-services-async-refresh.md)  
[Azure Analysis Services 擴充](analysis-services-scale-out.md)  
[Analysis Services 的高可用性](analysis-services-bcdr.md)  
[Azure 服務的重試指引](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

