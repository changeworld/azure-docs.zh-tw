---
title: 透明資料加密(門戶)
description: Azure 突觸分析中的透明資料加密 (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745238"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>開始使用透明資料加密 (TDE)

> [!div class="op_single_selector"]
>
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [驗證][](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>必要權限

您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## <a name="enabling-encryption"></a>啟用加密

要啟用 TDE,請按照以下步驟操作:

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選擇 **"透明資料加密**選項![門戶設置"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 選擇 **「打開**」![設置門戶設置。](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 選擇 **"保存**
   ![門戶設置保存"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>停用加密

要禁用 TDE,請按照以下步驟操作:

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選擇 **"透明資料加密**選項![門戶設置"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 選擇**Off**關閉![設定門戶設定](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. 選擇 **"保存**
   ![門戶保存 2"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>加密 DMV

可以使用下列 DMW 來確認加密：

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
