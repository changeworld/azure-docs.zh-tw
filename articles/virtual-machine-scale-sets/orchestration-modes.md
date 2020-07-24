---
title: 深入瞭解 Azure 中虛擬機器擴展集的協調流程模式
description: 深入瞭解 Azure 中虛擬機器擴展集的協調流程模式。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: eb7d4d8a6f1c1ee55601cdd839e330147e60bcc7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011068"
---
# <a name="orchestration-modes-preview"></a>協調流程模式（預覽）

> [!CAUTION]
> 感謝您參與此公開預覽版的每個人。 我們可以從我們的社區收集寶貴的意見反應。 這項預覽版現已針對任何新參與者**關閉**，以便整合意見反應。 我們會以任何新資訊更新此空間。

虛擬機器擴展集提供平臺管理之虛擬機器的邏輯群組。 使用擴展集時，您可以建立虛擬機器設定模型、根據 CPU 或記憶體負載自動新增或移除其他實例，以及自動升級為最新的作業系統版本。 傳統上，擴展集可讓您使用在建立擴展集時所提供的 VM 設定模型來建立虛擬機器，而擴展集只能管理根據設定模型隱含建立的虛擬機器。

使用擴展集協調流程模式（預覽），您現在可以選擇擴展集是否應協調在擴展集設定模型外明確建立的虛擬機器，或是根據設定模型隱含建立的虛擬機器實例。 擴展集協調流程模式也可協助您藉由在容錯網域和可用性區域中部署這些 Vm，來設計高可用性的 VM 基礎結構。


虛擬機器擴展集會支援2個不同的協調流程模式：

- ScaleSetVM –新增至擴展集的虛擬機器實例是以擴展集設定模型為基礎。 虛擬機器實例生命週期-建立、更新、刪除-由擴展集管理。
- VM （虛擬機器）–在擴展集外部建立的虛擬機器可以明確新增至擴展集。 
 

> [!IMPORTANT]
> 當您建立擴展集時，會定義協調流程模式，而且稍後無法變更或更新。 
> 
> 虛擬機器擴展集的這項功能目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="orchestration-modes"></a>協調流程模式

| 功能                     | "orchestrationMode"： "VM" （VirtualMachine） | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| VM 設定模型      | 無                                       | 必要 |
| 正在將新的 VM 新增至擴展集  | 建立 VM 時，會將 Vm 明確新增至擴展集。 | Vm 會根據 VM 設定模型、實例計數和自動調整規則，以隱含方式建立並新增至擴展集 | |
| 刪除 VM                   | Vm 必須個別刪除，如果擴展集內有任何 Vm，則不會刪除它。 | 您可以個別刪除 Vm，而刪除擴展集將會刪除所有的 VM 實例。  |
| 附加/卸離 Vm           | 不受支援                              | 不支援 |
| 實例生命週期（透過刪除建立） | Vm 及其構件（例如磁片和 Nic）可以獨立管理。 | 實例及其成品（例如磁片和 Nic）對建立它們的擴展集實例是隱含的。 它們無法在擴展集之外分開卸離或管理 |
| 容錯網域               | 可以定義容錯網域。 2或3以區域支援為基礎，而5用於可用性區域。 | 可以定義從1到5的容錯網域 |
| 更新網域              | 更新網域會自動對應至容錯網域 | 更新網域會自動對應至容錯網域 |
| 可用性區域          | 支援區域部署或單一可用性區域中的 Vm | 支援區域部署或多個可用性區域;可以定義區域平衡策略 |
| 自動調整                   | 不支援                              | 支援 |
| 作業系統升級                  | 不支援                              | 支援 |
| 模型更新               | 不支援                              | 支援 |
| 實例控制項            | 完整的 VM 控制。 Vm 具有完整的 URI，可支援完整範圍的 Azure VM 管理功能（例如 Azure 原則、Azure 備份和 Azure Site Recovery） | Vm 是擴展集的相依資源。 只能透過擴展集來存取實例。 |
| 實例模型              | Microsoft. Compute/VirtualMachines 模型定義。 | VirtualMachineScaleSets/VirtualMachines 模型定義。 |
| Capacity                    | 可以建立空的擴展集;最多可將200個 Vm 新增至擴展集 | 您可以使用實例計數 0-1000 來定義擴展集 |
| 移動                        | 支援                                  | 支援 |
| 單一放置群組 = = false | 不支援                          | 支援 |


## <a name="next-steps"></a>接下來的步驟

如需詳細資訊，請參閱[可用性選項的總覽](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machine-scale-sets%2ftoc.json)。
