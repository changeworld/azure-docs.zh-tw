---
title: 業務連續性 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 本文介紹在使用 Azure 資料庫進行 PostgreSQL 時的業務連續性（時間點還原、資料中心中斷、異地還原）。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981923"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>使用 Azure 資料庫進行後格雷SQL - 單伺服器的業務連續性概述

本概觀說明適用於 PostgreSQL 的 Azure 資料庫針對商務持續性和災害復原所提供的功能。 了解有哪些選項可讓您從可能導致資料遺失或造成資料庫和應用程式無法使用的干擾性事件中復原。 了解當使用者或應用程式錯誤影響資料完整性、Azure 區域中斷，或您的應用程式需要維護時該如何處理。

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>可用來提供商務持續性的功能

適用於 PostgreSQL 的 Azure 資料庫提供包含自動備份以及可讓使用者起始異地還原的商務持續性功能。 每項功能分別具有適用於預估復原時間 (ERT) 和潛在資料遺失的不同特性。 一旦您了解這些選項，就可以在其中選擇，並針對不同情況加以搭配使用。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間 - 這是您的復原時間目標 (RTO)。 您也必須了解在干擾性事件之後，應用程式在復原時可容許遺失的最近資料更新 (時間間隔) 最大數量 - 這是您的復原點目標 (RPO)。

下表將比較各項可用功能的 ERT 與 RPO：

| **功能** | **基本** | **通用用途** | **記憶體優化** |
| :------------: | :-------: | :-----------------: | :------------------: |
| 從備份進行時間點還原 | 保留期間內的任何還原點 | 保留期間內的任何還原點 | 保留期間內的任何還原點 |
| 從異地複寫備份進行異地還原 | 不支援 | ERT < 12 小時<br/>RPO < 1 小時 | ERT < 12 小時<br/>RPO < 1 小時 |

> [!IMPORTANT]
> 已刪除的伺服器**無法**還原。 如果您刪除伺服器，所有屬於該伺服器的資料庫也會一併刪除，且無法復原。 使用[Azure 資源鎖](../azure-resource-manager/management/lock-resources.md)有助於防止意外刪除伺服器。

## <a name="recover-a-server-after-a-user-or-application-error"></a>在使用者或應用程式錯誤之後復原伺服器

您可以使用服務的備份，在各種干擾性事件發生之後復原伺服器。 使用者可能會不小心刪除某些資料、不小心卸除重要的資料表，或甚至是卸除整個資料庫。 應用程式可能會因為應用程式缺陷或其他原因，而不慎以不正確的資料覆寫正確的資料。

您可以執行**時間點還原**，以創建伺服器的副本到已知良好的時間點。 此時間點必須在您為伺服器設定的備份保留期間內。 資料還原至新的伺服器之後，您可以將原始伺服器取代為還原的新伺服器，或從還原的伺服器將所需的資料複製到原始伺服器。

## <a name="recover-from-an-azure-data-center-outage"></a>從 Azure 資料中心中斷中恢復

雖然很罕見，但 Azure 資料中心也可能會有中斷的時候。 發生中斷時，可能只會讓營運中斷幾分鐘，但也可能會持續幾小時。

其中一個選項，是在資料中心中斷結束後等待區域資料中心中斷重新上線。 這適用於可承受伺服器離線一段時間的應用程式，例如在開發環境中。 當資料中心發生中斷時，您不知道中斷可能會持續多長時間，因此此選項僅在您一段時間內不需要伺服器時才有效。

## <a name="geo-restore"></a>異地還原

地理還原功能使用異地冗余備份還原伺服器。 備份託管在伺服器的[配對區域](../best-practices-availability-paired-regions.md)中。 可以從這些備份還原到任何其他區域。 地理還原將創建一台包含備份資料的新伺服器。 從[備份和還原概念文章中](concepts-backup.md)瞭解有關地理還原的更多內容。

> [!IMPORTANT]
> 只有使用異地備援備份儲存體來佈建伺服器時，才可進行異地還原。 如果您希望將現有伺服器從本機備援切換到異地備援備份，您必須先透過現有伺服器的 pg_dump 進行傾印後，再將其還原至已設定為異地備援備份的新建伺服器。

## <a name="cross-region-read-replicas"></a>跨區域讀取副本
您可以使用跨區域讀取副本來增強業務連續性和災害復原規劃。 使用 PostgreSQL 的物理複製技術非同步更新讀取副本。 詳細瞭解讀取副本、可用區域以及如何從[讀取副本概念中](concepts-read-replicas.md)容錯移轉。 

## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的自動備份](concepts-backup.md)。 
- 了解如何使用 [Azure 入口網站](howto-restore-server-portal.md)或 [Azure CLI](howto-restore-server-cli.md) 來進行還原。
- 了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。