---
title: Azure 春季雲端的服務方案和配額
description: 瞭解 Azure 春季雲端的服務配額和服務方案
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089459"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春季雲端的配額和服務方案

所有 Azure 服務都會設定資源和功能的預設限制和配額。   Azure 春季雲端提供兩個定價層：基本和標準。 我們將在本文中詳細說明這兩個層級的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 春季雲端服務層和限制

| 資源 | 基本 | 標準
------- | ------- | -------
vCPU | 每個服務實例1個 | 每個服務實例4個
Memory | 每個服務實例 2 GB | 每個服務實例 8 GB
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10 | 10
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 25 | 500
永續性磁碟區 | 1 GB/app x 10 應用程式 | 50 GB/app x 10 應用程式


在預覽期間，Azure 春季雲端僅提供一個服務層。 當您達到限制時，您會收到400錯誤，其中會顯示「配額超過訂用帳戶在*您的 Azure 春季雲端服務建立所在區域區域*中的訂用帳戶限制」。 *your subscription*

## <a name="next-steps"></a>後續步驟

有些預設限制可以增加。 如果您的安裝程式需要增加，請[建立支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
