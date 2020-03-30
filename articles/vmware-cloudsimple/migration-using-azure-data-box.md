---
title: Azure VMware 解決方案 - 使用 Azure 資料框進行遷移
description: 如何使用 Azure 資料框將資料批量遷移到 Azure VMware 解決方案。
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019752"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>使用 Azure 資料框將資料移轉到 Azure VMware 解決方案

Microsoft Azure 資料盒雲解決方案允許您以快速、廉價和可靠的方式向 Azure 發送 TB 資料。 透過向您寄送專屬的資料箱存放裝置，來加速安全的資料傳輸。 每個存放裝置的最大可用存儲容量為 80 TB，並由區域運營商傳輸到資料中心。 該裝置具有堅固的外殼，可在運輸過程中保護裝置與資料安全。

通過使用資料框，您可以將 VMware 資料批量遷移到私有雲。 來自本地 VMware vSphere 環境的資料通過網路檔案系統 （NFS） 協定複製到資料盒。 批量資料移轉涉及將虛擬機器、配置和相關資料的時間點副本保存到資料盒，然後手動將其傳送到 Azure。

在本文中，您將瞭解：

* 設置資料框。
* 通過 NFS 將資料從本地 VMware 環境複製到資料盒。
* 準備返回資料框。
* 準備用於複製到 Azure VMware 解決方案的 blob 資料。
* 將資料從 Azure 複製到私有雲。

## <a name="scenarios"></a>案例

在以下方案中使用資料框進行批量資料移轉：

* 將大量資料從本地遷移到 Azure VMware 解決方案。 此方法建立基線並在網路上同步差異。
* 遷移大量已關閉的虛擬機器（冷虛擬機器）。
* 遷移虛擬機器資料以設置開發和測試環境。
* 遷移大量虛擬機器範本、ISO 檔和虛擬機器磁片。

## <a name="before-you-begin"></a>開始之前

* 通過 Azure 門戶檢查先決條件並[訂購資料框](../databox/data-box-deploy-ordered.md)。 在訂購過程中，必須選擇啟用 Blob 存儲的存儲帳戶。 收到 Data Box 設備後，將其連接到本地網路，然後使用 vSphere 管理網路可訪問的 IP 位址[設置設備](../databox/data-box-deploy-set-up.md)。

* 在預配 Azure VMware 解決方案的同一區域中創建虛擬網路和存儲帳戶。

* 使用[ExpressRoute 按照將 Azure 虛擬網路連接到雲簡單](virtual-network-connection.md)中的步驟，從私有雲創建[Azure 虛擬網路連接](cloudsimple-azure-network-connection.md)到虛擬網路，在虛擬網路創建存儲帳戶。

## <a name="set-up-data-box-for-nfs"></a>為 NFS 設置資料框

