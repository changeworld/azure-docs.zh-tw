---
title: Avere vFXT 叢集調整 - Azure
description: 在 Avere vFXT for Azure 中最佳化效能的自訂設定概觀
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152930"
---
# <a name="cluster-tuning"></a>叢集調整

大部分的 vFXT 叢集都可以受益於自訂的效能設定。 這些設定可協助叢集妥善搭配您特定的工作流程、資料集和工具使用。

此自訂應在支援代表的説明下完成，因為它可能涉及配置 Avere 控制台中不可用的功能。

本節介紹可以完成的一些自訂調優。

## <a name="general-optimizations"></a>一般最佳化

根據資料集品質或工作流程樣式，可能會建議使用這些變更。

* 如果是大量寫入的工作負載，從其預設的 20% 增加寫入快取的大小。
* 如果資料集涉及許多小檔案，請增加叢集快取的檔案計數限制。
* 如果工作牽涉到複製或移動兩個存放庫之間的資料，調整用於移動資料之執行緒的數目：
  * 若要增加速度，您可能要增加使用之平行執行緒的數目。
  * 如果後端儲存體磁碟區變成超載，您可能需要減少使用之平行執行緒的數目。
* 如果叢集快取使用 NFSv4 ACL 之核心篩選的資料，啟用存取模式快取可簡化特定用戶端的檔案授權。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>雲端 NAS 或雲端閘道最佳化

在雲 NAS 或閘道方案中，vFXT 群集提供對雲容器的 NAS 樣式訪問。 為了利用 vFXT 群集和雲存儲之間的更高資料速度，您的代表可能會建議更改設置，以便更積極地將資料從緩存推送到存儲卷。 例如：

* 增加叢集與儲存體容器之間 TCP 連線的數目

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>雲端負載平衡或混合式 WAN 最佳化

在雲爆發方案或混合存儲 WAN 優化方案中，vFXT 群集提供雲和本地硬體存儲之間的集成。 這些更改可能很有説明：

* 增加叢集與核心篩選之間允許之 TCP 連線的數目
* 針對遠端核心篩選啟用 WAN 最佳化設定 (此設定在不同的 Azure 區域中，可用於遠端內部部署篩選或雲端核心篩選)。
* 增加 TCP 通訊端緩衝區大小<sup>*</sup>
* 啟用"始終向前"設置以減少冗余緩存的檔<sup>*</sup>

<sup>*</sup>這些調整可能並不適用于所有系統，具體取決於工作負載和性能需求。

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>協助最佳化 Avere vFXT for Azure

要聯繫支援人員瞭解這些優化，請使用["獲取系統説明"](avere-vfxt-open-ticket.md)中所述的過程。
