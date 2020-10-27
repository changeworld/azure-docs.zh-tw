---
title: 自動成長儲存體-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何使用 Azure 入口網站來啟用適用於 MySQL 的 Azure 資料庫的自動成長儲存體
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 9355c2b2c780b6ccd63100e576850fe5566db3cc
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546835"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫中自動成長儲存體
本文說明如何設定適用於 MySQL 的 Azure 資料庫 server 儲存體在不影響工作負載的情況下成長。

當伺服器達到配置的儲存空間限制時，伺服器會標示為唯讀。 但是，如果您啟用存放裝置自動成長，則伺服器儲存體會增加以容納不斷成長的資料。 針對小於 100 GB 布建儲存體的伺服器，布建的儲存體大小會在可用的儲存體低於 1 GB 或10% 的布建儲存體時，立即增加 5 GB。 針對具有超過 100 GB 布建儲存體的伺服器，當可用儲存空間低於所布建儲存體大小的5% 時，布建的儲存體大小會增加5%。 在 [這裡](./concepts-pricing-tiers.md#storage) 指定的最大儲存體限制適用。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>啟用儲存體自動成長 

遵循下列步驟來設定 MySQL 伺服器儲存體自動成長：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫伺服器。

2. 在 [MySQL 伺服器] 頁面的 [ **設定** ] 標題下，按一下 [ **定價層** ] 以開啟 [定價層] 頁面。

3. 在 [自動成長] 區段中，選取 **[是]** 以啟用儲存體自動成長。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="適用於 MySQL 的 Azure 資料庫-Settings_Pricing_tier-自動成長":::

4. 按一下 [確定] 以儲存變更。

5. 通知會確認已成功啟用自動成長。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="適用於 MySQL 的 Azure 資料庫-Settings_Pricing_tier-自動成長":::

## <a name="next-steps"></a>下一步

瞭解 [如何建立計量警示](howto-alert-on-metric.md)。