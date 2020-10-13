---
title: 將停機時間降至適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure 資料庫移轉服務，以最短停機時間將于 postgresql 資料庫移轉至適用於 PostgreSQL 的 Azure 資料庫單一伺服器。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1f1af53388f177dc348c5cb805ef8e6fbe9f9436
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710816"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>將停機時間降至適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

您可以使用 [Azure 資料庫移轉服務](https://aka.ms/get-dms) (DMS) 新導入的**持續同步功能**，以最短的停機時間執行從 PostgreSQL 到「適用於 PostgreSQL 的 Azure 資料庫」的移轉。 這項功能可縮短應用程式所造成的停機時間。

## <a name="overview"></a>概觀
Azure DMS 會執行「適用於 PostgreSQL 的 Azure 資料庫」內部部署的初始載入，然後在應用程式仍在執行時持續將任何新交易同步至 Azure。 當資料同步至目標 Azure 端後，您應短暫停止應用程式 (最短停機時間)，等待最後一批資料 (從您停止應用程式，到應用程式實際上無法再接收任何新流量為止) 同步至目標端，然後再更新連接字串以指向 Azure。 當您完成時，應用程式將會在 Azure 上執行！

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="透過 Azure 資料庫移轉服務進行持續同步":::

## <a name="next-steps"></a>後續步驟
- 檢視[使用 Microsoft Azure 將應用程式現代化](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)影片，影片中會示範如何將 PostgreSQL 應用程式移轉至適用於 PostgreSQL 的 Azure 資料庫。
- 請參閱[在線上使用 DMS 將 PostgreSQL 遷移至適用於 PostgreSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)教學課程。
