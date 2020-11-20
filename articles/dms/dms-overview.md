---
title: 何謂 Azure 資料庫移轉服務？
description: Azure 資料庫移轉服務的概觀，此服務能從許多資料庫來源提供無縫移轉到 Azure 資料平台。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963006"
---
# <a name="what-is-azure-database-migration-service"></a>何謂 Azure 資料庫移轉服務？

Azure 資料庫移轉服務是一個完全受控的服務，其設計目的是能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低 (線上移轉)。

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>使用熟悉的工具來將資料庫移轉到 Azure

Azure 資料庫移轉服務整合我們現有工具和服務的某些功能。 它提供客戶全方位、高可用性的解決方案。 服務會使用[資料移轉小幫手](/sql/dma/dma-overview)來產生評估報表，提供建議以引導您在移轉之前完成所需的變更。 由您自行決定，是否要執行任何所需的補救。 當您準備好要開始移轉程序時，Azure 資料庫移轉服務會執行所有必要步驟。 移轉程序會善用 Microsoft 決定的最佳做法，因此您可以放心地移轉專案。 

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。

## <a name="regional-availability"></a>區域可用性

如需關於 Azure 資料庫移轉服務區域可用性的最新資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)。

## <a name="pricing"></a>定價

如需關於 Azure 資料庫移轉服務定價的最新資訊，請參閱 [Azure 資料庫移轉服務定價](https://azure.microsoft.com/pricing/details/database-migration/)。

## <a name="next-steps"></a>後續步驟

* [Azure 資料庫移轉服務支援的移轉案例狀態](resource-scenario-status.md)。
* [使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體](quickstart-create-data-migration-service-portal.md)。
* [將 SQL Server 移轉到 Azure SQL Database](tutorial-sql-server-to-azure-sql.md)。
* [使用 Azure 資料庫移轉服務的必要條件概觀](pre-reqs.md)。
* [使用 Azure 資料庫移轉服務的相關常見問題集](faq.md)。
* [資料移轉案例可用的服務和工具](dms-tools-matrix.md)。