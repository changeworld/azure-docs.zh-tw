---
title: 透明資料加密（門戶）
description: Azure 突觸分析中的透明資料加密 （TDE）
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
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350539"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>開始使用透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [驗證][](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>必要權限
您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## <a name="enabling-encryption"></a>啟用加密
要啟用 TDE，請按照以下步驟操作：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選擇 **"透明資料加密**選項![門戶設置"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 選擇 **"打開**"![設置門戶設置。](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 選擇 **"保存**
   ![門戶設置保存"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>停用加密
要禁用 TDE，請按照以下步驟操作：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選擇 **"透明資料加密**選項![門戶設置"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 選擇**關閉**設置![門戶設置](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. 選擇 **"保存**
   ![門戶保存 2"](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>加密 DMV
可以使用下列 DMW 來確認加密：

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
