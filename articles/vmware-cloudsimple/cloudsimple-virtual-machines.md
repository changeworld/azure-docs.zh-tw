---
title: Azure VMware 解決方案（AVS）-虛擬機器總覽
description: 瞭解 AVS 虛擬機器及其優點。
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024903"
---
# <a name="avs-virtual-machines-overview"></a>AVS 虛擬機器總覽

AVS 可讓您從 Azure 入口網站管理 VMware 虛擬機器（Vm）。 來自 vSphere 叢集的叢集或資源集區會透過 Azure 對應到您的訂用帳戶來進行管理。

若要從 Azure 建立 AVS VM，您的 AVS 私用雲端 vCenter 上必須有 VM 範本。 此範本是用來自訂作業系統和應用程式。 您可以強化範本 VM 以符合企業安全性原則。 您可以使用範本來建立 Vm，然後使用自助模型從 Azure 入口網站取用它們。

## <a name="benefits"></a>優勢

Azure 入口網站的 AVS 虛擬機器提供自助機制，讓使用者建立和管理 VMware 虛擬機器。

* 在您的 AVS 私用雲端 vCenter 上建立 AVS VM
* 管理 VM 屬性
  * 新增/移除磁片
  * 新增/移除 Nic
* 您的 AVS VM 的電源操作
  * 開啟電源和關閉電源
  * 重設 VM
* 刪除 VM

## <a name="next-steps"></a>後續步驟

* 瞭解如何[使用 Azure 上的 VMware vm](quickstart-create-vmware-virtual-machine.md)
* 瞭解如何[對應您的 Azure 訂](azure-subscription-mapping.md)用帳戶
