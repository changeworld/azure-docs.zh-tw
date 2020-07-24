---
title: 啟用 Stretch Database 的透明資料加密
description: 為 Azure 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE)
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d632d078db55696dad7bb05e72368b13610b5978
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091414"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>為 Azure 上的 Stretch Database 啟用透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
> * [T Q](sql-server-stretch-database-tde-tsql.md)
>
>

透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。 資料庫加密金鑰是由內建伺服器憑證保護。 內建伺服器憑證對每個 Azure 伺服器都是唯一的。 Microsoft 會每隔 90 天自動輪換這些憑證。 如需 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

## <a name="enabling-encryption"></a>啟用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 啟用 TDE，請執行下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選取 [Azure 入口網站的 [**透明資料加密**] 選項 ![ 螢幕擷取畫面，並顯示 [設定] 分頁。 在 [一般] 區段中，會反白顯示 [透明資料加密]。][1]
4. 選取 [**開啟**] 設定，然後選取 [**儲存** 
    ![ Azure 入口網站的螢幕擷取畫面，並顯示 [透明資料加密] 分頁。 已開啟資料加密，並反白顯示 [儲存] 按鈕。][2]

## <a name="disabling-encryption"></a>停用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 停用 TDE，請執行下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀鋒視窗中，按一下 [設定] **** 按鈕
3. 選取 [透明資料加密] **** 選項 
4. 選取 [關閉]**** 設定，然後選取 [儲存]****

<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
