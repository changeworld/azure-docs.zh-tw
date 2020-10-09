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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775468"
---
# <a name="peering-service---faqs"></a>對等互連服務-常見問題

您可以參閱以下有關一般問題的資訊。

**電訊廠商是否可以使用其現有的直接對等互連與 Microsoft 來支援對等互連服務？**

是的，電訊廠商可以利用其現有的 PNI 來支援對等互連服務。 對等互連服務 PNI 需要多樣性才能支援 HA。 如果現有的 PNI 已經有多樣性，則不需要任何新的基礎結構。 如果現有的 PNI 需要多樣性，則可以進行擴充。

**電訊廠商是否可使用新的直接對等互連與 Microsoft 來支援對等互連服務？**

是的，這也是可行的。 Microsoft 將與電訊廠商合作來建立新的直接對等互連，以支援對等互連服務。  

**為什麼直接對等互連需要支援對等互連服務？**

對等互連服務的主要驅動程式之一，是透過連接完善的 SP 來提供與 Microsoft 線上服務的連線。 PNI 一律會在 Gbps 的範圍內，因此是在貨運公司與 Microsoft 之間進行高輸送量連線的基本組建區塊。

**直接對等互連上有哪些多樣性需求可支援對等互連服務？**

PNI 必須支援本機冗余和異地冗余。 本機冗余是在特定對等互連網站中定義為兩組不同的路徑。 異地複寫需要電訊廠商在不同的 Microsoft edge 網站上有額外的連線，以防主要網站失敗。 針對短暫的失敗持續時間，電訊廠商可以透過備份網站來路由傳送流量。

**此電訊廠商已提供 SLA 和企業級網際網路，這項供應專案有何不同？**

某些電訊廠商會在網路的部分提供 SLA 和企業級網際網路。 在對等互連服務中，Microsoft 會在 Microsoft 的網路部分提供 SLA 供應專案流量。 藉由選取對等互連服務，客戶將獲得端對端 SLA。 ISP 可以涵蓋其網站到 ISP 網路上 Microsoft edge 的 SLA。 Microsoft 全球網路（從 Microsoft edge 到終端使用者應用程式）的 SLA 現在涵蓋于 Microsoft。

**如果服務提供者已使用 PNI 與 Microsoft 對等，則需要何種變更才能支援對等互連服務？**

* 用來識別對等互連服務使用者及其流量的軟體變更。 可能需要透過對等互連服務連線，在最接近的 Microsoft edge 上交換使用者流量的路由原則變更。
* 確定連線具有本機冗余和異地冗余。
