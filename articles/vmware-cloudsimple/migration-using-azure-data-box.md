---
title: Azure VMware 解決方案-使用 Azure 資料箱遷移
description: 如何使用 Azure 資料箱將資料大量遷移至 Azure VMware 解決方案。
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507653"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>使用 Azure 資料箱將資料移轉至 Azure VMware 解決方案

Microsoft Azure Data Box 雲端解決方案可讓您以快速、實惠且可靠的方式，將 tb (Tb 的資料) 傳送至 Azure。 透過向您寄送專屬的資料箱存放裝置，來加速安全的資料傳輸。 每個存放裝置的可用儲存體容量上限為 80 TB，且由區域貨運公司傳輸到您的資料中心。 該裝置具有堅固的外殼，可在運輸過程中保護裝置與資料安全。

您可以使用資料箱，將 VMware 資料大量遷移至私人雲端。 內部部署 VMware vSphere 環境中的資料會透過網路檔案系統 (NFS) 通訊協定複製到資料箱。 大量資料移轉牽涉到將虛擬機器、設定和相關資料的時間點複本儲存至資料箱，然後以手動方式將其傳送至 Azure。

在本文中，您將瞭解：

* 設定資料箱。
* 透過 NFS 將資料從內部部署 VMware 環境複製到資料箱。
* 正在準備資料箱的退貨。
* 正在準備要複製到 Azure VMware 解決方案的 blob 資料。
* 將資料從 Azure 複製到您的私人雲端。

## <a name="scenarios"></a>案例

在下列案例中使用資料箱進行大量資料移轉：

* 將大量資料從內部部署遷移至 Azure VMware 解決方案。 這個方法會建立基準，並透過網路同步處理差異。
* 若要遷移關閉的大量虛擬機器 (冷虛擬機器) 。
* 若要遷移虛擬機器資料，以設定開發和測試環境。
* 遷移大量的虛擬機器範本、ISO 檔案和虛擬機器磁片。

## <a name="before-you-begin"></a>開始之前

* 透過您的 Azure 入口網站檢查必要條件和 [訂購資料箱](../databox/data-box-deploy-ordered.md) 。 在訂單程式中，您必須選取啟用 Blob 儲存體的儲存體帳戶。 收到資料箱裝置之後，請將它連接到您的內部部署網路，然後使用可從 vSphere 管理網路連線的 IP 位址來 [設定裝置](../databox/data-box-deploy-set-up.md) 。

* 在您的 Azure VMware 解決方案布建所在的相同區域中，建立虛擬網路和儲存體帳戶。

* 遵循[使用 ExpressRoute 將 azure 虛擬網路連線到 CloudSimple](virtual-network-connection.md)中的步驟，建立從私人雲端到建立儲存體帳戶之虛擬網路的[Azure 虛擬網路](cloudsimple-azure-network-connection.md)連線。

## <a name="set-up-data-box-for-nfs"></a>設定適用于 NFS 的資料箱

遵循教學課程：纜線的「連接到您的裝置」一節中的步驟，連接到您的資料箱本機 web UI [，並連接到您的 Azure 資料箱](../databox/data-box-deploy-set-up.md)。  設定資料箱以允許存取 NFS 用戶端：

1. 在本機 web UI 中，移至 [連線 **並複製]** 頁面。 在 [ **nfs 設定**] 底下，選取 [ **nfs 用戶端存取**]。 

    ![設定 NFS 用戶端存取 1](media/nfs-client-access.png)

