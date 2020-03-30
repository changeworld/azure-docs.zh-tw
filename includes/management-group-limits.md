---
title: 包含檔案
description: 包含檔案
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335115"
---
| 資源 | 限制 |
| --- | --- |
| 每個目錄的管理組 | 10,000 |
| 每個管理組的訂閱 | 無限制。 |
| 管理組層次結構級別 | 根級別加上 6 級<sup>1</sup> |
| 每個管理組的直接父管理組 | 一個 |
| 每個位置[的管理組級別部署](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 個級別不包括訂閱級別。

<sup>2</sup>如果達到 800 個部署的限制，請從不再需要的歷史記錄中刪除部署。 要刪除管理組級別部署，請使用[刪除 Az 管理組部署](/powershell/module/az.resources/Remove-AzManagementGroupDeployment)或[az 部署 mg 刪除](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。
