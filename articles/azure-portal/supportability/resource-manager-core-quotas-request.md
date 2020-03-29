---
title: Azure Resource Manager vCPU 配額增加要求
description: Azure Resource Manager vCPU 配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843662"
---
# <a name="quota-increase-requests"></a>增加配額要求

虛擬機器和虛擬機器規模集的資源管理器 vCPU 配額在每個區域的每個訂閱的兩層強制實施。

第一層是所有 VM 系列的總區域 vCPU 限制。 第二層是每個 VM 系列 vCPU 限制，如 D 系列 vCPU。 每當部署新的虛擬機器時，該 VM 系列的新 vCPU 和現有 vCPU 使用量的總和不得超過為該特定 VM 系列批准的 vCPU 配額。 此外，在所有 VM 系列中部署的新 vCPU 和現有 vCPU 計數總數不應超過為訂閱批准的區域 vCPU 總配額。 如果超過其中任一配額，則不允許 VM 部署。
可以從 Azure 門戶請求提高 VM 系列的 vCPU 配額限制。 VM 系列配額的增加會自動將區域總 vCPU 限制增加相同數量。

創建新訂閱時，預設的"區域總計"vCPU 可能不等於所有單個 VM 系列的預設 vCPU 配額的總和。 這一事實可能導致訂閱具有足夠的配額，每個單獨的 VM 系列，您要部署。 對於所有部署，可能沒有足夠的區域可用 CPU 配額。 在這種情況下，您需要提交請求以顯式增加"區域總 vCPU"限制。 區域 vCPU 總限制不能超過該區域所有 VM 系列中已批准配額的總和。

有關配額的詳細資訊，請參閱虛擬機器[vCPU 配額](../../virtual-machines/windows/quotas.md)和[Azure 訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

