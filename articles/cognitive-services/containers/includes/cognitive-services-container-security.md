---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 瞭解如何保護容器
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876809"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 認知服務容器安全性

每當開發應用程式時,安全性都應該是主要重點。 安全性的重要性是衡量成功與否的指標。 構建包含認知服務容器的軟體解決方案時,瞭解可用的限制和功能至關重要。 有關網路安全的詳細資訊,請參閱[設定 Azure 認知服務虛擬網路][az-security]。

> [!IMPORTANT]
> 預設情況下,認知服務容器 API*上沒有安全性*。 原因是,大多數情況下,容器將作為由網路網橋從外部保護的 pod 的一部分運行。 但是,可以啟用與訪問[基於雲的認知服務][request-authentication]時使用的身份驗證相同的身份驗證。

下圖說明了預設和非**安全**方法:

![容器安全性](../media/container-security.svg)

作為一種替代方法*和安全*的方法,認知服務容器的消費者可以使用正面元件增強容器,從而保持容器終結點的私密性。 讓我們考慮一個方案,我們將[Istio][istio]用作入口閘道。 Istio 支援 HTTPS/TLS 和用戶端憑證身份驗證。 在這種情況下,Istio 前端公開容器訪問,顯示事先使用 Istio 白名單的用戶端證書。

[Nginx][nginx]是同一類別中的另一個受歡迎的選擇。 Istio 和 Nginx 都充當服務網格,並提供其他功能,包括負載平衡、路由和速率控制等功能。

### <a name="container-networking"></a>容器網路服務

認知服務容器需要提交計量資訊以用於計費目的。 唯一的例外是*離線容器*,因為它們遵循不同的計費方法。 如果不允許列出認知服務容器所依賴的各種網路通道,將阻止容器工作。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>允許清單認知服務網域和埠

主機應允許清單**連接埠 443**和以下網域:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>關閉深度封包檢查

> [深度封包檢查](https://en.wikipedia.org/wiki/Deep_packet_inspection)(DPI) 是一種數據處理類型,用於詳細檢查透過電腦網路發送的數據,並且通常透過阻止、重新路由或相應地記錄數據來採取措施。

在認知服務容器創建到 Microsoft 伺服器的安全通道上禁用 DPI。 否則將阻止容器正常工作。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
