---
title: 在 Azure 中為 RHEL 虛擬機器上的 SQL Server 設定可用性群組接聽程式 - Linux 虛擬機器 | Microsoft Docs
description: 了解在 Azure 中為 RHEL 虛擬機器上的 SQL Server 設定可用性群組接聽程式的相關資訊
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096910"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>教學課程：在 Azure 中為 RHEL 虛擬機器上的 SQL Server 設定可用性群組接聽程式

> [!NOTE]
> 本教學課程將以**公開預覽**講解。 
>
> 我們在本教學課程中使用 SQL Server 2017 和 RHEL 7.6，但在 RHEL 7 或 RHEL 8 中使用 SQL Server 2019 設定 HA，也是可行的。 用來設定可用性群組資源的命令在 RHEL 8 中已有所變更，您可以參考[建立可用性群組資源](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource)一文和 RHEL 8 資源，以取得正確命令的詳細資訊。

本教學課程將逐步說明如何在 Azure 中為 RHEL 虛擬機器上的 SQL Server 建立可用性群組接聽程式。 您將了解如何：

> [!div class="checklist"]
> - 在 Azure 入口網站中建立負載平衡器
> - 設定負載平衡器的後端 IP 集區
> - 建立負載平衡器的探查
> - 設定負載平衡規則
> - 在叢集中建立負載平衡器資源
> - 建立可用性群組接聽程式
> - 測試接聽程式連線
> - 測試容錯移轉

## <a name="prerequisite"></a>必要條件

