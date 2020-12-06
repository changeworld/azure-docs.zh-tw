---
title: Azure Resource Manager vCPU 配額增加要求
description: Azure Resource Manager vCPU 配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745242"
---
# <a name="quota-increase-requests"></a>增加配額要求

在每個區域中，針對每個訂用帳戶，在兩個層級強制執行虛擬機器和虛擬機器擴展集的 Resource Manager vCPU 配額。

第一層是所有 VM 系列間的總區域個 vcpu 限制。 第二層是每個 VM 系列個 vcpu 限制，例如 D 系列個 vcpu。 每當部署新的虛擬機器時，該 VM 系列的新的和現有個 vcpu 使用量總和不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新的和現有的 vCPU 計數，不應超過針對訂用帳戶核准的總區域個 vcpu 配額。 如果超過其中一項配額，將不允許 VM 部署。
您可以從 Azure 入口網站要求增加 VM 系列的個 vcpu 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域個 vcpu 限制。

建立新的訂用帳戶時，預設的區域總個 vcpu 可能不會等於所有個別 VM 系列之預設 vCPU 配額的總和。 這項事實可能會導致訂用帳戶具有足夠的配額來容納您想要部署的每個個別 VM 系列。 它可能會有足夠的配額可供所有部署的區域總個 vcpu。 在此情況下，您必須提交要求以明確地增加區域個 vcpu 限制。 區域個 vcpu 限制總計不得超過區域所有 VM 系列的已核准配額總和。

> [!NOTE]
> 如果您想要將限制或配額提高到超過預設限制，請免費 [開啟線上客戶支援要求](../../azure-resource-manager/templates/error-resource-quota.md#solution)。

如需有關配額的詳細資訊，請參閱 [虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md) 和 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。