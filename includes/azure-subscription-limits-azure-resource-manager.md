---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334982"
---
| 資源 | 限制 |
| --- | --- |
| 每個 Azure 活動目錄租戶的訂閱 | 無限制。 |
| 每個訂閱[的協管理員](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |無限制。 |
| 每個訂閱[的資源組](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure 資源管理器 API 請求大小 |4，194，304 位元組。 |
| 每個訂閱的標記<sup>1</sup> |50 |
| 每個訂閱的唯一標記計算<sup>1</sup> | 10,000 |
| 每個位置[的訂閱級部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup>您只能將多達 50 個標記直接應用於訂閱。 但是，訂閱可以包含無限數量的標記，這些標記應用於訂閱中的資源組和資源。 每個資源或資源組的標記數限制為 50。 僅當標記數為 10，000 或更少時，資源管理器才會返回訂閱中[唯一標記名稱和值的清單](/rest/api/resources/tags)。 當數位超過 10，000 時，您仍可以通過標記查找資源。  

<sup>2</sup>如果達到 800 個部署的限制，請從不再需要的歷史記錄中刪除部署。 要刪除訂閱級別部署，請使用[刪除"部署"](/powershell/module/az.resources/Remove-AzDeployment)或["az 部署"子刪除](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)。
