---
title: 虛擬機器概述
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解雲簡單虛擬機器及其優勢。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024903"
---
# <a name="cloudsimple-virtual-machines-overview"></a>雲簡單虛擬機器概述

CloudSimple 允許您從 Azure 門戶管理 VMware 虛擬機器 （VM）。  vSphere 群集的群集或資源池通過 Azure 通過將其映射到訂閱進行管理。

要從 Azure 創建雲簡單 VM，私有雲 vCenter 上必須存在 VM 範本。  該範本用於自訂作業系統和應用程式。  可以強化範本 VM 以滿足企業安全性原則。  可以使用範本創建 VM，然後使用自助服務模型從 Azure 門戶使用它們。

## <a name="benefits"></a>優點

Azure 門戶的 CloudSimple 虛擬機器為使用者提供了創建和管理 VMware 虛擬機器的自助服務機制。

* 在私有雲 vCenter 上創建雲簡單 VM
* 管理 VM 屬性
  * 添加/刪除磁片
  * 添加/刪除 NIC
* 雲簡單 VM 的電源操作
  * 開機並關機
  * 重設 VM
* 刪除 VM

## <a name="next-steps"></a>後續步驟

* 瞭解如何在[Azure 上使用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 瞭解如何映射[Azure 訂閱](azure-subscription-mapping.md)