已完成[**教學課程：在 Azure 中為 RHEL 虛擬機器上的 SQL Server 設定可用性群組**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

下列指示會帶您了解[負載平衡器 - Azure 入口網站](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)文章的[在 Azure 入口網站中建立和設定負載平衡器](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)一節中的步驟 1 到 4。

### <a name="create-the-load-balancer"></a>建立負載平衡器

1. 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 

2. 在資源群組中，按一下 [新增]  。

3. 搜尋**負載平衡器**，然後在搜尋結果中，選取由 **Microsoft** 發佈的 [負載平衡器]  。

4. 在 [負載平衡器]  刀鋒視窗上，按一下 [建立]  。

5. 在 [建立負載平衡器]  對話方塊中，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表負載平衡器的文字名稱。 例如 **sqlLB**。 |
   | **型別** |**內部** |
   | **虛擬網路** |建立的預設 VNet 應該命名為 **VM1VNET**。 |
   | **子網路** |選取 SQL Server 執行個體所在的子網路。 預設值應為 **VM1Subnet**。|
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** |使用在叢集中建立的 `virtualip` IP 位址。 |
   | **訂用帳戶** |使用您的資源群組所使用的訂用帳戶。 |
   | **資源群組** |選取 SQL Server 執行個體所在的資源群組。 |
   | **位置** |選取 SQL Server 執行個體所在的 Azure 位置。 |

### <a name="configure-the-back-end-pool"></a>設定後端集區
Azure 會呼叫後端位址集區 *backend pool*。 在此情況下，後端集區是您的可用性群組中三部 SQL Server 執行個體的位址。 

1. 在資源群組中，按一下您建立的負載平衡器。 

2. 在 [設定]  上，按一下 [後端集區]  。

3. 在 [後端集區]  上，按一下 [新增]  以建立後端位址集區。 

4. 在 [新增後端集區]  的 [名稱]  之下，輸入後端集區的名稱。

5. 在 [關聯性]  下，選取 [虛擬機器]  。 

6. 選取環境中的每部虛擬機器，並將適當的 IP 位址與每個選項產生關聯。

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="新增後端集區":::

7. 按一下 [新增]  。 

### <a name="create-a-probe"></a>建立探查

探查會定義 Azure 如何確認哪一個 SQL Server 執行個體目前擁有可用性群組接聽程式。 Azure 會根據在建立探查時定義的連接埠上的 IP 位址來探查服務。

1. 在負載平衡器的 [設定]  刀鋒視窗上，按一下 [健全狀況探查]  。 

2. 在 [健全狀況探查]  刀鋒視窗上，按一下 [新增]  。

3. 在 [新增探查]  刀鋒視窗上設定探查。 使用下列值來設定探查：

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表探查的文字名稱。 例如 **SQLAlwaysOnEndPointProbe**。 |
   | **通訊協定** |**TCP** |
   | **通訊埠** |您可以使用任何可用的連接埠。 例如 *59999*。 |
   | **間隔** |*5* |
   | **狀況不良臨界值** |*2* |

4.  按一下 [確定]  。 

5. 登入您所有的虛擬機器，然後使用下列命令來開啟探查連接埠：

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure 會建立探查，然後使用它來測試那一個 SQL Server 執行個體具有可用性群組的接聽程式。

### <a name="set-the-load-balancing-rules"></a>設定負載平衡規則

負載平衡規則會設定負載平衡器將流量路由傳送至 SQL Server 執行個體的方式。 對此負載平衡器，您會啟用伺服器直接回傳，因為三個 SQL Server 執行個體中一次只有一個會擁有可用性群組接聽程式資源。

1. 在負載平衡器的 [設定]  刀鋒視窗上，按一下 [負載平衡規則]  。 

2. 在 [負載平衡規則]  刀鋒視窗上，按一下 [新增]  。

3. 在 [新增負載平衡規則]  刀鋒視窗上，設定負載平衡規則。 套用下列設定： 

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表負載平衡規則的文字名稱。 例如 **SQLAlwaysOnEndPointListener**。 |
   | **通訊協定** |**TCP** |
   | **通訊埠** |*1433* |
   | **後端連接埠** |*1433*.系統會忽略此值，因為此規則使用 [浮動 IP (伺服器直接回傳)]  。 |
   | **探查** |使用您為此負載平衡器建立之探查的名稱。 |
   | **工作階段持續性** |**None** |
   | **閒置逾時 (分鐘)** |*4* |
   | **浮動 IP (伺服器直接回傳)** |**已啟用** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="新增負載平衡規則":::

4. 按一下 [確定]  。 
5. Azure 會設定負載平衡規則。 負載平衡器現已設定成將流量路由傳送到裝載可用性群組接聽程式的 SQL Server 執行個體。 

此時，資源群組有一個與所有 SQL Server 電腦連線的負載平衡器。 負載平衡器也包含 SQL Server AlwaysOn 可用性群組接聽程式的 IP 位址，以便電腦回應對可用性群組的要求。

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>在叢集中建立負載平衡器資源

1. 登入主要虛擬機器。 我們需要建立資源來啟用 Azure 負載平衡器探查連接埠 (在我們的範例中使用 59999)。 執行以下命令：

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. 建立包含 `virtualip` 和 `azure_load_balancer` 資源的群組：

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>新增限制式

1. 為了確保 Azure 負載平衡器 IP 位址和 AG 資源在相同的節點上執行，必須設定共置限制式。 執行以下命令：

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. 建立排序限制式，以確保 AG 資源會在 Azure 負載平衡器 IP 位址之前啟動並執行。 雖然共置限制式隱含排序限制式，但此步驟會強制執行它。

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. 若要驗證限制式，請執行下列命令：

    ```bash
    sudo pcs constraint list --full
    ```

    您應該會看見下列輸出：

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>建立可用性群組接聽程式

1. 在主要節點上以 SQLCMD 或 SSMS 執行下列命令：

    - 以 `virtualip` IP 位址取代下列使用的 IP 位址。

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. 登入每個 VM 節點。 使用下列命令開啟主機檔案，並為每部機器的 `ag1-listener` 設定主機名稱解析。

    ```
    sudo vi /etc/hosts
    ```

    在 **vi** 編輯器中，輸入 `i` 以插入文字，並在空白行上新增 `ag1-listener` 的 IP。 然後，在 IP 旁的空格後面新增 `ag1-listener`。

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    若要結束 **vi** 編輯器，請先按 **Esc** 鍵，然後輸入命令 `:wq` 以寫入檔案並結束。 請在每個節點上執行此動作。

## <a name="test-the-listener-and-a-failover"></a>測試接聽程式和容錯移轉

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>使用可用性群組接聽程式測試登入 SQL Server

1. 使用 SQLCMD，以可用性群組接聽程式名稱登入 SQL Server 的主要節點：

    - 使用先前建立的登入，並將 `<YourPassword>` 取代為正確的密碼。 下列範例會使用以 SQL Server 建立的 `sa` 登入。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 檢查顯示您已連線的伺服器名稱。 在 SQLCMD 中執行下列命令︰

    ```sql
    SELECT @@SERVERNAME
    ```

    您的輸出應該會顯示目前的主要節點。 如果您從未測試過容錯移轉，應該會顯示 `VM1`。

    輸入 `exit` 命令結束 SQL 工作階段。

### <a name="test-a-failover"></a>測試容錯移轉

1. 執行下列命令，將主要複本手動容錯移轉至 `<VM2>` 或另一個複本。 將 `<VM2>` 取代為您伺服器名稱的值。

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. 如果您檢查限制式，會看到因為手動容錯移轉而新增了另一個限制式：

    ```bash
    sudo pcs constraint list --full
    ```

    您會看到已新增識別碼為 `cli-prefer-ag_cluster-master` 的限制式。

1. 使用下列命令，移除識別碼為 `cli-prefer-ag_cluster-master` 的限制式：

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. 使用命令 `sudo pcs resource` 檢查您的叢集資源，您應該會看到此時的主要執行個體為 `<VM2>`。

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. 使用 SQLCMD 及接聽程式名稱登入主要複本：

    - 使用先前建立的登入，並將 `<YourPassword>` 取代為正確的密碼。 下列範例會使用以 SQL Server 建立的 `sa` 登入。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 檢查您已連線的伺服器。 在 SQLCMD 中執行下列命令︰

    ```sql
    SELECT @@SERVERNAME
    ```

    您應該會看到現在已連線到您已進行容錯移轉的 VM。

## <a name="next-steps"></a>後續步驟

如需關於 Azure 中負載平衡器的詳細資訊，請參閱：

> [!div class="nextstepaction"]
> [在 Azure SQL Server VM 中設定可用性群組的負載平衡器](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
