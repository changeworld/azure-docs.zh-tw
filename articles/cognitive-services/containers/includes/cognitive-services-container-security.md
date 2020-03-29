---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 瞭解如何保護容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272926"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 認知服務容器安全性

每當開發應用程式時，安全性都應該是主要重點。 安全性的重要性是衡量成功與否的指標。 構建包含認知服務容器的軟體解決方案時，瞭解可用的限制和功能至關重要。 有關網路安全的詳細資訊，請參閱[配置 Azure 認知服務虛擬網路][az-security]。

> [!IMPORTANT]
> 預設情況下，認知服務容器 API*上沒有安全性*。 原因是，大多數情況下，容器將作為由網路橋接器從外部保護的 pod 的一部分運行。 但是，可以啟用與訪問[基於雲的認知服務][request-authentication]時使用的身份驗證相同的身份驗證。

下圖說明瞭預設和非**安全**方法：

![容器安全性](../media/container-security.svg)

作為一種替代方法*和安全*的方法，認知服務容器的消費者可以使用正面元件增強容器，從而保持容器終結點的私密性。 讓我們考慮一個方案，我們將[Istio][istio]用作入口閘道。 Istio 支援 HTTPS/TLS 和用戶端憑證身份驗證。 在這種情況下，Istio 前端公開容器訪問，顯示事先使用 Istio 白名單的用戶端憑證。

[Nginx][nginx]是同一類別中的另一個受歡迎的選擇。 Istio 和 Nginx 都充當服務網格，並提供其他功能，包括負載平衡、路由和速率控制等功能。

### <a name="container-networking"></a>容器網路服務

認知服務容器需要提交計量資訊以用於計費目的。 唯一的例外是*離線容器*，因為它們遵循不同的計費方法。 如果不允許列出認知服務容器所依賴的各種網路通道，將阻止容器工作。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>允許清單認知服務域和埠

主機應允許清單**埠 443**和以下域：

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>禁用深度資料包檢查

> [深度資料包檢查](https://en.wikipedia.org/wiki/Deep_packet_inspection)（DPI） 是一種資料處理類型，用於詳細檢查通過電腦網路發送的資料，並且通常通過阻止、重新路由或相應地記錄資料來採取措施。

在認知服務容器創建到 Microsoft 伺服器的安全通道上禁用 DPI。 否則將阻止容器正常工作。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
