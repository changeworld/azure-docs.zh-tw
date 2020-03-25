---
title: 包含檔案
description: 包含檔案
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456729"
---
這些範例示範如何針對已為 Azure 委派的資源管理上線的訂用帳戶，使用 Azure 原則。

| **範本** | **說明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | 指派原則，以將標籤新增到委派的訂用帳戶，或從委派的訂用帳戶移除標籤 (使用修改效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | 指派將針對委派指派進行審核的原則。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | 指派原則，以在委派的訂用帳戶中診斷 Azure Key Vault 資源 (使用 deployIfNotExists 效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | 指派數個原則，以便診斷委派的訂用帳戶，並將所有 Windows 和 Linux VN 連線到原則所建立的 Log Analytics 工作區。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | 將計畫 (多個相關的原則定義) 套用到委派的訂用帳戶。 |

