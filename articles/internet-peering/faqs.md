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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775494"
---
# <a name="internet-peering---faqs"></a>網際網路對等互連-常見問題

如需一般問題，您可以參閱以下資訊。

**網際網路對等互連與對等服務之間有何差異？**

對等互連服務是一種服務，旨在為其企業客戶提供企業級的公用 IP 連線能力給 Microsoft。 企業級網際網路包括透過具有高輸送量連線到 Microsoft 的 Isp，以及 HA 連線能力的冗余來連接。 此外，使用者流量已針對最接近 Microsoft Edge 的延遲進行優化。 對等互連服務建基於與合作夥伴電信公司的對等互連連線。 與合作夥伴的對等互連連線必須是直接對等互連，而不是交換對等互連。 直接對等互連必須具有本機和異地冗余。

**什麼是傳統對等互連？**

使用 Azure PowerShell 設定的對等互連連線會作為 Azure 資源進行管理。 過去設定的對等互連連線會儲存在我們的系統中做為舊版的對等互連，您可以選擇將其轉換為管理為 Azure 資源。

**呼叫 AzPeeringDirectConnectionObject 時，會將哪些 IP 位址提供給 Microsoft 和對等裝置？**

呼叫 AzPeeringDirectConnectionObject Cmdlet 時，會輸入/31 位址（. b. c. d/31）或/30 位址（. b.. d/30）。 第一個 IP 位址（a. b. d + 0）會提供給對等的裝置，並將第二個 IP 位址（a. b. c. d + 1）提供給 Microsoft 裝置。

**AzPeeringDirectConnectionObject Cmdlet 中的 MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 參數是什麼？**

MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 參數代表對等想要讓 Microsoft 接受的 IPv4 和 IPv6 首碼的最大數目。 這些參數可以隨時修改。