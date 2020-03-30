---
title: 對等服務 - 常見問題解答
titleSuffix: Azure
description: 對等服務 - 常見問題解答
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775468"
---
# <a name="peering-service---faqs"></a>對等服務 - 常見問題解答

您可以查看以下資訊，瞭解一般問題。

**運營商能否使用其現有的直接與 Microsoft 對等互連來支援對等互連服務？**

是的，運營商可以利用其現有的 PNI 來支援對等服務。 對等服務 PNI 需要多元化來支援 HA。 如果現有的 PNI 已經具有多樣性，則不需要新的基礎結構。 如果現有的 PNI 需要多樣性，則可以將其增強。

**運營商能否使用新的直接對等互連來支援對等互連服務？**

是的，這也是可能的。 微軟將與運營商合作，創建新的直接對等互連，以支援對等服務。  

**為什麼直接對等互連是支援對等服務的要求？**

對等互連服務背後的主要驅動因素之一是通過連接良好的 SP 與 Microsoft 線上服務提供連接。 PNI 始終位於 Gbps 範圍內，因此是運營商和 Microsoft 之間高輸送量連接的基本構建基塊。

**支援對等互連服務的直接對等互連有哪些多樣性要求？**

PNI 必須支援本地冗余和異地冗余。 本地冗余定義為特定對等網站中的兩組不同的路徑。 地理冗余要求開利在不同 Microsoft 邊緣網站具有其他連接，以防主網站發生故障。 對於短暫的故障持續時間，運營商可以路由流量通過備份網站。

**運營商已經提供SLA和企業級互聯網，這如何提供不同？**

一些運營商在其網路部分提供SLA和企業級互聯網。 在對等服務中，微軟將在微軟網路部分提供SLA流量。 通過選擇對等服務，客戶將獲得端到端的 SLA。 ISP 可以覆蓋從其網站到 ISP 網路上的 Microsoft 邊緣的 SLA。 微軟全球網路從微軟邊緣到最終使用者應用程式的SLA現在由微軟覆蓋。

**如果服務提供者已經使用 PNI 與 Microsoft 對等，則需要進行哪些類型的更改來支援對等互連服務？**

* 軟體更改以標識對等服務使用者及其流量。 可能需要路由策略更改才能通過對等互連服務連接在最接近的 Microsoft 邊緣交換使用者的流量。
* 確保連接具有本地冗余和異地冗余。