按照[教程"電纜](../databox/data-box-deploy-set-up.md)"部分"連接到設備"部分的步驟連接到資料盒本地 Web UI。  配置資料框以允許訪問 NFS 用戶端：

1. 在本地 Web UI 中，轉到 **"連接"和"複製**"頁。 在**NFS 設置**下，選擇**NFS 用戶端訪問**。 

    ![設定 NFS 用戶端存取 1](media/nfs-client-access.png)

2. 輸入 VMware ESXi 主機的 IP 位址，然後選擇 **"添加**"。 您可以通過重複此步驟來配置 vSphere 群集中所有主機的訪問。 選取 [確定]****。

    ![設定 NFS 用戶端存取 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 不能將檔直接複製到存儲帳戶中的*根*資料夾。

在區塊 Blob 與分頁 Blob 共用底下，第一層實體是容器，而第二層實體是 Blob。 在 Azure 檔的共用下，第一級實體為共用，第二級實體為檔。

下表顯示您資料箱上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案       |<li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 塊 blob 複製 VMware 資料。

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>將 NFS 共用作為資料存儲裝載到本地 vCenter 群集中並複製資料

資料框中的 NFS 共用必須作為資料存儲裝載到本地 vCenter 群集或 VMware ESXi 主機上，以便將資料複製到 NFS 資料存儲：

1. 登錄到本地 vCenter 伺服器。

2. 按右鍵 **"資料中心**"，選擇 **"存儲**"，選擇 **"新資料存儲**"，然後選擇 **"下一步**"。

   ![添加新資料存儲](media/databox-migration-add-datastore.png)

3. 在"添加資料存儲"嚮導的步驟 1 中，在**類型**下選擇**NFS。**

   ![添加新資料存儲 - 類型](media/databox-migration-add-datastore-type.png)

4. 在嚮導的步驟 2 中，選擇**NFS 3**作為 NFS 版本，然後選擇 **"下一步**"。

   ![添加新資料存儲 - NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在嚮導的步驟 3 中，指定資料存儲、路徑和伺服器的名稱。 您可以將資料框的 IP 位址用於伺服器。 資料夾路徑將以格式`/<StorageAccountName_BlockBlob>/<ContainerName>/`表示。

   ![添加新資料存儲 - NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在嚮導的步驟 4 中，選擇要裝入資料存儲的 ESXi 主機，然後選擇 **"下一步**"。  在群集中，選擇所有主機以確保虛擬機器的遷移。

   ![添加新資料存儲 - 選擇主機](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在嚮導的步驟 5 中，查看摘要並選擇 **"完成**"。

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>將資料複製到資料盒 NFS 資料存儲

虛擬機器可以遷移或克隆到新的資料存儲。  可以使用**存儲 vMotion**選項將要遷移的任何未使用的虛擬機器遷移到資料盒 NFS 資料存儲。 活動虛擬機器可以克隆到資料盒 NFS 資料存儲。

* 標識並列出可**移動**的虛擬機器。
* 標識並列出必須**克隆的**虛擬機器。

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>將虛擬機器移動到資料盒資料存儲

1. 按右鍵要移動到資料框資料預存虛擬機器，然後選擇 **"遷移**"。

    ![遷移虛擬機器](media/databox-migration-vm-migrate.png)

2. 選擇**僅更改**遷移類型的存儲，然後選擇 **"下一步**"。

    ![僅遷移虛擬機器 - 僅存儲](media/databox-migration-vm-migrate-change-storage.png)

3. 選擇 **"資料箱-資料存儲**"作為目標，然後選擇 **"下一步**"。

    ![遷移虛擬機器 - 選擇資料存儲](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 查看資訊並選擇 **"完成**"。

5. 對於其他虛擬機器，重複步驟 1 到 4。

> [!TIP]
> 您可以選擇多個處於相同電源狀態（已打開或關閉）的虛擬機器，並批量遷移它們。

虛擬機器將從資料盒遷移到 NFS 資料存儲。 遷移所有虛擬機器後，可以關閉（關閉）活動虛擬機器，為將資料移轉到 Azure VMware 解決方案做準備。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>將虛擬機器或虛擬機器範本克隆到資料盒資料存儲

1. 按右鍵要克隆的虛擬機器或虛擬機器範本。 選擇**克隆** > **到虛擬機器**。

    ![虛擬機器克隆](media/databox-migration-vm-clone.png)

2. 為克隆虛擬機器或虛擬機器範本選擇名稱。

3. 選擇要放置克隆物件的資料夾，然後選擇 **"下一步**"。

4. 選擇要放置克隆物件的群集或資源池，然後選擇 **"下一步**"。

5. 選擇 **"資料箱-資料存儲**"作為存儲位置，然後選擇 **"下一步**"。

    ![虛擬機器克隆 - 選擇資料存儲](media/databox-migration-vm-clone-select-datastore.png)

6. 如果要為克隆物件自訂任何選項，請選擇自訂選項，然後選擇 **"下一步**"。

7. 查看配置並選擇 **"完成**"。

8. 對於其他虛擬機器或虛擬機器範本，重複步驟 1 到 7。

虛擬機器將從資料盒克隆並存儲在 NFS 資料存儲中。 克隆虛擬機器後，請確保關閉虛擬機器，以便將資料移轉到 Azure VMware 解決方案。

### <a name="copy-iso-files-to-the-data-box-datastore"></a>將 ISO 檔案複製到資料盒資料存儲

1. 從本地 vCenter Web UI 轉到**存儲**。  選擇 **"資料箱-資料存儲"，** 然後選擇 **"檔**"。 創建新資料夾以存儲 ISO 檔。

    ![複製 ISO - 創建新資料夾](media/databox-migration-create-folder.png)

2. 提供將存儲 ISO 檔的資料夾的名稱。

3. 按兩下新創建的資料夾以將其打開。

4. 選擇 **"上傳檔**"，然後選擇要上載的 ISO 檔。
    
    ![複製 ISO - 上傳檔](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果本地資料存儲中已有 ISO 檔，則可以選擇要將檔案複製**的檔並複製以**將檔案複製到資料盒 NFS 資料存儲。


## <a name="prepare-data-box-for-return"></a>準備返回的資料框

將所有虛擬機器資料、虛擬機器範本資料以及任何 ISO 檔案複製到資料盒 NFS 資料存儲後，可以將資料存儲與 vCenter 斷開連接。 在斷開資料存儲的連接之前，必須從庫存中刪除所有虛擬機器和虛擬機器範本。

### <a name="remove-objects-from-inventory"></a>從清單中刪除物件

1. 從本地 vCenter Web UI 轉到**存儲**。 選擇**資料箱-資料存儲**，然後選擇**VM。**

    ![從庫存中刪除虛擬機器 - 已關閉](media/databox-migration-select-databox-vm.png)

2. 確保所有虛擬機器都已關閉。

3. 選擇所有虛擬機器，按右鍵，然後選擇從**庫存中刪除**。

    ![從庫存中刪除虛擬機器](media/databox-migration-remove-vm-from-inventory.png)

4. 在**資料夾中**選擇 VM 範本，然後重複步驟 3。

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>從 vCenter 中刪除資料框 NFS 資料存儲

在準備返回之前，必須斷開資料盒 NFS 資料存儲與 VMware ESXi 主機的連接。

1. 從本地 vCenter Web UI 轉到**存儲**。

2. 按右鍵 **"資料箱-資料存儲"，** 然後選擇 **"卸載資料存儲**"。

    ![卸載資料框資料存儲](media/databox-migration-unmount-datastore.png)

3. 選擇裝入資料存儲的所有 ESXi 主機，然後選擇 **"確定**"。

    ![卸載資料框資料存儲 - 選擇主機](media/databox-migration-unmount-datastore-select-hosts.png)

4. 查看並接受任何警告，並選擇 **"確定**"。

### <a name="prepare-data-box-for-return-and-then-return-it"></a>準備資料框進行返回，然後返回它

按照"返回 Azure 資料框"一文中概述的步驟[操作，並驗證將資料上載到 Azure](../databox/data-box-deploy-picked-up.md)以返回資料框。 檢查資料副本到 Azure 存儲帳戶的狀態。 狀態顯示為已完成後，可以驗證 Azure 存儲帳戶中的資料。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>將資料從 Azure 存儲複製到 Azure VMware 解決方案

資料框的訂單狀態顯示為已完成後，複製到資料框設備的資料將在 Azure 存儲帳戶上可用。 資料現在可以複製到 Azure VMware 解決方案。 存儲帳戶中的資料必須使用 NFS 協定複製到私有雲的 vSAN 資料存儲。 

首先，使用**AzCopy**將 Blob 存儲資料複製到 Azure 中的 Linux 虛擬機器上的託管磁片。 通過 NFS 使託管磁片可用，將 NFS 共用裝載為私有雲上的資料存儲，然後複製資料。 此方法可更快地將資料複製到私有雲。

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>使用 Linux 虛擬機器和託管磁片將資料複製到私有雲，然後匯出為 NFS 共用

1. 在創建存儲帳戶並具有與私有雲的 Azure 虛擬網路連接的同一區域中的 Azure 中創建[Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)。

2. 創建存儲容量大於 blob 資料量的託管磁片，並將其[附加到 Linux 虛擬機器](../virtual-machines/linux/attach-disk-portal.md)。  如果 Blob 資料量大於可用最大託管磁片的容量，則必須以多個步驟或使用多個託管磁碟複製資料。

3. 連接到 Linux 虛擬機器並裝載託管磁片。

4. [在 Linux 虛擬機器上安裝 AzCopy。](../storage/common/storage-use-azcopy-v10.md)

5. 使用 AzCopy 將資料從 Azure Blob 存儲下載到託管磁片上。  命令語法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  替換為`<storage-account-name>`Azure 存儲帳戶名稱以及`<container-name>`保存通過資料框複製資料的容器。

6. 在 Linux 虛擬機器上安裝 NFS 伺服器：

    - 在 Ubuntu/Debian 分佈`sudo apt install nfs-kernel-server`： 上。
    - 在企業 Linux 發行`sudo yum install nfs-utils`版上： .

7. 更改複製 Azure Blob 存儲中的資料的託管磁片上的資料夾的許可權。  更改要作為 NFS 共用匯出的所有資料夾的許可權。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 通過編輯檔為用戶端 IP 位址分配訪問 NFS 共用`/etc/exports`的許可權。

    ```bash
    sudo vi /etc/exports
    ```
    
    為私有雲的每個 ESXi 主機 IP 在檔中輸入以下行。  如果要為多個資料夾創建共用，則添加所有資料夾。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 使用`sudo exportfs -a`命令匯出 NFS 共用。

10. 使用`sudo systemctl restart nfs-kernel-server`命令重新開機 NFS 內核伺服器。


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>將 Linux 虛擬機器 NFS 共用作為資料存儲裝載到私有雲 vCenter 群集上，然後複製資料

Linux 虛擬機器中的 NFS 共用必須作為資料存儲裝載到私有雲 vCenter 群集上。 裝載後，可以從 NFS 資料存儲將資料複製到私有雲 vSAN 資料存儲。

1. 登錄到私有雲 vCenter 伺服器。

2. 按右鍵 **"資料中心**"，選擇 **"存儲**"，選擇 **"新資料存儲**"，然後選擇 **"下一步**"。

   ![添加新資料存儲](media/databox-migration-add-datastore.png)

3. 在"添加資料存儲"嚮導的步驟 1 中，選擇**NFS**類型。

   ![添加新資料存儲 - 類型](media/databox-migration-add-datastore-type.png)

4. 在嚮導的步驟 2 中，選擇**NFS 3**作為 NFS 版本，然後選擇 **"下一步**"。

   ![添加新資料存儲 - NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在嚮導的步驟 3 中，指定資料存儲、路徑和伺服器的名稱。  您可以將 Linux 虛擬機器的 IP 位址用於伺服器。  資料夾路徑將以格式`/<folder>/<subfolder>/`表示。

   ![添加新資料存儲 - NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在嚮導的步驟 4 中，選擇要裝入資料存儲的 ESXi 主機，然後選擇 **"下一步**"。  在群集中，選擇所有主機以確保虛擬機器的遷移。

   ![添加新資料存儲 - 選擇主機](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在嚮導的步驟 5 中，查看摘要，然後選擇 **"完成**"。

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>將 NFS 資料存儲中的虛擬機器和虛擬機器範本添加到庫存中

1. 從私有雲 vCenter Web UI 轉到**存儲**。  選擇 Linux 虛擬機器 NFS 資料存儲，然後選擇**檔**。

    ![從 NFS 資料存儲中選擇檔](media/databox-migration-datastore-select-files.png)

2. 選擇包含虛擬機器或虛擬機器範本的資料夾。  在詳細資訊窗格中，為虛擬機器選擇 .vmx 檔，為虛擬機器範本選擇 .vmx 檔。

3. 選擇 **"註冊 VM"** 以在私有雲 vCenter 上註冊虛擬機器。

    ![註冊虛擬機器](media/databox-migration-datastore-register-vm.png)

4. 選擇要註冊虛擬機器的資料中心、資料夾和群集/資源池。

4. 對所有虛擬機器和虛擬機器範本重複步驟 3 和 4。

5. 轉到包含 ISO 檔的資料夾。  選擇 ISO 檔，然後選擇 **"複製"以**將檔案複製到 vSAN 資料存儲上的資料夾。

虛擬機器和虛擬機器範本現在可在私有雲 vCenter 上使用。 在打開這些虛擬機器之前，必須將這些虛擬機器從 NFS 資料存儲移動到 vSAN 資料存儲。 您可以使用**存儲 vMotion**選項，並選擇 vSAN 資料存儲作為虛擬機器的目標。

虛擬機器範本必須從 Linux 虛擬機器 NFS 資料存儲克隆到 vSAN 資料存儲。

### <a name="clean-up-your-linux-virtual-machine"></a>清理 Linux 虛擬機器

將所有資料複製到私有雲後，可以從私有雲中刪除 NFS 資料存儲：

1. 確保所有虛擬機器和範本都移動並克隆到 vSAN 資料存儲。

2. 從清單中刪除從 NFS 資料存儲中的所有虛擬機器範本。

3. 從私有雲 vCenter 卸載 Linux 虛擬機器資料存儲。

4. 從 Azure 中刪除虛擬機器和託管磁片。

5. 如果不想將資料框傳輸的資料保留在存儲帳戶中，請刪除 Azure 存儲帳戶。  
    


## <a name="next-steps"></a>後續步驟

* 瞭解有關[資料框](../databox/data-box-overview.md)的更多。
* 詳細瞭解將[工作負載遷移到私有雲](migrate-workloads.md)的不同選項。
