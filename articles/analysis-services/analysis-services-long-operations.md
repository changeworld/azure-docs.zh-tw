---
title: Azure Analysis Services 中長時間執行作業的最佳作法 |Microsoft Docs
description: 本文說明長時間執行作業的最佳做法。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014829"
---
# <a name="best-practices-for-long-running-operations"></a>長時間執行作業的最佳做法

在 Azure Analysis Services 中， *節點* 代表伺服器資源正在其中執行的主機虛擬機器。 如果伺服器資源移至不同的節點，某些作業（例如長時間執行的查詢、重新整理作業和查詢相應放大同步處理）可能會失敗。 此案例中的常見錯誤訊息包括：

- 「嘗試找出長時間執行的 XMLA 要求時發生錯誤。 服務升級或伺服器重新開機時，要求可能已中斷。」
- 因為 <guid> 服務錯誤 (非使用) 狀態，所以已取消「識別碼」為模型 ' ' 的作業， <database> 因為發生訊息「正在取消重新整理要求，因為沒有任何更新。 這是內部服務的問題。 如果重複發生此問題，請重新提交作業或提交票證以取得協助。」

有許多原因可能會中斷長時間執行的作業。 例如，Azure 中的更新，例如： 
- 作業系統修補程式 
- 安全性更新
- Azure Analysis Services 服務更新
- Service Fabric 更新。 Service Fabric 是許多 Microsoft 雲端服務（包括 Azure Analysis Services）所使用的平臺元件。

除了在服務中發生的更新之外，還會在節點之間進行服務的自然移動，因為負載平衡。 節點移動是雲端服務的預期部分。 Azure Analysis Services 會嘗試將節點移動的影響降到最低，但無法完全消除它們。 

除了節點移動之外，還有其他可能發生的失敗。 例如，資料來源資料庫系統可能離線，或網路連線中斷。 如果在重新整理期間，資料分割有10000000個數據列，而在9個百萬分之一秒資料列發生失敗，則無法在失敗點重新開機重新整理。 服務必須從一開始重新開機。 

## <a name="refresh-rest-api"></a>重新整理 REST API

針對長時間執行的作業（例如資料重新整理），服務中斷可能很困難。 Azure Analysis Services 包含 REST API，可協助減輕服務中斷的負面影響。 若要深入瞭解，請參閱 [使用 REST API 的非同步](analysis-services-async-refresh.md)重新整理。
 
除了 REST API 之外，還有其他方法可以用來將長時間執行的重新整理作業中的潛在問題降至最低。 主要的目標是要避免從頭開始重新開機重新整理作業，而是以較小的批次來執行重新整理，而這些較小的批次可依階段進行認可。 
 
REST API 允許這類重新開機，但不允許完整的彈性來建立和刪除資料分割。 如果案例需要複雜的資料管理作業，您的解決方案應該在其邏輯中包含某種形式的批次處理。 例如，藉由使用交易來處理多個資料，個別的批次允許失敗，而不需要從頭開始，而是從中繼檢查點重新開機。 
 
## <a name="scale-out-query-replicas"></a>向外擴充查詢複本

無論使用 REST 或自訂邏輯，用戶端應用程式查詢在處理批次時，仍會傳回不一致或中繼結果。 如果在進行處理時需要用戶端應用程式查詢所傳回的一致資料，而且模型資料處於中繼狀態，則您可以使用向 [外擴充](analysis-services-scale-out.md) 與唯讀查詢複本。

藉由使用唯讀查詢複本，同時以批次方式執行重新整理，用戶端應用程式使用者可以繼續查詢唯讀複本上舊的資料快照集。 重新整理完成之後，就可以執行同步處理作業，使唯讀複本保持在最新狀態。


## <a name="next-steps"></a>後續步驟

[使用 REST API 進行非同步重新整理](analysis-services-async-refresh.md)  
[Azure Analysis Services 擴充](analysis-services-scale-out.md)  
[Analysis Services 的高可用性](analysis-services-bcdr.md)  
[Azure 服務的重試指引](/azure/architecture/best-practices/retry-service-specific)