---
title: Azure NetApp Files 的服務層級 | Microsoft Docs
description: 描述 Azure NetApp Files 之服務層級的輸送量效能。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533116"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files 的服務等級
服務層級是容量集區的屬性。 服務層級是根據指派給磁片區的配額，以容量集區中的磁片區所允許的最大輸送量來定義和區分。

## <a name="supported-service-levels"></a>支援的服務等級

Azure NetApp Files 支援三種服務層級： *Ultra*、 *Premium*和*Standard*。 

* <a name="Ultra"></a>Ultra 儲存體

    Ultra 儲存層可為每1個指派的磁片區配額 TiB 提供最多 128 MiB/秒的輸送量。 

* <a name="Premium"></a>Premium 儲存體

    Premium 儲存層提供每1個指派的磁片區配額 TiB，最高可達 64 MiB/秒的輸送量。 

* <a name="Standard"></a>標準儲存體

    標準儲存層提供最多 16 MiB/秒的輸送量，每 1 TiB 指派的磁片區配額。

## <a name="throughput-limits"></a>輸送量限制

磁片區的輸送量限制取決於下列因素的組合：
* 磁片區所屬之容量集區的服務層級
* 指派給磁片區的配額  

下圖說明此概念：

![服務等級圖例](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

在上述的範例1中，容量集區中的磁片區與指派了2個配額 TiB 的 Premium 儲存層會被指派輸送量限制為 128 MiB/秒（2 TiB * 64 MiB/秒）。 無論容量集區大小或實際磁片區耗用量為何，都適用此案例。

在上述的範例2中，容量集區中的磁片區（具有指派 100 GiB 配額的 Premium 儲存層）將獲指派 6.25 MiB/秒（0.09765625 TiB * 64 MiB/s）的輸送量限制。 無論容量集區大小或實際磁片區耗用量為何，都適用此案例。

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [適用於Azure NetApp Files 的成本模型](azure-netapp-files-cost-model.md) 
- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
- [適用于 Azure NetApp Files 的服務等級協定（SLA）](https://azure.microsoft.com/support/legal/sla/netapp/)
- [動態變更磁碟區的服務等級](dynamic-change-volume-service-level.md) 