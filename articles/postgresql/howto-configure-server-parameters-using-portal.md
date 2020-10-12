---
title: 設定伺服器參數-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何透過 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫中設定 Postgres 參數。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707688"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>透過 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中設定伺服器參數 
您可以透過 Azure 入口網站列出、顯示、更新適用於 PostgreSQL 的 Azure 資料庫伺服器的設定參數。

## <a name="prerequisites"></a>必要條件
為了逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>檢視和編輯參數
1. 開啟 [Azure 入口網站](https://portal.azure.com)。

2. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

3. 在 [設定]**** 區段下，選取 [伺服器參數]****。 頁面會顯示一份參數、值和說明的清單。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="參數的概觀頁面":::

4. 選取**下拉**按鈕可查看列舉型別參數 (如 client_min_messages) 的可能值。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="參數的概觀頁面":::

5. 選取或將滑鼠停留在 [i]**** (資訊) 按鈕上可查看數值參數 (如 cpu_index_tuple_cost) 的可能值範圍。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="參數的概觀頁面":::

6. 如有需要，使用 [搜尋方塊]**** 縮小至特定參數。 搜尋作業是依據參數的名稱和說明。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="參數的概觀頁面":::

7. 變更您想要調整的參數值。 您在工作階段中所做的所有變更都會以紫色顯示。 變更值之後，可以選取 [儲存]****。 或者，也可以 [捨棄]**** 您的變更。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="參數的概觀頁面":::

8. 儲存新的參數值後，隨時可以選取 [全部重設為預設值]**** 回復為所有參數的預設值。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="參數的概觀頁面":::

## <a name="next-steps"></a>後續步驟
了解：
- [適用於 PostgreSQL 的 Azure 資料庫中的伺服器參數概觀](concepts-servers.md)
- [使用 Azure CLI 設定參數](howto-configure-server-parameters-using-cli.md)
