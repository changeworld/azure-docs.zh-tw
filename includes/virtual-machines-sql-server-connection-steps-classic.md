---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173923"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>決定虛擬機器的 DNS 名稱
若要從另一部電腦連接 SQL Server Database Engine，您必須知道虛擬機器的網域名稱系統 (DNS) 名稱。 (這是網際網路用來識別虛擬機器的名稱。 您可以使用 IP 位址，不過當 Azure 因備援或維護而移動資源時，IP 位址可能會改變。 DNS 名稱是穩定的，因為它可以重新導向新的 IP 位址。)  

1. 在 Azure 入口網站 (或前一個步驟) 中選取 [虛擬機器 (傳統)] ****。
2. 選取您的 SQL VM。
3. 在 [虛擬機器]**** 刀鋒視窗上，複製虛擬機器的 [DNS 名稱]****。
   
    ![DNS 名稱](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>從另一部電腦連接 Database Engine
1. 在連接網際網路的電腦上開啟 SQL Server Management Studio。
2. 在 [連接到伺服器]**** 或 [連接到 Database Engine]**** 對話方塊的 [伺服器名稱]**** 方塊中，輸入虛擬機器的 DNS 名稱 (於上一個工作中決定) 和 *DNSName,portnumber* 格式的公用端點連接埠名稱 (如 **mysqlvm.cloudapp.net,57500**)。
   
    ![使用 SSMS 進行連線](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    如果您不記得之前建立的公用端點連接埠號碼您可以在 [虛擬機器]**** 刀鋒視窗的 [端點]**** 區域中找到。
   
    ![公用連接埠](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. 在 [驗證]**** 方塊中，選取 [SQL Server 驗證]****。
4. 在 [登入] **** 方塊中，輸入於先前工作中建立之登入的名稱。
5. 在 [密碼] **** 方塊中，輸入於先前工作中建立之登入的密碼。
6. 按一下 [連線]****。

