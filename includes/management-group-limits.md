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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335115"
---
| 資源 | 限制 |
| --- | --- |
| 每個目錄的管理群組 | 10,000 |
| 每個管理群組的訂用帳戶 | 無限制。 |
| 管理群組階層的層級 | 根層級加上6層級<sup>1</sup> |
| 每個管理群組的直接父管理群組 | 一個 |
| 每個位置的[管理群組層級部署](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6層級不包含訂用帳戶層級。

<sup>2</sup>如果您達到800部署的限制，請從已不再需要的歷程記錄中刪除部署。 若要刪除管理群組層級部署，請使用[AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment)或[az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。
