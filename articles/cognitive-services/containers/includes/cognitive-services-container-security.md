---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 瞭解如何保護您的容器
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/11/2020
ms.author: aahi
ms.openlocfilehash: bb9b0da609169288521d21ee6d5e412a786c7549
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94573837"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 認知服務容器安全性

當您在開發應用程式時，安全性應該是主要焦點。 安全性的重要性是成功的度量。 當您在架構包含認知服務容器的軟體解決方案時，請務必瞭解您可用的限制和功能。 如需網路安全性的詳細資訊，請參閱 [設定 Azure 認知服務虛擬網路][az-security]。

> [!IMPORTANT]
> 根據預設，認知服務容器 API *沒有任何安全性* 。 這是因為容器最常作為 pod 的一部分執行，而該 pod 是由網路橋接外部保護。 不過，您可以啟用驗證，其運作方式與存取 [雲端式認知服務][request-authentication]時所使用的驗證相同。

下圖說明預設和 **不安全** 的方法：

![容器安全性](../media/container-security.svg)

作為替代且 *安全* 的方法，認知服務容器的取用者可以使用 front 元件增強容器，讓容器端點保持私用。 讓我們看看使用 [Istio][istio] 做為輸入閘道的案例。 Istio 支援 HTTPS/TLS 和用戶端憑證驗證。 在此案例中，Istio 前端會公開容器存取權，並呈現事先以 Istio 核准的用戶端憑證。

[Nginx][nginx] 是相同類別中的另一種常用選擇。 Istio 和 Nginx 都可作為服務網格，並提供額外的功能，包括負載平衡、路由和速率控制等。

### <a name="container-networking"></a>容器網路服務

需要有認知服務容器，才能提交計量資訊以供計費之用。 若無法允許列出認知服務容器所依賴的各種網路通道，將會導致容器無法運作。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>允許列出認知服務網域和埠

主機應該允許清單 **埠 443** 和下列網域：

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>停用深度封包檢查

> [深度封包檢查](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) 是一種資料處理方式，會詳細檢查透過電腦網路傳送的資料，而且通常會依適當的方式封鎖、重新路由傳送或記錄它來採取動作。

停用認知服務容器建立到 Microsoft 伺服器的安全通道上的 DPI。 若無法這樣做，將會導致容器無法正常運作。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
