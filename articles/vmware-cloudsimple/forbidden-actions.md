---
title: 在更高的存取期間禁止的動作
description: VMware 引擎會還原變更，以確保當 VMware 引擎偵測到下列任何禁止的動作時，服務會保持不中斷。
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915406"
---
# <a name="forbidden-actions-during-elevated-access"></a>在更高的存取期間禁止的動作

在提高許可權時間間隔期間，會禁止某些動作。 當 VMware 引擎偵測到下列任何禁止的動作時，VMware 引擎會還原變更，以確保服務保持不中斷。

## <a name="cluster-actions"></a>叢集動作

- 從 vCenter 移除叢集。
- 變更叢集上 (HA) 的 vSphere 高可用性。
- 從 vCenter 將主機新增至叢集中。
- 從 vCenter 移除叢集中的主機。

## <a name="host-actions"></a>主機動作

- 移除 ESXi 主機上的資料存放區。
- 正在從主機卸載 vCenter 代理程式。
- 修改主機設定。
- 對主機設定檔進行任何變更。
- 將主機置於維護模式。

## <a name="network-actions"></a>網路動作

- 在私人雲端中刪除預設的分散式虛擬交換器 (DVS) 。
- 從預設 DVS 中移除主機。
- 正在匯入任何 DVS 設定。
- 正在重新設定任何 DVS 設定。
- 正在升級任何 DVS。
- 正在刪除管理通訊埠群組。
- 編輯管理通訊埠群組。

## <a name="roles-and-permissions-actions"></a>角色和許可權動作

- 建立全域角色。
- 修改或刪除任何管理物件的許可權。
- 修改或移除任何預設角色。
- 將角色的許可權提高到雲端擁有者角色以上。

## <a name="other-actions"></a>其他動作

- 移除任何預設授權：
  - vCenter Server
  - ESXi 節點
  - NSX-T
  - HCX
- 修改或刪除管理資源集區。
- 複製管理 Vm。


## <a name="next-steps"></a>接下來的步驟
[CloudSimple 維護和更新](cloudsimple-maintenance-updates.md) 
