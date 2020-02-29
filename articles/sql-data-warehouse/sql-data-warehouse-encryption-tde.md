---
title: 透明資料加密（入口網站）
description: Azure Synapse Analytics 中的透明資料加密（TDE）
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: c121f9c16895a749922525d1ba85ee2c2e60cfb0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195851"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>開始使用透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [安全性概觀](sql-data-warehouse-overview-manage-security.md)
> * [驗證](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>必要權限
您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## <a name="enabling-encryption"></a>啟用加密
若要啟用 TDE，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] 按鈕
3. 選取 [透明資料加密] 選項 ![][1]
4. 選取 [開啟] 設定 ![][2]
5. 選取 [儲存]
   ![][3]  

## <a name="disabling-encryption"></a>停用加密
若要停用 TDE，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定] 按鈕
3. 選取 [透明資料加密] 選項 ![][1]
4. 選取 [關閉] 設定 ![][4]
5. 選取 [儲存]
   ![][5]  

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
