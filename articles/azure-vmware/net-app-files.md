---
title: 適用于 Azure VMware 解決方案的 NetApp Files
description: 使用 Azure NetApp Files 搭配 Azure VMware Vm，在內部部署伺服器、Azure VMware 解決方案 Vm 和雲端基礎結構之間遷移和同步處理資料。
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573917"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>適用于 Azure VMware 解決方案的 NetApp Files

在本文中，我們將逐步解說整合 Azure NetApp Files 與以 Azure VMware 解決方案為基礎的工作負載的步驟。 [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) 是一項 azure 服務，可在雲端中遷移和執行企業檔案工作負載，而不需變更程式碼。 它可讓您輕鬆地在內部部署和雲端基礎結構之間遷移資料。 快速、增強安全性的資料同步處理可利用即時快照、還原和 Active Directory 整合等功能，來簡化遷移和 DevOps 案例。

## <a name="topology"></a>拓撲

在此案例中，若要測試及驗證 Azure NetApp Files 集區共用，Azure NetApp Files 是在 Azure 上設定容量集區、磁片區集區和子網。 我們已使用 NFS 通訊協定。 Azure NetApp Files 和 Azure VMware 解決方案都是在美國東部的相同 Azure 區域中建立。 Azure VMware 解決方案的連線是透過 Azure ExpressRoute。

