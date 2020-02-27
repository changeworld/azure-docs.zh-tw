---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618475"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每個 Azure Active Directory 租使用者的訂用帳戶 | 無限制。 | 無限制。 |
| 每個訂用帳戶[共同管理員](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |無限制。 |無限制。 |
| 每個訂用帳戶的[資源群組](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure Resource Manager API 要求大小 |4194304個位元組。 |4194304個位元組。 |
| 每個訂用帳戶的標記<sup>1</sup> |無限制。 |無限制。 |
| 每個訂用帳戶的唯一標記計算<sup>1</sup> | 10,000 | 10,000 |
| 每個位置[的訂用帳戶層級部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> | 800 |

<sup>1</sup>您可以對每個訂用帳戶套用不限數目的標記。 每個資源或資源群組的標記數目限制為50。 只有當標記數目為10000或更少時，Resource Manager 才會傳回訂用帳戶中[唯一標記名稱和值的清單](/rest/api/resources/tags)。 當數位超過10000時，您仍然可以透過標記來尋找資源。  

<sup>2</sup>如果您達到800部署的限制，請從已不再需要的歷程記錄中刪除部署。 若要刪除訂用帳戶層級部署，請使用[new-azdeployment](/powershell/module/az.resources/Remove-AzDeployment)或[az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。
