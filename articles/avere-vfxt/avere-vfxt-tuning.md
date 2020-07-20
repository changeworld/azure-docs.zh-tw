---
title: Avere vFXT 叢集調整 - Azure
description: 在 Avere vFXT for Azure 中最佳化效能的自訂設定概觀
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 2d06e3cc80f05cc86980dd2570d1d4d203295ff2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85505285"
---
# <a name="cluster-tuning"></a>叢集調整

大部分的 vFXT 叢集都可以受益於自訂的效能設定。 這些設定可協助叢集妥善搭配您特定的工作流程、資料集和工具使用。

這項自訂作業應該透過支援代表的協助來完成，因為它可能牽涉到設定 Avere 控制台無法使用的功能。

本節說明一些可以完成的自訂調整。

## <a name="general-optimizations"></a>一般最佳化

根據資料集品質或工作流程樣式，可能會建議使用這些變更。

* 如果是大量寫入的工作負載，從其預設的 20% 增加寫入快取的大小。
* 如果資料集涉及許多小檔案，請增加叢集快取的檔案計數限制。
* 如果工作牽涉到複製或移動兩個存放庫之間的資料，調整用於移動資料之執行緒的數目：
  * 若要增加速度，您可能要增加使用之平行執行緒的數目。
  * 如果後端儲存體磁碟區變成超載，您可能需要減少使用之平行執行緒的數目。
* 如果叢集快取使用 NFSv4 ACL 之核心篩選的資料，啟用存取模式快取可簡化特定用戶端的檔案授權。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>雲端 NAS 或雲端閘道最佳化

在雲端 NAS 或閘道案例中，vFXT 叢集會提供對雲端容器的 NAS 樣式存取。 若要利用 vFXT 叢集和雲端儲存體之間的更高資料速度，您的代表可能會建議您變更設定，以更積極地將資料從快取推送至存放磁片區。 例如：

* 增加叢集與儲存體容器之間 TCP 連線的數目

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>雲端負載平衡或混合式 WAN 最佳化

在雲端負載平衡案例或混合式儲存體 WAN 優化案例中，vFXT 叢集會提供雲端與內部部署硬體儲存體之間的整合。 這些變更可能很有説明：

* 增加叢集與核心篩選之間允許之 TCP 連線的數目
* 針對遠端核心篩選啟用 WAN 最佳化設定 (此設定在不同的 Azure 區域中，可用於遠端內部部署篩選或雲端核心篩選)。
* 增加 TCP 通訊端緩衝區大小<sup>*</sup>
* 啟用「永遠轉寄」設定，以減少重複的快取檔案<sup>*</sup>

<sup>*</sup>這些調整可能不適用於所有系統，視工作負載和效能需求而定。

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>協助最佳化 Avere vFXT for Azure

若要與支援人員聯繫這些優化，請使用[取得系統協助](avere-vfxt-open-ticket.md)中所述的程式。
