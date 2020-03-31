---
title: 視頻索引子容錯移轉和災害復原
titleSuffix: Azure Media Services
description: 瞭解如何在區域資料中心發生故障或災難時容錯移轉到輔助視頻索引子帳戶。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499614"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>視頻索引子容錯移轉和災害復原

如果區域資料中心中斷或發生故障，Azure 媒體服務視頻索引子不會提供服務的即時容錯移轉。 本文介紹如何為容錯移轉配置環境，以確保應用的最佳可用性，並在發生災難時最大限度地減少恢復時間。

我們建議您跨區域對配置業務連續性災害復原 （BCDR），以便從 Azure 的隔離和可用性策略中受益。 如需詳細資訊，請參閱 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="prerequisites"></a>Prerequisites

Azure 訂用帳戶。 如果尚未使用 Azure 訂閱，請註冊[Azure 免費試用](https://azure.microsoft.com/free/)版。

## <a name="failover-to-a-secondary-account"></a>容錯移轉到輔助帳戶

要實現 BCDR，您需要有兩個視頻索引子帳戶來處理冗余。

1. 創建連接到 Azure 的兩個視頻索引子[帳戶（請參閱創建視頻索引子帳戶](connect-to-azure.md)）。 為主區域創建一個帳戶，為配對的 Azure 區域創建另一個帳戶。
1. 如果主區域出現故障，請使用輔助帳戶切換到索引。

> [!TIP]
> 您可以通過根據"[在服務通知上創建活動日誌警報](../../service-health/alerts-activity-log-service-notifications.md)"為服務運行狀況通知設置活動日誌警報來自動執行 BCDR。

有關使用多個租戶的資訊，請參閱[管理多個租戶](manage-multiple-tenants.md)。 要實現 BCDR，請選擇以下兩個選項之一：[每個租戶的視頻索引子帳戶](manage-multiple-tenants.md#video-indexer-account-per-tenant)或[每個租戶的 Azure 訂閱](manage-multiple-tenants.md#azure-subscription-per-tenant)。

## <a name="next-steps"></a>後續步驟

[管理連接到 Azure 的視頻索引子帳戶](manage-account-connected-to-azure.md)。
