---
title: Azure 分析服務中長時間運行操作的最佳做法 |微軟文件
description: 本文介紹了長時間運行操作的最佳做法。
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428104"
---
# <a name="best-practices-for-long-running-operations"></a>長時間執行操作的最佳做法

在 Azure 分析服務中,*節點*表示伺服器資源正在運行的主機虛擬機器。 如果伺服器資源移動到其他節點,某些操作(如長時間運行的查詢、刷新操作和查詢橫向擴展同步)可能會失敗。 此專案中的常見錯誤訊息包括:

- 嘗試查找長時間運行的 XMLA 請求時出錯。 請求可能已被服務升級或伺服器重新啟動中斷。
- 由於服務錯誤(不<guid>活動),"<database>使用 ID' 的"模型"的工作被取消,消息"取消刷新請求,因為它被卡住,沒有任何更新。 這是一個內部服務問題。 如果此問題再次發生,請重新提交作業或提交票證以獲得説明。

長時間運行操作可能會中斷的原因有很多。 例如,Azure 中的更新,例如: 
- 作業系統修補程式 
- 安全性更新
- Azure 分析服務服務更新
- 服務結構更新。 Service Fabric 是許多 Microsoft 雲服務(包括 Azure 分析服務)使用的平臺元件。

除了服務中發生的更新之外,由於負載平衡,服務在節點之間自然移動。 節點移動是雲服務的預期部分。 Azure 分析服務嘗試將節點移動的影響降至最低,但不可能完全消除它們。 

除了節點移動之外,還可能發生其他故障。 例如,數據源資料庫系統可能處於離線狀態或網路連接丟失。 如果在刷新期間,分區具有 1000 萬行,並且第 9 百萬行發生故障,則無法在失敗點重新啟動刷新。 服務必須從一開始就重新開始。 

## <a name="refresh-rest-api"></a>刷新 REST API

對於長時間運行的操作(如數據刷新),服務中斷可能具有挑戰性。 Azure 分析服務包括 REST API,以幫助減輕服務中斷的負面影響。 要瞭解更多資訊,請參閱[使用 REST API 進行非同步刷新](analysis-services-async-refresh.md)。
 
除了 REST API 之外,您還可以使用其他方法在長時間運行刷新操作期間最小化潛在問題。 主要目標是避免從頭開始重新啟動刷新操作,而是以較小的批處理執行刷新,這些批處理可以分階段提交。 
 
REST API 允許此類重新啟動,但它不允許完全靈活地創建和刪除分區。 如果方案需要複雜的數據管理操作,則解決方案應在其邏輯中包括某種形式的批處理。 例如,通過使用事務處理多個單獨批處理中的數據,可以失敗,不需要從頭開始重新啟動,而是從中間檢查點重新啟動。 
 
## <a name="scale-out-query-replicas"></a>橫向延伸查詢複本

無論是使用 REST 還是自訂邏輯,用戶端應用程式查詢仍可在處理批次處理時返回不一致或中間的結果。 如果在處理期間需要客戶端應用程式查詢傳回的一致資料,並且模型資料處於中間狀態,則可以使用唯讀查詢複本橫向[擴充](analysis-services-scale-out.md)。

通過使用唯讀查詢副本,在分批執行刷新時,用戶端應用程式用戶可以繼續查詢唯讀副本上資料的舊快照。 刷新完成後,可以執行 Synch 操作以使唯讀副本保持最新。


## <a name="next-steps"></a>後續步驟

[使用 REST API 進行非同步重新整理](analysis-services-async-refresh.md)  
[Azure Analysis Services 擴充](analysis-services-scale-out.md)  
[Analysis Services 的高可用性](analysis-services-bcdr.md)  
[重試 Azure 服務指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

