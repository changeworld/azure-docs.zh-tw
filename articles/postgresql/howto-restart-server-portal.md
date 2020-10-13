---
title: 重新開機伺服器-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure 入口網站來重新開機適用於 PostgreSQL 的 Azure 資料庫單一伺服器。
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907373"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>使用 Azure 入口網站重新開機適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
本主題說明如何重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器。 您可能會為了進行維護而需要重新啟動伺服器，進而在伺服器執行作業時導致短暫中斷。

如果服務忙碌中，系統會阻止伺服器重新啟動。 例如，該服務可能正在處理先前要求的作業，例如調整虛擬核心。
 
完成重新啟動所需的時間取決於 PostgreSQL 復原程序。 若要減少重新啟動時間，建議您先盡量減少伺服器上發生的活動數量，再進行重新啟動。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>執行伺服器重新啟動

下列步驟會重新啟動 PostgreSQL 伺服器：

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您的適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在伺服器 [概觀]**** 頁面的工具列中，按一下 [重新啟動]****。

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫 - 概觀 - 重新啟動按鈕":::

3. 按一下 [是]**** 以確認要重新啟動伺服器。

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫 - 概觀 - 重新啟動按鈕":::

4. 您會發現伺服器的狀態變更為「正在重新啟動」。

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫 - 概觀 - 重新啟動按鈕":::

5. 確認伺服器重新啟動已成功。

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫 - 概觀 - 重新啟動按鈕":::

## <a name="next-steps"></a>後續步驟

瞭解 [如何在適用於 PostgreSQL 的 Azure 資料庫中設定參數](howto-configure-server-parameters-using-portal.md)