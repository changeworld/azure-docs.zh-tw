---
title: 停用異地備份
description: 如何在 Azure Synapse Analytics 中停用專用 SQL 集區的異地備份 (先前的 SQL DW) 的操作指南
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739097"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中停用專用 SQL 集區 (先前為 SQL DW) 的異地備份

在本文中，您將瞭解如何停用專用 SQL 集區的異地備份 (先前的 SQL DW) Azure 入口網站。

## <a name="disable-geo-backups-through-azure-portal"></a>透過 Azure 入口網站停用異地備份

遵循下列步驟來停用您的專用 SQL 集區 (先前的 SQL DW) 的異地備份：

> [!NOTE]
> 如果您停用異地備份，您將無法再將先前 SQL DW)  (的專用 SQL 集區復原到另一個 Azure 區域。 
>

1. 登入您的 [Azure 入口網站](https://portal.azure.com/) 帳戶。
1. 選取您想要停用異地備份的專用 SQL 集區 (先前的 SQL DW) 資源。 
1. 在左側導覽面板的 [ **設定** ] 底下，選取 [ **異地備份原則**]。

   ![停用異地備份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. 若要停用異地備份，請選取 [ **已停用**]。 

   ![停用異地備份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. 選取 [ *儲存* ] 以確保儲存您的設定。 

   ![儲存異地備份設定](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>後續步驟

- [將現有的專用 SQL 集區還原 (先前的 SQL DW) ](sql-data-warehouse-restore-active-paused-dw.md)
- [將已刪除的專用 SQL 集區還原 (先前的 SQL DW) ](sql-data-warehouse-restore-deleted-dw.md)
