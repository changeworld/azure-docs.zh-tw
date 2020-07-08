---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 4e79fef08af8ff73ce63ab4732c9efd77e3a5d3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173934"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>設定公用 IP 位址的 DNS 標籤

若要從網際網路連線到 SQL Server Database Engine，請考慮建立公用 IP 位址的 DNS 標籤。 您可以經由 IP 位址連線，但 DNS 標籤可建立較容易識別的 A 記錄並擷取基礎公用 IP 位址。

> [!NOTE]
> 如果您計畫只連線到同一個虛擬網路中或只連線到本機的 SQL Server 執行個體，則不需要 DNS 標籤。

若要建立 DNS 標籤，請先在入口網站選取 [虛擬機器] **** 。 選取 SQL Server VM 以顯示其屬性。

1. 在虛擬機器概觀中，選取您的 [公用 IP 位址]****。

    ![公用 IP 位址](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. 在公用 IP 位址屬性中，展開 [組態] ****。

1. 輸入 DNS 標籤名稱。 此名稱是「A 紀錄」，可用來透過名稱 (而非直接透過 IP 位址) 連線到 SQL Server VM。

1. 按一下 [儲存]  按鈕。

    ![DNS 標籤](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>從另一部電腦連接 Database Engine

1. 在已連線網際網路的電腦上開啟 SQL Server Management Studio (SSMS)。 如果您沒有 SQL Server Management Studio，您可以在[這裡](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)下載。

1. 在 [連接到伺服器] 或 [連接到 Database Engine] 對話方塊中，編輯 [伺服器名稱] 值。 輸入虛擬機器的 IP 位址或完整 DNS 名稱 (在前一項工作中決定)。 您也可以新增逗號並提供 SQL Server 的 TCP 連接埠。 例如： `mysqlvmlabel.eastus.cloudapp.azure.com,1433` 。

1. 在 [驗證] 方塊中，選取 [SQL Server 驗證]。

1. 在 [登入]  方塊中，輸入有效的 SQL 登入名稱。

1. 在 [密碼]  方塊中，輸入登入的密碼。

1. 按一下 [ **連接**]。

    ![SSMS 連線](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)