![適用于 Azure VMware 解決方案拓撲的 NetApp Files。](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>必要條件 

> [!div class="checklist"]
> * Azure 訂用帳戶與 Azure NetApp Files
> * 適用于 Azure NetApp File 的子網
> * Azure VMware 解決方案上的 Linux VM
> * Azure VMware 解決方案上的 Windows VM

## <a name="verify-configuration-of-azure-netapp-files"></a>確認 Azure NetApp Files 的設定 

首先，請確認 azure NetApp Files 的設定是在 premium 磁片的 Azure 中建立的。

1. 在 [ **儲存體**] 下的 [Azure 入口網站] 中，選取 [ **Azure NetApp Files**]。 您已設定的 Azure NetApp Files 清單隨即顯示。

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Azure NetApp Files 的清單。"::: 

2. 如果您選取 NetApp 帳戶，就可以查看各種設定。 例如，如果您選取 **Contoso-anf2**，則會看到其設定。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Azure NetApp Files 的清單。"::: 

3. 選取 **容量** 集區以驗證設定的集區。 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Azure NetApp Files 的清單。":::

    您可以看到儲存集區已設定為具有 premium 磁片的4個 TiB。

4. 選取 **磁片** 區 (查看步驟 2) 的螢幕擷取畫面，以查看容量集區下建立的磁片區。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files 的清單。":::

5. 選取磁片區以查看其設定。  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Azure NetApp Files 的清單。":::

    您可以看到 anfpool 磁片區集區（含 200 GiB）已建立為 NetApp Files 共用。 針對 Azure NetApp Files 建立了私人 IP 虛擬網路，可讓您在 Azure VMware 解決方案 Vm 上掛接 NFS 路徑。 

## <a name="protocols-supported-by-azure-netapp-files"></a>Azure NetApp Files 所支援的通訊協定  

Azure NetApp Files 支援 Server Message Block (SMB) 和網路檔案系統 (NFS) 共用對應。 

- **Smb 共用**：若要部署 smb 磁片區，您必須先建立 Active Directory 連接。 指定的網域控制站必須可供 Azure NetApp Files 的委派子網存取，才能成功連線。 一旦在 Azure NetApp Files 帳戶內設定 Active Directory，就會在建立 SMB 磁片區時，顯示為可選取的專案。 

- **Nfs 共用**： Azure NetApp Files 有助於使用 NFS (NFSv3 和 nfsv4.1 4.1) 、SMBv3 或雙重通訊協定 (NFSV3 和 SMB) 來建立磁片區。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 您可以使用命令列將 NFS 裝載至 Linux 伺服器。

## <a name="create-azure-netapp-files"></a>建立 Azure NetApp Files 

1. 登入 [Azure 入口網站](https://rc.portal.azure.com/#home)。 在 [Azure 服務] 下，選取 [ **Azure NetApp Files**]。 

2. 選取 [ **+ 新增** ] 以建立新的 Azure NetApp Files 磁片區。 

3. 填寫必要欄位 (名稱、訂用帳戶、資源群組和位置) ，然後選取 [ **建立**]。 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>將容量集區新增至 Azure NetApp Files 

1. 在 Azure 入口網站中，選取 [ **Azure NetApp Files**]，然後選取 [ **容量** 集區] 和 [ **+ 新增集**區]。 

2. 輸入磁片區集區名稱、服務等級和磁片大小的必要詳細資料， (FQDN) 或 IP 和權數。 選取 [新增]。

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Azure NetApp Files 的清單。":::

3. 若要建立容量集區底下的磁片區，請從 [搜尋] 窗格中選取 **磁片** 區，然後選取 [ **+ 新增磁片**區 
 
4. 填寫必要欄位，如下列螢幕擷取畫面所示。

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Azure NetApp Files 的清單。":::

5. 在下一個頁面上，選取 [通訊協定類型] 共用。 如果是 NFS 共用，請選取版本，如果是 SMB 共用，請選取 Active Directory 網域。  

6. 如果是 NFS 共用，請新增可從中存取通訊協定類型共用的來源 IP 位址。 選取 [檢閱 + 建立]。 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Azure NetApp Files 的清單。":::
 
    在 Azure 入口網站中的 Azure NetApp Files 下，您的 NFS 共用已可供使用。

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Azure NetApp Files 的清單。":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>在您的 Azure VMware 解決方案 Vm 上掛接 NFS 檔案共用

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>在 Azure VMware 解決方案 Windows VM 上掛接 NFS 檔案共用

- 開啟命令提示字元，並執行命令以對應 NFS 檔案共用。 下列螢幕擷取畫面顯示在 Azure VMware 解決方案中的 Windows VM 上對應的共用磁片磁碟機。  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Azure NetApp Files 的清單。":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Azure NetApp Files 的清單。":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>在 Azure VMware Solution Linux VM 上掛接 NFS 檔案共用

#### <a name="setting-up-your-azure-instance"></a>設定您的 Azure 實例

1. 在您的 Azure 入口網站中，將您的 Azure 實例與您磁片區在相同虛擬網路中定義的子網產生關聯。

    > [!NOTE]
    > 子網需要網路安全性群組規則，以允許 NFS 埠 (2049、111) 、UDP 和 TCP 的流量。

2. 開啟 SSH 用戶端，並連接到您的 Azure 實例。 如需詳細資訊，請參閱 [如何在 Azure 上搭配 Windows 使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)。

3. 在您的 Azure 實例上安裝 NFS 用戶端：
   - 在 Red Hat Enterprise Linux 或 SUSE Linux 實例上： `sudo yum install -y nfs-utils`
   - 在 Ubuntu 或 Debian 實例上： `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>裝載檔案系統

1. 在您的 Azure 實例上建立新目錄： `sudo mkdir ANFPOOL`

2. 選取裝載目標 IP 位址。

3. 掛接檔案系統： `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Azure NetApp Files 的清單。":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>建立 SMB 共用的 Active Directory 連接

1. 選取 Azure 入口網站中的 NetApp 帳戶。

2. 在 [Azure NetApp Files] 下，選取 [ **Active Directory 連接**]。

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Azure NetApp Files 的清單。"::: 

3. 選取 [ **加入** ] 以加入 SMB 共用以 Active Directory。 下列螢幕擷取畫面顯示 SMB 共用的網域詳細資料。

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Azure NetApp Files 的清單。"::: 

    您的 Azure SMB 檔案共用已可供使用。  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Azure NetApp Files 的清單。"::: 

4. 將 SMB 共用對應到您的 Windows Azure VMware 解決方案 VM。 使用 SMB 掛接路徑，如先前的螢幕擷取畫面所示。 如需詳細資訊，請參閱[Windows 10 中的對應網路磁碟機](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)機。

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [Azure NetApp Files 的儲存體](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)階層。
- 查看 [Azure VMware 解決方案 vm 的生命週期管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- 請參閱 [整合中樞和輪輻架構中的 Azure VMware 解決方案](concepts-hub-and-spoke.md)
