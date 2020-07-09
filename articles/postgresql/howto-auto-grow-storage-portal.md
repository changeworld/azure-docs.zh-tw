---
title: 自動成長儲存體-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的 Azure 入口網站來設定存放裝置自動成長
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 24ca9d84e8e05b2ffe093dc54963c1ac39464105
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119611"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>使用適用於 PostgreSQL 的 Azure 資料庫中的 Azure 入口網站自動增加儲存體-單一伺服器
本文說明如何在不影響工作負載的情況下，將適用於 PostgreSQL 的 Azure 資料庫伺服器存放裝置設定為可成長。

當伺服器達到配置的儲存空間限制時，伺服器會標示為唯讀。 不過，如果您啟用存放裝置自動成長，伺服器儲存體會增加以容納成長中的資料。 針對具有小於 100 GB 布建儲存體的伺服器，布建的儲存體大小會在可用的儲存體低於 1 GB 或10% 的已布建儲存體的最大值時，增加 5 GB。 針對具有超過 100 GB 已布建儲存體的伺服器，當可用儲存空間低於布建的儲存體大小的5% 時，布建的儲存體大小會增加5%。 [這裡](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)所指定的最大儲存體限制。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>啟用存放裝置自動成長 

請遵循下列步驟來設定于 postgresql 伺服器存放裝置自動成長：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在 [于 postgresql 伺服器] 頁面的 [**設定**] 底下，按一下 [**定價層**] 以開啟 [定價層] 頁面。

3. 在 [**自動成長**] 區段中，選取 **[是]** 以啟用儲存體自動成長。

    ![適用於 PostgreSQL 的 Azure 資料庫-Settings_Pricing_tier-自動成長](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. 按一下 [確定]**** 儲存變更。

5. 通知會確認已成功啟用自動成長。

    ![適用於 PostgreSQL 的 Azure 資料庫-自動成長成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[如何建立計量的警示](howto-alert-on-metric.md)。
