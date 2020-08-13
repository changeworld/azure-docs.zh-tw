---
title: 虛擬機器總覽
titleSuffix: Azure VMware Solution by CloudSimple
description: 深入瞭解 CloudSimple 虛擬機器及其優點。 您可以從 Azure 入口網站管理 VMware 虛擬機器。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141971"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 虛擬機器總覽

CloudSimple 可讓您管理從 Azure 入口網站)  (Vm 的 VMware 虛擬機器。  來自 vSphere 叢集的叢集或資源集區會透過 Azure 對應到您的訂用帳戶來進行管理。

若要從 Azure 建立 CloudSimple VM，VM 範本必須存在於您的私用雲端 vCenter 上。  此範本是用來自訂作業系統和應用程式。  您可以強化範本 VM 以符合企業安全性原則。  您可以使用範本來建立 Vm，然後使用自助模型從 Azure 入口網站取用它們。

## <a name="benefits"></a>優點

從 Azure 入口網站 CloudSimple 虛擬機器提供自助機制，讓使用者建立和管理 VMware 虛擬機器。

* 在您的私用雲端 vCenter 上建立 CloudSimple VM
* 管理 VM 屬性
  * 新增/移除磁片
  * 新增/移除 Nic
* CloudSimple VM 的電源作業
  * 開啟電源和關閉電源
  * 重設 VM
* 刪除 VM

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [使用 Azure 上的 VMware vm](quickstart-create-vmware-virtual-machine.md)
* 瞭解如何[對應您的 Azure 訂](azure-subscription-mapping.md)用帳戶
