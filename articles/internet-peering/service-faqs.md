---
title: 對等互連服務-常見問題
titleSuffix: Azure
description: 對等互連服務-常見問題
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775468"
---
# <a name="peering-service---faqs"></a>對等互連服務-常見問題

如需一般問題，您可以參閱以下資訊。

**貨運公司是否可以使用其現有的直接對等互連與 Microsoft 來支援對等互連服務？**

是，電訊廠商可以利用其現有的 PNI 來支援對等互連服務。 對等互連服務 PNI 需要多樣性才能支援 HA。 如果現有的 PNI 已經有多樣性，則不需要任何新的基礎結構。 如果現有的 PNI 需要多樣性，則可以擴充。

**電訊廠商是否可以使用與 Microsoft 的新直接對等互連來支援對等服務？**

沒錯，這也是可行的。 Microsoft 會與電訊廠商合作，建立新的直接對等互連以支援對等服務。  

**為什麼直接對等需要支援對等互連服務？**

對等互連服務背後的主要驅動程式之一，是透過連線良好的 SP，提供 Microsoft 線上服務連接。 PNI 一律會在 Gbps 範圍內，因此是在電訊廠商與 Microsoft 之間進行高輸送量連線的基本建立區塊。

**直接對等互連支援對等互連服務有哪些多樣性需求？**

PNI 必須支援本機冗余和異地冗余。 本機冗余會定義為特定對等互連網站中的兩組不同路徑。 異地冗余要求在主要網站失敗時，電訊廠商必須在不同的 Microsoft edge 網站上有額外的連線能力。 在短時間內，電訊廠商可以透過備份網站路由傳送流量。

**此電訊廠商已提供 SLA 和企業級網際網路，此供應專案有何不同？**

有些電訊廠商會在網路中提供 SLA 和企業級的網際網路。 在對等互連服務中，Microsoft 會在 Microsoft 的網路部分提供 SLA 供應專案流量。 選取 [對等服務] [客戶] 可取得端對端 SLA。 ISP 可能會涵蓋其網站到 ISP 網路上 Microsoft edge 的 SLA。 Microsoft 全球網路中 microsoft edge 到終端使用者應用程式的 SLA 現已由 Microsoft 涵蓋。

**如果服務提供者已經使用 PNI 與 Microsoft 對等，則需要何種變更才能支援對等服務？**

* 軟體變更，用以識別對等互連服務使用者及其流量。 可能需要進行路由原則變更，以透過對等互連服務連線，在最接近的 Microsoft edge 交換使用者的流量。
* 請確定連線具有本機冗余和異地冗余。
