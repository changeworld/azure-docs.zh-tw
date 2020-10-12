---
title: 分析 Azure CDN 使用模式
description: 本文說明適用於 Azure CDN 產品的不同分析報告類型。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/22/2020
ms.author: allensu
ms.openlocfilehash: 169889dbb87d00fdde44ff72c0d2004c331604ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073047"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>分析 Azure CDN 使用模式

啟用應用程式的 CDN 後，您可以監視 CDN 使用情況、檢查傳遞的健康情況，並為可能的問題進行疑難排解。 Azure CDN 會以下列方式提供這些功能： 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>來自 Microsoft 的 Azure CDN 原始記錄
使用標準的 Microsoft 設定檔時，您可以啟用原始記錄，並選取將記錄串流至：

* Azure 儲存體
* 事件中樞
* Azure Log Analytics

您可以使用 Azure Log Analytics 來查看監視計量，以及設定警示。 

如需詳細資訊，請參閱 [AZURE CDN HTTP 原始記錄](enable-raw-logs.md)。


## <a name="core-analytics-via-azure-diagnostic-logs"></a>透過 Azure 診斷記錄的核心分析

核心分析適用於所有定價層的 CDN 端點。 Azure 診斷記錄可讓您將核心分析匯出至 Azure 儲存體、事件中樞或 Azure 監視器記錄。 Azure 監視器記錄提供具有使用者可設定和自訂之圖形的解決方案。 如需 Azure 診斷記錄的詳細資訊，請參閱 [Azure 診斷記錄](cdn-azure-diagnostic-logs.md)。

## <a name="verizon-core-reports"></a>Verizon 核心報告

來自**Verizon 的 AZURE Cdn 標準**或**來自 Verizon 設定檔的 azure cdn Premium**提供核心報告。 您可以在 Verizon 補充入口網站中查看核心報表。 Verizon 核心報表可透過 Azure 入口網站的 [ **管理** ] 選項存取，並提供不同類型的圖形和視圖。 如需詳細資訊，請參閱 [Verizon 核心報告](cdn-analyze-usage-patterns.md)。

## <a name="verizon-custom-reports"></a>Verizon 自訂報告

來自**Verizon 的 AZURE Cdn 標準**或**來自 Verizon 設定檔的 azure cdn Premium**提供自訂報告。 您可以在 Verizon 補充入口網站中查看自訂報表。 Verizon 自訂報表可透過 Azure 入口網站的 [ **管理** ] 選項來存取。 

自訂報表會顯示每個邊緣 CNAME 的點擊次數或傳輸的資料。 資料會依 HTTP 回應碼或快取狀態，以一段時間來分組。 如需詳細資訊，請參閱 [Verizon 自訂報告](cdn-verizon-custom-reports.md)。

## <a name="azure-cdn-premium-from-verizon-reports"></a>來自 Verizon 的進階 Azure CDN 報告

您也可以使用**來自 Verizon 的 Azure CDN 進階** 來存取下列報告：
   * [進階 HTTP 報告](cdn-advanced-http-reports.md)
   * [即時統計資料](cdn-real-time-stats.md)
   * [Azure CDN 邊緣節點效能](cdn-edge-performance.md)

## <a name="next-steps"></a>接下來的步驟
在本文中，您已瞭解 Azure CDN 的不同分析報告選項。

如需有關 Azure CDN 和本文所述其他 Azure 服務的詳細資訊，請參閱：

* [什麼是 Azure CDN？](cdn-overview.md)
* [Azure CDN HTTP 原始記錄](enable-raw-logs.md)


