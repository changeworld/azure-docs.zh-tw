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
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986668"
---
這些範例示範如何針對已為 Azure 委派的資源管理上線的訂用帳戶，使用 Azure 原則。

| **範本** | **說明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 指派原則，以將標籤新增到委派的訂用帳戶，或從委派的訂用帳戶移除標籤 (使用修改效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 指派將針對委派指派進行審核的原則。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | 指派原則，以在委派的訂用帳戶中診斷 Azure Key Vault 資源 (使用 deployIfNotExists 效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 指派數個原則，以便診斷委派的訂用帳戶，並將所有 Windows 和 Linux VN 連線到原則所建立的 Log Analytics 工作區。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 將計畫 (多個相關的原則定義) 套用到委派的訂用帳戶。 |

