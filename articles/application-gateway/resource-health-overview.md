---
title: Azure 應用程式閘道資源運行狀況概述
description: 本文概述了 Azure 應用程式閘道的資源運行狀況功能
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659498"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 應用程式閘道資源運行狀況概述

[Azure 資源健康狀態](../service-health/resource-health-overview.md)可協助您在 Azure 問題影響您的資源時進行診斷並取得支援。 它會通知您資源的目前及過去的健康狀態。 並提供可協助您減輕問題的技術支援。

對於應用程式閘道，資源運行狀況依賴于閘道發出的信號來評估它是否正常。 如果閘道不正常，資源運行狀況將分析其他資訊以確定問題的根源。 它還標識 Microsoft 正在執行的操作或您可以採取哪些措施來解決問題。

如需健康狀態評估方式的其他詳細資訊，請檢閱 [Azure 資源健康狀態](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)中的資源類型及健康狀態檢查完整清單。


應用程式閘道的運行狀況顯示為以下狀態之一：

## <a name="available"></a>可用

**"可用"** 狀態表示服務未檢測到任何影響資源運行狀況的事件。 如果閘道在過去 24 小時內從計畫外停機中恢復，您將看到 **"最近解決**"通知。

![可用運行狀況](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>無法使用

**不可用**狀態表示服務檢測到影響閘道運行狀況的持續平臺或非平臺事件。

### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基礎結構的多個元件觸發。 其中包括已排程的動作 (例如計劃性維護) 和非預期的事件 (例如非計劃性主機重新開機)。

資源健康狀態提供有關事件和復原程序的其他詳細資料。 即使您沒有作用中的 Microsoft 支援合約，它也可讓您連絡支援。

![不可用狀態](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

**"未知**運行狀況"狀態表示資源運行狀況已超過 10 分鐘未收到有關閘道的資訊。 此狀態不是閘道狀態的明確指示。 但它是故障排除過程中的一個重要資料點。

如果閘道按預期運行，狀態將在幾分鐘後更改為 **"可用**"。

如果您遇到問題，**未知**運行狀況可能表示平臺中的事件正在影響閘道。

![未知狀態](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>已降級

**降級**狀態表示閘道檢測到性能損失，儘管它仍然可供使用。

![已退退狀態](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>後續步驟

要瞭解對應用程式閘道 Web 應用程式防火牆 （WAF） 進行故障排除，請參閱[Azure 應用程式閘道的 Web 應用程式防火牆疑難排解 （WAF）。](web-application-firewall-troubleshoot.md)