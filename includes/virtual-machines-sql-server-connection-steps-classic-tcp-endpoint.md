---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173922"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>為虛擬機器建立 TCP 端點
若要從網際網路存取 SQL Server，虛擬機器必須具有端點才能接聽傳入 TCP 通訊。 此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

> [!NOTE]
> 如果您要在相同的雲端服務或虛擬網路內進行連線，則不需要建立可公開存取的端點。 在此情況下，您可以繼續進行下一個步驟。 如需詳細資訊，請參閱[連接案例](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)。
> 
> 

1. 在 Azure 入口網站中，選取 [虛擬機器 (傳統)] ****。
2. 然後選取您的 SQL Server 虛擬機器。
3. 選取 [端點]****，然後按一下位於 [端點] 刀鋒視窗頂端的 [新增]**** 按鈕。
   
    ![建立端點的入口網站步驟](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. 在 [新增端點]**** 刀鋒視窗上，提供 [名稱]****，例如 SQLEndpoint。
5. 針對 [通訊協定]**** 請選取 [TCP]****。
6. 對於 [公用連接埠]****，請指定連接埠號碼，例如 **57500**。
7. 對於 [私用連接埠]****，請指定 SQL Server 的接聽連接埠，預設為 **1433**。
8. 按一下 [確定] **** 以建立端點。

