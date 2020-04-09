---
title: Azure 網络往返延遲統計資訊 |微軟文件
description: 瞭解 Azure 區域之間的往返延遲統計資訊。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886939"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路往返延遲統計資訊

Azure 使用內部監控工具以及由第三方綜合監控服務[千美希望](https://thousandeyes.com)收集的測量值持續監控其網路核心區域的延遲(速度)。

## <a name="how-are-the-measurements-collected"></a>如何收集測量值?

延遲測量是從千眼代理收集的,這些代理託管在全球 Azure 雲區域中,這些代理每隔 1 分鐘一次在它們之間連續發送網路探測。 每月延遲統計資訊派生自該月收集的樣本的平均值。

## <a name="march-2020-round-trip-latency-figures"></a>2020年3月往返延遲數據

Azure 區域過去 31 天(2020 年 3 月 31 日結束)之間的每月平均往返時間如下所示。 以下測量由[千眼](https://thousandeyes.com)驅動。

[![Azure 區域間延遲統計資訊](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
