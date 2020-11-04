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
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233981"
---
| 資源 | 限制 |
| --- | --- |
| 每個 Azure Active Directory 租用戶的訂用帳戶 | 無限制 |
| 每個訂用帳戶的[共同管理員](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |無限制 |
| 每個訂用帳戶的[資源群組](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 要求大小 |4,194,304 個位元組 |
| 每個訂用帳戶的標記<sup>1</sup> |50 |
| 每個訂用帳戶的唯一標記計算<sup>1</sup> | 10,000 |
| 每個位置的[訂用帳戶層級部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |

<sup>1</sup>您可以直接將最多 50 個標記套用至一個訂用帳戶。 不過，該訂用帳戶可包含套用至訂用帳戶內資源群組和資源的標記數目不限。 每個資源或資源群組的標記數目受限於 50。 只有當標記數目為 10,000 或更少時，Resource Manager 才會傳回訂用帳戶中[唯一標記名稱和值的清單](/rest/api/resources/tags)。 當數目超出 10,000 時，您仍然可以依照標記尋找資源。  

<sup>2</sup>如果達到 800 個部署數量限制，請從歷程記錄中刪除不再需要的部署。 若要刪除訂用帳戶層級部署，請使用 [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 或 [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete)。
