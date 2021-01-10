---
title: Azure 網路來回延遲統計資料 |Microsoft Docs
description: 瞭解 Azure 區域之間的來回延遲統計資料。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: bc2d7bb7ba17a4a47fecf2144157f79f5367fca7
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059159"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路來回延遲統計資料

Azure 會使用內部監視工具以及 [ThousandEyes](https://thousandeyes.com)（協力廠商綜合監視服務）所收集的度量，持續監控其網路核心區域的延遲 (速度) 。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延遲測量是從全球 Azure 雲端區域中裝載的 ThousandEyes 代理程式收集而來，其會在1分鐘的間隔內持續傳送自己的網路探查。 每月的延遲統計資料是從每個月收集樣本的平均值所衍生。

## <a name="december-2020-round-trip-latency-figures"></a>2020年12月的來回延遲圖表

過去30天的 Azure 區域之間的每月平均來回行程 (于) 2020 年12月31日結束，如下所示。 下列測量是由 [ThousandEyes](https://thousandeyes.com)提供技術支援。

[![Azure 區域間延遲統計資料](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>下一步

深入瞭解 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
