---
title: 自動增長存儲 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 本文介紹如何使用 Azure 門戶為 MariaDB 啟用 Azure 資料庫的自動增長存儲
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528892"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>使用 Azure 門戶在 Azure 資料庫中自動增長 MariaDB 的存儲
本文介紹如何為 MariaDB 伺服器存儲配置 Azure 資料庫，以便在不影響工作負荷的情況下增長。

當伺服器達到分配的存儲限制時，伺服器被標記為唯讀。 但是，如果啟用存儲自動增長，伺服器存儲會增加以適應不斷增長的資料。 對於預配存儲小於 100 GB 的伺服器，一旦可用存儲低於 1 GB 或預配存儲的 10%， 則預配存儲大小將增加 5 GB。 對於具有 100 GB 以上預配存儲的伺服器，當可用存儲空間低於預配存儲大小的 5% 時，預配存儲大小將增加 5%。 [此處](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)指定的最大存儲限制適用。

## <a name="prerequisites"></a>Prerequisites
若要完成本操作說明指南，您需要：
- [MariaDB 伺服器的 Azure 資料庫](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>實現存儲自動增長 

按照以下步驟設置 MariaDB 伺服器存儲自動增長：

1. 在[Azure 門戶](https://portal.azure.com/)中，為 MariaDB 伺服器選擇現有的 Azure 資料庫。

2. 在 MariaDB 伺服器頁面上，按一下"**設置"** 標題，按一下 **"定價層**"以打開定價層頁面。

3. 在"自動增長"部分中，選擇 **"是**"以啟用存儲自動增長。

    ![MariaDB 的 Azure 資料庫 - Settings_Pricing_tier - 自動增長](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. 按一下 [確定]**** 儲存變更。

5. 通知將確認自動增長已成功啟用。

    ![用於 MariaDB 的 Azure 資料庫 - 自動增長成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[在指標上創建警報](howto-alert-metric.md)。