2. 輸入 VMware ESXi 主機的 IP 位址，然後選取 [ **新增**]。 您可以重複此步驟，以設定 vSphere 叢集中所有主機的存取權。 選取 [確定]  。

    ![設定 NFS 用戶端存取 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 *根* 資料夾。

在區塊 Blob 與分頁 Blob 共用底下，第一層實體是容器，而第二層實體是 Blob。 在 Azure 檔案儲存體的 [共用] 底下，第一層實體是共用，而第二層實體是檔案。

下表顯示您資料箱上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
| Blob 與檔案 | 路徑和 URL |
|---------------- | ------------ |
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案       |<li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 區塊 blob 複製 VMware 資料。

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>將 NFS 共用掛接為內部部署 vCenter 叢集上的資料存放區，並複製資料

您資料箱中的 NFS 共用必須裝載為內部部署 vCenter 叢集或 VMware ESXi 主機上的資料存放區，才能將資料複製到 NFS 資料存放區：

1. 登入您的內部部署 vCenter 伺服器。

2. 以滑鼠右鍵按一下 [ **Datacenter**]、[儲存體]、[**新增資料**存放**區**]，然後選取 **[下一步]**

   ![新增資料存放區](media/databox-migration-add-datastore.png)

3. 在 [新增資料存放區] 的步驟1中，選取 [**類型**] 下的 [ **NFS** ]。

   ![新增資料存放區-類型](media/databox-migration-add-datastore-type.png)

4. 在嚮導的步驟2中，選取 **nfs 3** 作為 nfs 版本，然後選取 **[下一步]**。

   ![新增資料存放區-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在嚮導的步驟3中，指定資料存放區、路徑和伺服器的名稱。 您可以針對伺服器使用資料箱的 IP 位址。 資料夾路徑的格式將會是 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 。

   ![新增資料存放區-NFS 設定](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在嚮導的步驟4中，選取您要在其中裝載資料存放區的 ESXi 主機，然後選取 **[下一步]**。  在叢集中，選取所有主機，以確保虛擬機器的遷移。

   ![新增資料存放區-選取主控制項](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在嚮導的步驟5中，檢查摘要，然後選取 **[完成]**。

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>將資料複製到 Data Box NFS 資料存放區

虛擬機器可以遷移或複製到新的資料存放區。  任何您想要遷移的未使用虛擬機器都可以使用 **Storage vMotion** 選項遷移至 DATA Box NFS 資料存放區。 使用中的虛擬機器可以複製到 Data Box NFS 資料存放區。

* 識別並列出可以 **移動**的虛擬機器。
* 識別並列出必須 **複製**的虛擬機器。

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>將虛擬機器移至 Data Box 資料存放區

1. 以滑鼠右鍵按一下您要移至 Data Box 資料存放區的虛擬機器，然後選取 [ **遷移**]。

    ![遷移虛擬機器](media/databox-migration-vm-migrate.png)

2. 選取 [ **只變更** 遷移類型的儲存體]，然後選取 **[下一步]**。

    ![只遷移虛擬機器-儲存體](media/databox-migration-vm-migrate-change-storage.png)

3. 選取 **資料箱-資料** 存放區做為目的地，然後選取 **[下一步]**。

    ![遷移虛擬機器-選取資料存放區](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 檢查資訊，然後選取 **[完成]**。

5. 針對額外的虛擬機器重複步驟1到4。

> [!TIP]
> 您可以選取處於相同電源狀態的多部虛擬機器 (開啟或關閉) 並大量遷移它們。

虛擬機器將會從資料箱遷移至 NFS 資料存放區。 遷移所有虛擬機器之後，您可以關閉 (關機) 使用中的虛擬機器，以準備將資料移轉至 Azure VMware 解決方案。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>將虛擬機器或虛擬機器範本複製到 Data Box 資料存放區

1. 以滑鼠右鍵按一下您要複製的虛擬機器或虛擬機器範本。 選取 [**複製**  >  **複製到虛擬機器**]。

    ![虛擬機器複製](media/databox-migration-vm-clone.png)

2. 選取複製的虛擬機器或虛擬機器範本的名稱。

3. 選取您要放置複製物件的資料夾，然後選取 [ **下一步]**。

4. 選取您要放置複製物件的叢集或資源集區，然後選取 [ **下一步]**。

5. 選取 **資料箱-資料** 存放區作為儲存位置，然後選取 **[下一步]**。

    ![虛擬機器複製-選取資料存放區](media/databox-migration-vm-clone-select-datastore.png)

6. 如果您想要為複製的物件自訂任何選項，請選取 [自訂] 選項，然後選取 [ **下一步]**。

7. 檢查設定，然後選取 **[完成]**。

8. 針對額外的虛擬機器或虛擬機器範本重複步驟1到步驟7。

虛擬機器將會從資料箱複製並儲存在 NFS 資料存放區。 複製虛擬機器之後，請確定它們已在準備將資料移轉至 Azure VMware 解決方案時關閉。

### <a name="copy-iso-files-to-the-data-box-datastore"></a>將 ISO 檔案複製到 Data Box 資料存放區

1. 從您的內部部署 vCenter web UI，移至 [ **儲存體**]。  選取 [**資料箱]-[資料**存放區]，然後選取 [檔案]。 **Files** 建立用來儲存 ISO 檔案的新資料夾。

    ![複製 ISO-建立新資料夾](media/databox-migration-create-folder.png)

2. 提供將儲存 ISO 檔案的資料夾名稱。

3. 按兩下新建立的資料夾以開啟它。

4. 選取 [ **上傳** 檔案]，然後選取您想要上傳的 ISO 檔案。
    
    ![複製 ISO-上傳檔案](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果您的內部部署資料存放區中已有 ISO 檔案，您可以選取檔案並 **複製到** ，以將檔案複製到 DATA Box NFS 資料存放區。


## <a name="prepare-data-box-for-return"></a>準備 Data Box 以進行退貨

在所有虛擬機器資料、虛擬機器範本資料和任何 ISO 檔案都複製到 Data Box NFS 資料存放區之後，您就可以中斷資料存放區與 vCenter 的連線。 在中斷資料存放區連線之前，必須先從清查中移除所有虛擬機器和虛擬機器範本。

### <a name="remove-objects-from-inventory"></a>從清查中移除物件

1. 從您的內部部署 vCenter web UI，移至 [ **儲存體**]。 選取 [ **資料箱]-[資料** 存放區]，然後選取 [ **vm**]。

    ![從清查移除虛擬機器-關閉](media/databox-migration-select-databox-vm.png)

2. 請確定所有虛擬機器都已關閉。

3. 選取 [所有虛擬機器]，按一下滑鼠右鍵，然後選取 [ **從清查中移除**]。

    ![從清查中移除虛擬機器](media/databox-migration-remove-vm-from-inventory.png)

4. 選取 [ **資料夾] 中的 VM 範本** ，然後重複步驟3。

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>從 vCenter 移除 Data Box NFS 資料存放區

Data Box NFS 資料存放區必須先從 VMware ESXi 的主機中斷連線，才能準備進行退回。

1. 從您的內部部署 vCenter web UI，移至 [ **儲存體**]。

2. 以滑鼠右鍵按一下 **資料箱-資料** 存放區，然後選取 [ **卸載資料**存放區]。

    ![卸載資料箱資料存放區](media/databox-migration-unmount-datastore.png)

3. 選取裝載資料存放區的所有 ESXi 主機，然後選取 **[確定]**。

    ![卸載資料箱資料存放區-選取主控制項](media/databox-migration-unmount-datastore-select-hosts.png)

4. 檢查並接受任何警告，然後選取 **[確定]**。

### <a name="prepare-data-box-for-return-and-then-return-it"></a>準備要傳回的資料箱，然後傳回它

遵循本文中所述的步驟 [，Azure 資料箱並確認資料上傳至 Azure](../databox/data-box-deploy-picked-up.md) 以傳回資料箱。 檢查將資料複製到您的 Azure 儲存體帳戶的狀態。 當狀態顯示為 [已完成] 之後，您就可以確認 Azure 儲存體帳戶中的資料。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>將資料從 Azure 儲存體複製到 Azure VMware 解決方案

當資料箱的訂單狀態顯示為 [已完成] 之後，您的 Azure 儲存體帳戶上將會有複製到您的資料箱裝置的資料。 現在可以將資料複製到您的 Azure VMware 解決方案。 您必須使用 NFS 通訊協定，將儲存體帳戶中的資料複製到您私人雲端的 vSAN 資料存放區。 

首先，使用 **AzCopy**將 Blob 儲存體資料複製到 Azure 中 Linux 虛擬機器上的受控磁片。 透過 NFS 提供受控磁片，將 NFS 共用掛接為私用雲端上的資料存放區，然後複製資料。 此方法可讓您更快速地將資料複製到您的私人雲端。

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>使用 Linux 虛擬機器和受控磁片將資料複製到您的私人雲端，然後匯出為 NFS 共用

1. 在 Azure 中建立 [Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md) ，並在您的儲存體帳戶建立所在的相同區域中建立，並將 azure 虛擬網路連線到您的私人雲端。

2. 建立儲存體容量大於 blob 資料量的受控磁片，並 [將其附加至您的 Linux 虛擬機器](../virtual-machines/linux/attach-disk-portal.md)。  如果 blob 資料的數量大於可用受控磁片的容量，則必須以多個步驟或使用多個受控磁片來複製資料。

3. 連接至 Linux 虛擬機器並掛接受控磁片。

4. [在您的 Linux 虛擬機器上安裝 AzCopy](../storage/common/storage-use-azcopy-v10.md)。

5. 使用 AzCopy 將資料從 Azure Blob 儲存體下載到受控磁片。  命令語法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` 。  請 `<storage-account-name>` 以您的 Azure 儲存體帳戶名稱取代，並 `<container-name>` 以保存透過資料箱複製之資料的容器取代。

6. 在 Linux 虛擬機器上安裝 NFS 伺服器：

    - 在 Ubuntu/Debian 散發上： `sudo apt install nfs-kernel-server` 。
    - 在 Enterprise Linux 發行版本上： `sudo yum install nfs-utils` 。

7. 變更 Azure Blob 儲存體中的資料複製到受控磁片上資料夾的許可權。  變更您想要匯出為 NFS 共用的所有資料夾的許可權。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 藉由編輯檔案，指派用戶端 IP 位址存取 NFS 共用的許可權 `/etc/exports` 。

    ```bash
    sudo vi /etc/exports
    ```
    
    針對私人雲端的每個 ESXi 主機 IP，在檔案中輸入下列幾行。  如果您要建立多個資料夾的共用，請新增所有資料夾。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 使用命令匯出 NFS 共用 `sudo exportfs -a` 。

10. 使用命令重新開機 NFS 核心伺服器 `sudo systemctl restart nfs-kernel-server` 。


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>將 Linux 虛擬機器 NFS 共用掛接為私用雲端 vCenter 叢集上的資料存放區，然後複製資料

您的 Linux 虛擬機器上的 NFS 共用必須掛接為私用雲端 vCenter 叢集上的資料存放區。 裝載之後，您可以從 NFS 資料存放區將資料複製到私用雲端 vSAN 資料存放區。

1. 登入您的私人雲端 vCenter 伺服器。

2. 以滑鼠右鍵按一下 [ **Datacenter**]、[儲存體]、[**新增資料**存放**區**]，然後選取 **[下一步]**

   ![新增資料存放區](media/databox-migration-add-datastore.png)

3. 在 [新增資料存放區] 嚮導的步驟1中，選取 [ **NFS** 類型]。

   ![新增資料存放區-類型](media/databox-migration-add-datastore-type.png)

4. 在嚮導的步驟2中，選取 **nfs 3** 作為 nfs 版本，然後選取 **[下一步]**。

   ![新增資料存放區-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在嚮導的步驟3中，指定資料存放區、路徑和伺服器的名稱。  您可以針對伺服器使用 Linux 虛擬機器的 IP 位址。  資料夾路徑的格式將會是 `/<folder>/<subfolder>/` 。

   ![新增資料存放區-NFS 設定](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在嚮導的步驟4中，選取您要在其中裝載資料存放區的 ESXi 主機，然後選取 **[下一步]**。  在叢集中，選取所有主機，以確保虛擬機器的遷移。

   ![新增資料存放區-選取主控制項](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在嚮導的步驟5中，檢查摘要，然後選取 **[完成]**。

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>從 NFS 資料存放區將虛擬機器和虛擬機器範本新增至清查

1. 從您的私人雲端 vCenter web UI，移至 [ **儲存體**]。  選取 Linux 虛擬機器 NFS 資料存放區，然後選取 [檔案 **]。**

    ![從 NFS 資料存放區選取檔案](media/databox-migration-datastore-select-files.png)

2. 選取包含虛擬機器或虛擬機器範本的資料夾。  在詳細資料窗格中，選取虛擬機器的 vmx 檔案或虛擬機器範本的 .vmtx 檔案。

3. 選取 [ **註冊 VM** ]，在您的私人雲端 vCenter 上註冊虛擬機器。

    ![註冊虛擬機器](media/databox-migration-datastore-register-vm.png)

4. 選取您要註冊虛擬機器的資料中心、資料夾和叢集/資源集區。

4. 針對所有虛擬機器和虛擬機器範本重複步驟3和4。

5. 移至包含 ISO 檔案的資料夾。  選取 ISO 檔案，然後選取 [ **複製到** ]，將檔案複製到 vSAN 資料存放區上的資料夾。

虛擬機器和虛擬機器範本現在已可在您的私人雲端 vCenter 上使用。 在您開啟這些虛擬機器之前，必須先將這些虛擬機器從 NFS 資料存放區移至 vSAN 資料存放區。 您可以使用 [ **儲存體 vMotion** ] 選項，並選取 vSAN 資料存放區做為虛擬機器的目標。

虛擬機器範本必須從您的 Linux 虛擬機器 NFS 資料存放區複製到 vSAN 資料存放區。

### <a name="clean-up-your-linux-virtual-machine"></a>清除您的 Linux 虛擬機器

將所有資料複製到您的私人雲端之後，您就可以從私人雲端移除 NFS 資料存放區：

1. 請確定所有虛擬機器和範本都已移動並複製到 vSAN 資料存放區。

2. 從所有 NFS 資料存放區的虛擬機器範本中移除清查。

3. 從您的私人雲端 vCenter 卸載 Linux 虛擬機器資料存放區。

4. 從 Azure 刪除虛擬機器和受控磁片。

5. 如果您不想要保留資料箱在儲存體帳戶中傳送的資料，請刪除 Azure 儲存體帳戶。  
    


## <a name="next-steps"></a>接下來的步驟

* 深入瞭解 [資料箱](../databox/data-box-overview.md)。
* 深入瞭解將 [工作負載遷移至私用雲端](migrate-workloads.md)的不同選項。
