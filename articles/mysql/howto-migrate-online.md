---
title: 減少停機時間-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何使用 Azure 資料庫移轉服務，以最短的停機時間執行從 MySQL 資料庫到「適用於 MySQL 的 Azure 資料庫」的移轉。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540326"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>以最短停機時間移轉至適用於 MySQL 的 Azure 資料庫
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

您可以使用 [Azure 資料庫移轉服務](https://aka.ms/get-dms) (DMS) 新導入的 **持續同步功能** ，以最短的停機時間執行從 MySQL 到「適用於 MySQL 的 Azure 資料庫」的移轉。 這項功能可縮短應用程式所造成的停機時間。

您也可以參閱 [資料庫移轉指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) ，以取得有關將資料庫移轉至適用於 MySQL 的 Azure 資料庫的詳細資訊和使用案例。 本指南提供將 MySQL 遷移至 Azure 的成功規劃和執行的指引。

## <a name="overview"></a>概觀
DMS 會執行「適用於 MySQL 的 Azure」內部部署的初始載入，然後在應用程式仍在執行時持續將任何新交易同步至 Azure。 當資料同步至目標 Azure 端後，您應短暫停止應用程式 (最短停機時間)，等待最後一批資料 (從您停止應用程式，到應用程式實際上無法再接收任何新流量為止) 同步至目標端，然後再更新連接字串以指向 Azure。 當您完成時，應用程式將會在 Azure 上執行！

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="透過 Azure 資料庫移轉服務進行持續同步":::

## <a name="next-steps"></a>後續步驟
- 若要深入了解如何將資料庫移轉至適用於 MySQL 的 Azure 資料庫，請參閱[資料庫移轉指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
- 檢視[輕鬆地將 MySQL/PostgreSQL 應用程式遷移至 Azure 受控服務](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)影片，影片中會示範如何將 MySQL 應用程式移轉至適用於 MySQL 的 Azure 資料庫。
- 請參閱[使用 DMS 在線上將 MySQL 移轉至適用於 MySQL 的 Azure 資料庫](../dms/tutorial-mysql-azure-mysql-online.md)教學課程。