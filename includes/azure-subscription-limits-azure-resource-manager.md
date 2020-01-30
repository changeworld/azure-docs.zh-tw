---
title: 包含檔案
description: 包含檔案
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845987"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) VM |每個區域 25000<sup>1</sup> 。 |25000每個區域。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 總計核心 |每個區域 20<sup>1</sup>個。 | 請連絡支援人員。 |
| Azure 點 VM 每個[訂](../articles/billing-buy-sign-up-azure-subscription.md)用帳戶的核心總數 |每個區域 20<sup>1</sup>個。 | 請連絡支援人員。 |
| 每個系列的 VM，例如 Dv2 和 F，每個[訂](../articles/billing-buy-sign-up-azure-subscription.md)用帳戶的核心 |每個區域 20<sup>1</sup>個。 | 請連絡支援人員。 |
| 每一訂用帳戶每一區域的[儲存體帳戶](../articles/storage/common/storage-account-create.md) |250 |250 |
| 每個訂用帳戶的[可用性設定組](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |2000每個區域。 |2000每個區域。 |
| 每一訂用帳戶[同質群組](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| 每一訂用帳戶[雲端服務](../articles/cloud-services/cloud-services-choose-me.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| 每個訂用帳戶的[資源群組](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure Resource Manager API 要求大小 |4194304個位元組。 |4194304個位元組。 |
| 每個訂用帳戶的標記<sup>2</sup> |無限制。 |無限制。 |
| 每個訂用帳戶的唯一標記計算<sup>2</sup> | 10,000 | 10,000 |
| 每個位置[的訂用帳戶層級部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>4</sup> | 800 |
| 每個 Azure Active Directory 租使用者的訂用帳戶 | 無限制。 | 無限制。 |
| 每個訂用帳戶[共同管理員](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |無限制。 |無限制。 |

<sup>1</sup>預設限制會因供應專案類別類型而異，例如免費試用版和隨用隨付，以及依系列（例如 Dv2、F 和 G）。例如，Enterprise 合約訂閱的預設值是350。

<sup>2</sup>您可以對每個訂用帳戶套用不限數目的標記。 每個資源或資源群組的標記數目限制為50。 只有當標記數目為10000或更少時，Resource Manager 才會傳回訂用帳戶中[唯一標記名稱和值的清單](/rest/api/resources/tags)。 當數位超過10000時，您仍然可以透過標記來尋找資源。  

<sup>3</sup>Azure 資源群組和 Resource Manager 不再需要這些功能。

<sup>4</sup>如果您達到800部署的限制，請從已不再需要的歷程記錄中刪除部署。 若要刪除訂用帳戶層級部署，請使用[new-azdeployment](/powershell/module/az.resources/Remove-AzDeployment)或[az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。

> [!NOTE]
> 虛擬機器核心具有區域總計限制。 它們也會限制每一大小的區域，例如 Dv2 和 F。系統會分別強制執行這些限制。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可以部署30個 A1 Vm 或30個 D1 Vm，或兩者的組合，而不超過總計30個核心。 結合的範例為10個 A1 Vm 和20個 D1 Vm。  
> <!-- -->
> 
> 

