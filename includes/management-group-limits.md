---
title: 包含檔案
description: 包含檔案
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748754"
---
| 資源 | 限制 |
| --- | --- |
| 每個 Azure AD 租用戶的管理群組 | 10,000 |
| 每個管理群組的訂用帳戶 | 無限制。 |
| 管理群組階層的層級 | 根層級加 6 個層級<sup>1</sup> |
| 每個管理群組的直接父管理群組 | 一個 |
| 每個位置的[管理群組層級部署](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 個層級不包含訂用帳戶層級。

<sup>2</sup>如果達到 800 個部署數量限制，請從歷程記錄中刪除不再需要的部署。 若要刪除管理群組層級部署，請使用 [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 或 [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)。
