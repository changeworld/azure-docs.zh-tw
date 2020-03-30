---
title: Azure 網路往返延遲統計資訊 |微軟文檔
description: 瞭解 Azure 區域之間的往返延遲統計資訊。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082928"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路往返延遲統計資訊

Azure 使用內部監控工具以及由協力廠商綜合監控服務[千美希望](https://thousandeyes.com)收集的測量值持續監控其網路核心區域的延遲（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集測量值？

延遲測量是從千眼代理收集的，這些代理託管在全球 Azure 雲區域中，這些代理每隔 1 分鐘一次在它們之間連續發送網路探測。 每月延遲統計資訊派生自該月收集的樣本的平均值。

## <a name="february-2020-round-trip-latency-figures"></a>2020 年 2 月往返延遲資料

Azure 區域過去 29 天（2020 年 2 月 29 日結束）之間的每月平均往返時間如下所示。 以下測量由[千眼](https://thousandeyes.com)驅動。

[![Azure 區域間延遲統計資訊](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
