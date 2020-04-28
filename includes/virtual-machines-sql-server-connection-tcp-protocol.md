---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67173921"
---
1. 透過遠端桌面連線到虛擬機器時，請搜尋 [組態管理員]****：

    ![開啟 SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. 在 SQL Server 組態管理員的主控台窗格中，展開 [SQL Server 網路組態]****。

1. 在主控台窗格中，按一下 [ **MSSQLSERVER 的通訊協定**] （預設實例名稱）。在詳細資料窗格中，以滑鼠右鍵按一下 [ **TCP** ]，然後按一下 [**啟用**] （如果尚未啟用）。

    ![啟用 TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. 在主控台窗格中，按一下 **[SQL Server 服務]**。 在詳細資料窗格中，以滑鼠右鍵按一下 [SQL Server (執行個體名稱)]****** \(預設執行個體是 SQL Server (MSSQLSERVER)****)，然後按一下 [重新啟動]**** 以停止及重新啟動 SQL Server 執行個體。

    ![重新啟動 Database Engine](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. 關閉 SQL Server 組態管理員。

如需啟用 SQL Server Database Engine 之通訊協定的詳細資訊，請參閱 [啟用或停用伺服器網路通訊協定](https://msdn.microsoft.com/library/ms191294.aspx)。
