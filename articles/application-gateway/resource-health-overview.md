---
title: Azure 應用程式閘道資源健康狀態總覽
description: 本文概述 Azure 應用程式閘道的資源健康狀態功能
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67659498"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 應用程式閘道資源健康狀態總覽

[Azure 資源健康狀態](../service-health/resource-health-overview.md) 可協助您在 Azure 問題影響您的資源時進行診斷並取得支援。 它會通知您資源的目前及過去的健康狀態。 並提供可協助您減輕問題的技術支援。

針對應用程式閘道，資源健康狀態會依賴閘道發出的信號來評估其狀況是否良好。 如果閘道狀況不良，資源健康狀態會分析其他資訊，以判斷問題的來源。 它也會識別 Microsoft 所採取的動作，或您可以做什麼來修正問題。

如需健康狀態評估方式的其他詳細資訊，請檢閱 [Azure 資源健康狀態](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)中的資源類型及健康狀態檢查完整清單。


應用程式閘道的健全狀況狀態會顯示為下列其中一個狀態：

## <a name="available"></a>可用

[**可用**] 狀態表示服務尚未偵測到任何會影響資源健康情況的事件。 當閘道在過去24小時內從未規劃的停機時間復原時，您會看到**最近已解決**的通知。

![可用的健全狀況狀態](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>無法使用

**無法使用**的狀態表示服務偵測到正在進行的平臺或非平臺事件會影響閘道的健康情況。

### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基礎結構的多個元件觸發。 其中包括已排程的動作 (例如計劃性維護) 和非預期的事件 (例如非計劃性主機重新開機)。

資源健康狀態提供有關事件和復原程序的其他詳細資料。 即使您沒有作用中的 Microsoft 支援合約，它也可讓您連絡支援。

![無法使用的狀態](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

[**未知**的健全狀況] 狀態表示資源健康狀態未收到閘道的相關資訊超過10分鐘。 此狀態不是閘道狀態的明確指示。 但在疑難排解程式中，這是很重要的資料點。

如果閘道如預期般執行，則狀態會在幾分鐘後變更為 [**可用**]。

如果您遇到問題，則**未知**的健全狀況狀態可能會建議平臺中的事件影響到閘道。

![未知的狀態](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>已降級

**降級**的健全狀況狀態表示您的閘道已偵測到效能降低，但仍可供使用。

![Degrated 狀態](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>後續步驟

若要深入瞭解應用程式閘道 Web 應用程式防火牆（WAF）的疑難排解，請參閱[疑難排解適用于 Azure 應用程式閘道的 Web 應用程式防火牆（WAF）](web-application-firewall-troubleshoot.md)。