---
title: Azure CDN DDoS 保護功能 |微軟文檔
description: 來自 Microsoft 的 Azure CDN 受 DDoS 保護保護，基本不增加成本
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: magattus
ms.openlocfilehash: 9cd688de861015cc12d1f98ed71e5376e5f574db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593892"
---
# <a name="azure-cdn-ddos-protection"></a>Azure CDN DDoS 保護

內容交付網路按設計提供 DDoS 保護。 除了全域容量來吸收批量攻擊外，Azure CDN 還具有如下所述的額外 DDoS 保護，無需額外費用。

## <a name="azure-cdn-from-microsoft"></a>來自 Microsoft 的 Azure CDN

來自微軟的 Azure CDN 受[Azure 基本 DDoS 的保護](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。 預設情況下，它將從 Microsoft 平臺集成到 Azure CDN 中，無需額外費用。 Microsoft 全球部署網路的 Azure CDN 全面規模和容量通過始終打開的流量監控和即時緩解功能，可抵禦常見的網路層攻擊。 基本 DDoS 保護還針對針對 CDN 終結點的最常見、經常發生的第 7 層 DNS 查詢洪流和第 3 層和第 4 層卷攻擊進行防禦。 此服務在保護 Microsoft 的企業和消費者服務免受大規模攻擊方面也有著良好的記錄。

## <a name="azure-cdn-from-verizon"></a>Azure CDN from Verizon

來自 Verizon 的 Azure CDN 受 Verzion 專有 DDoS 緩解平臺的保護。 預設情況下，它是從 Verizon 集成到 Azure CDN 中，無需額外費用。 它針對針對 CDN 終結點的最常見、經常發生的第 7 層 DNS 查詢洪水和第 3 層和第 4 層卷攻擊提供基本保護。

## <a name="azure-cdn-from-akamai"></a>Azure CDN from Akamai

Akamai 的 Azure CDN 受 Akamai 專有 DDoS 緩解平臺的保護。 預設情況下，它由 Akamai 集成到 Azure CDN 中，無需額外費用。 它針對針對 CDN 終結點的最常見、經常發生的第 7 層 DNS 查詢洪水和第 3 層和第 4 層卷攻擊提供基本保護。

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)的更多。 
