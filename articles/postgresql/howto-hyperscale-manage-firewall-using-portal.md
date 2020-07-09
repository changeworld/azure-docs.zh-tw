---
title: 管理防火牆規則 - 超大規模 (Citus) - 適用於 PostgreSQL 的 Azure 資料庫
description: 使用 Azure 入口網站建立和管理適用於 PostgreSQL Azure 資料庫的防火牆規則 - 超大規模 (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/12/2019
ms.openlocfilehash: c84616e8a9b9ff9722f5a104175c80c37dbcbcc3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116908"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>管理適用於 PostgreSQL Azure 資料庫的防火牆規則 - 超大規模 (Citus)
伺服器層級防火牆規則可從所有指定 IP 位址或 IP 位址範圍來管理對超大規模 (Citus) 協調器節點的存取。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- 伺服器群組[建立適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 伺服器群組](quickstart-create-hyperscale-portal.md)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 入口網站中建立伺服器層級的防火牆規則

> [!NOTE]
> 在建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 伺服器群組期間，也可以存取這些設定。 在 [網路] 索引標籤下，按一下 [公用端點]。
> ![Azure 入口網站 - [網路] 索引標籤](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. 在 [PostgreSQ 伺服器群組] 頁面的 [安全性] 標題底下，按一下 [網路] 以開啟防火牆規則。

   ![Azure 入口網站 - 按一下 [網路]](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 在工具列上 (下方的選項 A)，或在連結中 (選項 B) 按一下 [新增用戶端 IP]。 兩者皆會使用 Azure 系統找到的電腦公用 IP 位址來自動建立防火牆規則。

   ![Azure 入口網站 - 按一下 [新增用戶端 IP]](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

或者，按一下 [+新增 0.0.0.0 - 255.255.255.255] (選項 B 右側)，除了 IP 之外，也會允許整個網際網路存取協調器節點的連接埠 5432。 在此情況下，用戶端仍然必須使用正確的使用者名稱和密碼登入，才能使用叢集。 不過，我們建議只在短時間內且只對非生產資料庫允許全球存取。

3. 儲存設定之前，請確認您的 IP 位址。 在某些情況下，Azure 入口網站觀察到的 IP 位址，不同於用來存取網際網路和 Azure 伺服器的 IP 位址。 因此，您可能需要變更起始 IP 和結束 IP，規則才會正常運作。
   使用搜尋引擎或其他線上工具，檢查您自己的 IP 位址。 例如，搜尋「我的 IP 是什麼」。

   ![使用 Bing 搜尋「我的 IP 是什麼」](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 新增其他位址範圍。 在防火牆規則中，您可指定單一 IP 位址或位址範圍。 如果您想要將規則限制到單一 IP 位址，請在 [起始 IP] 和 [結束 IP] 欄位中輸入相同位址。 開啟防火牆可讓系統管理員、使用者和應用程式存取連接埠 5432 上的協調器節點。

5. 按一下工具列上的 [儲存]，儲存此伺服器等級防火牆規則。 等待確認已成功更新防火牆規則。

## <a name="connecting-from-azure"></a>從 Azure 連線

有一種簡單方法可將超大規模資料庫存取權授與在 Azure 上裝載的應用程式 (例如 Azure Web Apps 應用程式，或在 Azure VM 中執行的應用程式)。 只需在入口網站中，從 [網路] 窗格將 [允許 Azure 服務和資源存取此伺服器群組] 選項設定為 [是]，然後按下 [儲存]。

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的伺服器層級防火牆規則
重複步驟來管理防火牆規則。
* 若要新增目前的電腦，請按一下 [+新增用戶端 IP] 按鈕。 按一下 [儲存]  儲存變更。
* 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。 按一下 [儲存]  儲存變更。
* 若要修改現有的規則，請按一下和修改規則中的任何欄位。 按一下 [儲存]  儲存變更。
* 若要刪除現有的規則，請按一下省略符號 [...]，然後按一下 [刪除] 移除規則。 按一下 [儲存]  儲存變更。

## <a name="next-steps"></a>後續步驟
- 深入了解[防火牆規則概念](concepts-hyperscale-firewall-rules.md)，包括如何針對連線問題進行疑難排解。
