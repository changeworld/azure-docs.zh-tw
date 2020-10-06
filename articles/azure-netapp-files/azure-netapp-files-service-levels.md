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
ms.date: 10/05/2020
ms.author: b-juche
ms.openlocfilehash: b5c576211fe7bb202e7a27bee5ee3bfd90d74cf9
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743092"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files 的服務等級
服務層級是容量集區的屬性。 服務層級會根據指派給磁片區的配額，定義並依容量集區中的磁片區允許的最大輸送量來區分。

## <a name="supported-service-levels"></a>支援的服務層級

Azure NetApp Files 支援三種服務層級： *Ultra*、 *Premium*和 *Standard*。 

* <a name="Ultra"></a>Ultra 儲存體

    Ultra 儲存層最多可為每 1 TiB 布建的容量提供最高 128 MiB/秒的輸送量。 

* <a name="Premium"></a>Premium 儲存體

    Premium 儲存層可為每 1 TiB 布建的容量提供最高 64 MiB/秒的輸送量。 

* <a name="Standard"></a>標準儲存體

    標準儲存層最多可為每 1 TiB 布建的容量提供 16 MiB/秒的輸送量。

## <a name="throughput-limits"></a>輸送量限制

磁片區的輸送量限制取決於下列因素的組合：
* 磁片區所屬容量集區的服務層級
* 指派給磁片區的配額  
* QoS 類型 (容量集區的 *自動* 或 *手動*)   

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>自動 QoS 容量集區中磁片區的輸送量限制範例

下圖顯示自動 QoS 容量集區中磁片區的輸送量限制範例：

![服務等級圖例](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 在上述範例1中，來自自動 QoS 容量集區的磁片區，具有指派2個配額 TiB 的 Premium 儲存層，將會獲指派 128 MiB/秒 (2 TiB * 64 MiB/s) 的輸送量限制。 無論容量集區大小或實際的磁片區耗用量為何，都適用此案例。

* 在上述範例2中，自動 QoS 容量集區中的磁片區具有指派 100 GiB 配額的 Premium 儲存層，將獲得 6.25 MiB/s 的輸送量限制 (0.09765625 TiB * 64 MiB/s) 。 無論容量集區大小或實際的磁片區耗用量為何，都適用此案例。

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>手動 QoS 容量集區中磁片區的輸送量限制範例 

如果您使用手動 QoS 容量集區，則可以個別指派磁片區的容量和輸送量。 當您在手動 QoS 容量集區中建立磁片區時，您可以 (MiB/S) 值指定輸送量。 在手動 QoS 容量集區中指派給磁片區的輸送量總計，取決於集區的大小和服務層級。 在 TiB x 服務層級的輸送量/TiB) 中， (容量集區大小的上限。 例如，具有 Ultra 服務層級的 10 TiB 容量集區具有 1280 MiB/s 的總輸送量容量 (10 TiB x 128 MiB/s/TiB) 可供磁片區使用。

若為 SAP Hana 系統，可以使用此容量集區來建立下列磁片區。 每個磁片區都會提供個別大小和輸送量，以符合您的應用程式需求：

* SAP Hana 資料磁片區：大小為 4 TB，最高 704 MiB/秒
* SAP Hana 記錄磁片區：大小為 0.5 TB，最多 256 MiB/秒
* SAP Hana 共用磁片區：大小為 1 TB，最高 64 MiB/秒
* SAP Hana 備份磁片區：大小為 6.5 TB，最多 256 MiB/秒

下圖說明 SAP Hana 磁片區的案例：

![QoS SAP Hana 磁片區案例](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [適用於Azure NetApp Files 的成本模型](azure-netapp-files-cost-model.md) 
- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
- [適用于 Azure NetApp Files 的服務等級協定 (SLA) ](https://azure.microsoft.com/support/legal/sla/netapp/)
- [動態變更磁碟區的服務等級](dynamic-change-volume-service-level.md) 
- [跨區域複寫的服務層級目標](cross-region-replication-introduction.md#service-level-objectives)
