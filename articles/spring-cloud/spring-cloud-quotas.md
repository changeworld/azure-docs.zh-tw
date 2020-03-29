---
title: Azure 春雲的服務方案和配額
description: 瞭解 Azure 春雲的服務配額和服務方案
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278898"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春雲的配額和服務方案

所有 Azure 服務都為資源和功能設置預設限制和配額。  在預覽期間，Azure 春雲僅提供一個服務方案。

本文詳細介紹了當前預覽期間提供的服務配額。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季雲服務層和配額

在預覽期間，Azure 春雲僅提供一個服務層。

資源 | Amount
------- | -------
vCPU | 每個服務實例 4 個
記憶體 | 每個服務實例 8 GB
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 500
每個 Spring 應用程式的應用實例總數 | 20
永續性磁碟區 | 10 x 50 GB

達到配額時，您將收到一個 400 個錯誤，該錯誤如下："*在創建 Azure Spring 雲服務的區域*中，*訂閱*的配額超過限制。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果資源需要增加，[請創建支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
