---
title: Azure 網路來回延遲統計資料 |Microsoft Docs
description: 瞭解 Azure 區域之間的來回行程延遲統計資料。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 07/07/2020
ms.author: kumud
ms.openlocfilehash: f0266205ea5ce618793022523ce167cbc6a615c0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114275"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路來回延遲統計資料

Azure 會使用內部監視工具，以及[ThousandEyes](https://thousandeyes.com)（協力廠商綜合監視服務）所收集的度量，持續監控其網路核心區域的延遲（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延遲測量是從全球 Azure 雲端區域中裝載的 ThousandEyes 代理程式收集而來，在1分鐘的間隔內持續傳送網路探查。 每月延遲統計資料是從每月收集的樣本平均值衍生而來。

## <a name="june-2020-round-trip-latency-figures"></a>2020年6月的來回延遲圖表

以下顯示過去30天內 Azure 區域之間的每月平均來回行程時間（從2020年6月30日結束）。 下列測量值是由[ThousandEyes](https://thousandeyes.com)提供技術支援。

[![Azure 區域間延遲統計資料](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
