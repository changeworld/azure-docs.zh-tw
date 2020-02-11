---
title: Azure 網路來回行程延遲統計資料 |Microsoft Docs
description: 瞭解 Azure 區域之間的來回行程延遲統計資料。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 02/10/2020
ms.author: kumud
ms.openlocfilehash: dc2cb6545093e8ab70d18014a5fd9d0e7d36f8b7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122377"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路來回行程延遲統計資料

Azure 會使用內部監視工具，以及[ThousandEyes](https://thousandeyes.com)（協力廠商綜合監視服務）所收集的度量，持續監控其網路核心區域的延遲（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延遲測量是從全球 Azure 雲端區域中裝載的 ThousandEyes 代理程式收集而來，在1分鐘的間隔內持續傳送網路探查。 每月延遲統計資料是從每月收集的樣本平均值衍生而來。

## <a name="january-2020-round-trip-latency-figures"></a>2020年1月的來回行程延遲圖形

以下顯示過去31天內 Azure 區域之間的每月平均來回行程時間（從2020年1月31日結束）。 下列測量值是由[ThousandEyes](https://thousandeyes.com)提供技術支援。

[![Azure 區域間延遲統計資料](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
