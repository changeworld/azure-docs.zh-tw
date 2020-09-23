---
title: Azure 春季雲端的服務方案和配額
description: 瞭解 Azure 春季雲端的服務配額和服務方案
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904266"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春季雲端的配額和服務方案

本文**適用于：** ✔️ JAVA ✔️ C#

所有 Azure 服務都會設定資源和功能的預設限制和配額。   Azure 春季雲端提供兩種定價層：基本和標準。 本文將詳細說明這兩個層級的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 春季雲端服務層和限制

| 資源 | 基本 | 標準
------- | ------- | -------
vCPU | 每個服務實例1個 | 每個服務實例4個
記憶體 | 每個服務實例 2 GB | 每個服務實例 8 GB
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10 | 10
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 25 | 500
永續性磁碟區 | 1 GB/app x 10 個應用程式 | 50 GB/app x 10 應用程式

## <a name="next-steps"></a>下一步

某些預設限制可以增加。 如果您的安裝程式需要增加，請 [建立支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
