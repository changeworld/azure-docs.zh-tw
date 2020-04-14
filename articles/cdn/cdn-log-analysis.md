---
title: 分析 Azure CDN 使用模式 | Microsoft Docs
description: 本文說明適用於 Azure CDN 產品的不同分析報告類型。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253606"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>分析 Azure CDN 使用模式

啟用應用程式的 CDN 後，您可以監視 CDN 使用情況、檢查傳遞的健康情況，並為可能的問題進行疑難排解。 Azure CDN 會以下列方式提供這些功能： 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>透過 Azure 診斷記錄的核心分析

核心分析適用於所有定價層的 CDN 端點。 Azure 診斷日誌允許將核心分析匯出到 Azure 存儲、事件中心或 Azure 監視器日誌。 Azure 監視器日誌提供了一個包含可使用者可配置和可自訂圖形的解決方案。 如需 Azure 診斷記錄的詳細資訊，請參閱 [Azure 診斷記錄](cdn-azure-diagnostic-logs.md)。

## <a name="verizon-core-reports"></a>Verizon 核心報告

身為具有**來自 Verizon 的 Azure CDN 標準**或**來自 Verizon 的 Azure CDN 進階**設定檔的 Azure CDN 使用者，您可以在 Verizon 補充入口網站中檢視 Verizon 核心報告。 Verizon 核心報告可透過 Azure 入口的 **「管理」** 選項存取,並提供各種圖形和檢視。 如需詳細資訊，請參閱 [Verizon 核心報告](cdn-analyze-usage-patterns.md)。

## <a name="verizon-custom-reports"></a>Verizon 自訂報告

身為具有**來自 Verizon 的 Azure CDN 標準**或**來自 Verizon 的 Azure CDN 進階**設定檔的 Azure CDN 使用者，您可以在 Verizon 補充入口網站中檢視 Verizon 自訂報告。 Verizon 自定義報表可透過 Azure 入口的 **「管理**」選項進行存取。 [Verizon 自訂報告] 頁面會針對屬於 Azure CDN 設定檔的每個邊緣 CName 顯示點擊次數或傳輸的資料。 此資料可依照任何一段時間的 HTTP 回應碼或快取狀態進行分組。 如需詳細資訊，請參閱 [Verizon 自訂報告](cdn-verizon-custom-reports.md)。

## <a name="azure-cdn-premium-from-verizon-reports"></a>來自 Verizon 的進階 Azure CDN 報告

您也可以使用**來自 Verizon 的 Azure CDN 進階** 來存取下列報告：
   * [進階 HTTP 報告](cdn-advanced-http-reports.md)
   * [即時統計資料](cdn-real-time-stats.md)
   * [邊緣節點效能](cdn-edge-performance.md)

