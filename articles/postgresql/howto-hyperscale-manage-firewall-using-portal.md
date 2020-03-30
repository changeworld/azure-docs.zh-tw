---
title: 管理防火牆規則 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 使用 Azure 門戶為後格雷SQL - 超大規模 （Citus） 創建和管理 Azure 資料庫的防火牆規則
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977534"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>管理 Azure 資料庫的防火牆規則，用於後格雷SQL - 超大規模（Citus）
伺服器級防火牆規則可用於管理從指定的 IP 位址或 IP 位址範圍對超大規模 （Citus） 協調器節點的訪問。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本作法指南，您需要︰
- 伺服器組[為 PostgreSQL = 超大規模 （Citus） 伺服器組創建 Azure 資料庫](quickstart-create-hyperscale-portal.md)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 入口網站中建立伺服器層級的防火牆規則

> [!NOTE]
> 在創建後格雷SQL - 超大規模 （Citus） 伺服器組的 Azure 資料庫期間，還可以訪問這些設置。 在"**網路"** 選項卡下，按一下 **"公共終結點**"。
> ![Azure 門戶 - 網路選項卡](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. 在 PostgreSQL 伺服器組頁上，在"安全"標題下，按一下 **"網路**"以打開防火牆規則。

   ![Azure 門戶 - 按一下"網路"](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 按一下在工具列上**添加用戶端 IP**（下面的選項 A） 或連結（選項 B）。 無論哪種方式，都會自動創建具有電腦公共 IP 位址的防火牆規則，Azure 系統會感知到該規則。

   ![Azure 門戶 - 按一下"添加用戶端 IP"](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

或者，按一下 **+Add 0.0.0.0 - 255.255.255（** 選項 B 右側）不僅允許您的 IP，還允許整個 Internet 訪問協調節點的埠 5432。 在此情況下，用戶端仍必須使用正確的使用者名和密碼登錄才能使用群集。 然而，我們建議只允許全球訪問很短的時間，並且只允許非生產資料庫訪問。

3. 儲存設定之前，請確認您的 IP 位址。 在某些情況下，Azure 入口網站觀察到的 IP 位址，不同於用來存取網際網路和 Azure 伺服器的 IP 位址。 因此，您可能需要變更起始 IP 和結束 IP，規則才會正常運作。
   使用搜尋引擎或其他線上工具，檢查您自己的 IP 位址。 例如，搜尋「我的 IP 是什麼」。

   ![使用 Bing 搜尋「我的 IP 是什麼」](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 新增其他位址範圍。 在防火牆規則中，可以指定單個 IP 位址或位址範圍。 如果您想要將規則限制到單一 IP 位址，請在 [起始 IP] 和 [結束 IP] 欄位中輸入相同位址。 打開防火牆使管理員、使用者和應用程式能夠訪問埠 5432 上的協調器節點。

5. 按一下工具列上的 [儲存]****，儲存此伺服器等級防火牆規則。 等待確認已成功更新防火牆規則。

## <a name="connecting-from-azure"></a>從 Azure 連線

有一種簡單的方法可以授予對 Azure 上託管的應用程式（如 Azure Web 應用應用程式或在 Azure VM 中運行的應用程式）的超大規模資料庫存取權限。 只需將 **"允許 Azure 服務和資源"設置為**從 **"網路"** 窗格在**門戶中**訪問此伺服器組選項，然後點擊 **"保存**"。

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的伺服器層級防火牆規則
重複步驟來管理防火牆規則。
* 要添加當前電腦，請按一下按鈕以 =**添加用戶端 IP**。 按一下 **[儲存]**，儲存變更。
* 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。 按一下 **[儲存]**，儲存變更。
* 若要修改現有的規則，請按一下和修改規則中的任何欄位。 按一下 **[儲存]**，儲存變更。
* 若要刪除現有的規則，請按一下省略符號 [...]，然後按一下 [刪除]**** 移除規則。 按一下 **[儲存]**，儲存變更。

## <a name="next-steps"></a>後續步驟
- 詳細瞭解[防火牆規則的概念](concepts-hyperscale-firewall-rules.md)，包括如何解決連接問題。
