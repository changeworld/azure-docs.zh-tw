---
title: Include 檔案
description: 包含檔案
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560435"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每一[訂用帳戶](https://azure.microsoft.com/pricing/)的 vCPU<sup>1</sup> |20 |10,000 |
| 每個訂用帳戶的[共同管理員](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |200 |200 |
| 每一訂用帳戶的[儲存體帳戶](../articles/storage/common/storage-account-create.md)<sup>2</sup> |100 |100 |
| 每一訂用帳戶[雲端服務](../articles/cloud-services/cloud-services-choose-me.md) |20 |200 |
| [區域網路](/previous-versions/azure/reference/jj157100(v=azure.100)) |10 |500 |
| 每一訂閱的 DNS 伺服器 |9 |100 |
| 每一訂用帳戶的保留 IP |20 |100 |
| 每一訂用帳戶[同質群組](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) |256 |256 |
| 訂用帳戶名稱長度 (字元) | 64 | 64 |

<sup>1</sup>雖然只使用部分 CPU 核心，在計算 vCPU 限制時，仍會將超小型執行個體視為一個 vCPU。

<sup>2</sup>儲存體帳戶限制包括標準和進階儲存體帳戶。