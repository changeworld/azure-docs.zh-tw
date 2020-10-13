---
title: 網際網路對等互連-常見問題
titleSuffix: Azure
description: 網際網路對等互連-常見問題
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775494"
---
# <a name="internet-peering---faqs"></a>網際網路對等互連-常見問題

您可以參閱以下有關一般問題的資訊。

**網際網路對等互連與對等互連服務有何不同？**

對等互連服務是一項服務，旨在為其企業客戶提供企業級的公用 IP 連線能力給 Microsoft。 企業級網際網路包括透過 Isp 的連線能力，這些 Isp 具有與 Microsoft 的高輸送量連線能力，以及 HA 連線能力的冗余。 此外，使用者流量會針對最接近的 Microsoft Edge 進行延遲的優化。 對等互連服務是以與合作夥伴電訊廠商的對等互連為基礎。 與夥伴之間的對等互連連線必須是直接對等互連，而不是交換對等互連。 直接對等互連必須具有本機和異地冗余。

**什麼是舊版對等互連？**

使用 Azure PowerShell 設定的對等互連連線會以 Azure 資源的形式來管理。 先前設定的對等互連連線會儲存在我們的系統中做為舊版對等互連，您可以選擇將其轉換為 Azure 資源的管理。

**當呼叫 New-AzPeeringDirectConnectionObject 時，會提供哪些 IP 位址給 Microsoft 和對等裝置？**

當呼叫 New-AzPeeringDirectConnectionObject Cmdlet 時，會 (. b.. d/31) 的/31 位址，或輸入的/30 位址 () 。 第一個 IP 位址 (b.. d + 0) 會提供給對等的裝置，而第二個 IP 位址 (b. 將) 提供給 Microsoft 裝置。

**什麼是 New-AzPeeringDirectConnectionObject Cmdlet 中的 MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 參數？**

MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 參數代表對等互連希望 Microsoft 接受的 IPv4 和 IPv6 首碼的最大數目。 您可以隨時修改這些參數。