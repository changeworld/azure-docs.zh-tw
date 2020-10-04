---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711191"
---
Azure 儲存體會提供下列 Azure 監視器交易計量。

| 計量 | 描述 |
| ------------------- | ----------------- |
| 交易 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 <br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 適用維度：ResponseType、GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions))<br/> 值範例：1024 |
| 輸入 | 輸入資料量。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| 輸出 | 輸出資料量。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessServerLatency | Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。 <br/><br/> 單位：毫秒 <br/> 彙總類型：Average <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessE2ELatency | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 <br/><br/> 單位：毫秒 <br/> 彙總類型：Average <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| 可用性 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將可計費的要求值總計除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 <br/><br/> 單位：百分比 <br/> 彙總類型：Average <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：99.99 |