---
title: 教學課程：從 Vhd 複製到受控磁片
titleSuffix: Azure Data Box
description: 了解如何將 VHD 的資料從內部部署 VM 工作負載複製到 Azure 資料箱
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 8f076deaafd938dc93800cf351bf471cead5f009
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380196"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>教學課程：使用資料箱在 Azure 中將資料匯入為受控磁片

本教學課程示範如何使用 Azure 資料箱，將內部部署 VHD 移轉到 Azure 中的受控磁碟。 來自內部部署 VM 的 VHD 會複製到資料箱以作為分頁 Blob，並上傳至 Azure 以作為受控磁碟。 這些受控磁碟接著可以連結到 Azure VM。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢閱必要條件
> * 連線至資料箱
> * 將資料複製到資料箱


## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已完成[教學課程：設定 Azure 資料箱](data-box-deploy-set-up.md)。
2. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]。
3. 您已連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，請使用 1 GbE 資料連結，但是複製速度會受到影響。
4. 您已檢閱：

    - [Azure 物件大小限制中所支援的受控磁碟大小](data-box-limits.md#azure-object-size-limits)。
    - [Azure 受控磁碟簡介](/azure/virtual-machines/windows/managed-disks-overview)。 

## <a name="connect-to-data-box"></a>連線至資料箱

根據指定的資源群組，資料箱會針對每個相關聯的資源群組建立一個共用。 例如，如果 `mydbmdrg1` 和 `mydbmdrg2` 是在下訂單時所建立的，就會建立下列共用：

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

在每個共用內，會建立下列三個對應到您儲存體帳戶中容器的資料夾。

- 進階 SSD
- 標準 HDD
- 標準 SSD

下表顯示您資料箱上共用的 UNC 路徑。
 
|        連線通訊協定           |             共用的 UNC 路徑                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

根據您是否使用 SMB 或 NFS 來連線到資料箱共用而定，連線的步驟會有所不同。

> [!NOTE]
> 此功能不支援透過 REST 連線。

### <a name="connect-to-data-box-via-smb"></a>透過 SMB 連線至資料箱

如果使用 Windows Server 主機電腦，請依照下列步驟來連線至資料箱。

1. 第一個步驟是驗證並啟動工作階段。 移至 [連線並複製]。 按一下 [取得認證]，以取得與您資源群組相關聯之共用的存取認證。 您也可以在 Azure 入口網站，從 [裝置詳細資料] 中取得存取認證。

    > [!NOTE]
    > 適用於受控磁碟之所有共用的認證均完全相同。

    ![取得共用認證 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. 從 [存取共用及複製資料] 對話方塊中，複製共用的**使用者名稱**和**密碼**。 按一下 [確定]。
    
    ![取得共用認證 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. 若要從主機電腦存取與您的資源 (在下列範例中為 *mydbmdrg1*) 相關聯的共用，請開啟命令視窗。 在命令提示字元中，輸入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    您在此範例中的 UNC 共用路徑如下所示：

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. 出現提示時，請輸入共用的密碼。 下列範例說明如何透過前述命令連線至共用。

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. 按 Windows + R。在 [執行] 視窗中，指定 `\\<device IP address>\<ShareName>`。 按一下 [確定] 以開啟檔案總管。
    
    ![透過檔案總管 2 連線至共用](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    您現在應該會在每個共用內看到下列預先建立的資料夾。
    
    ![透過檔案總管 2 連線至共用](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>透過 NFS 連線至資料箱

如果您使用 Linux 主機電腦，請執行下列步驟設定資料箱，以允許 NFS 用戶端的存取。

1. 針對允許存取共用的用戶端提供其 IP 位址。 在本機 Web UI 中，移至 [連線並複製] 頁面。 在 [NFS 設定] 下方，按一下 [NFS 用戶端存取]。

    ![設定 NFS 用戶端存取 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. 提供 NFS 用戶端的 IP 位址，然後按一下 [新增]。 您可以重複此步驟，以設定多個 NFS 用戶端的存取。 按一下 [確定]。

    ![設定 NFS 用戶端存取 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. 確定 Linux 主機電腦已安裝NFS 用戶端的[支援版本](data-box-system-requirements.md)。 針對您的 Linux 發行版本，請使用特定版本。

3. 安裝 NFS 用戶端之後，請使用下列命令將 NFS 共用掛接在您的資料箱裝置上：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    下列範例說明如何透過 NFS 連線至資料箱共用。 資料箱裝置 IP 為 `169.254.250.200`，共用 `mydbmdrg1_MDisk` 會掛接在 ubuntuVM 上，掛接點為 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>將資料複製到資料箱

當您連線至資料伺服器之後，下一個步驟是複製資料。 VHD 檔案會複製到暫存的儲存體帳戶以作為分頁 Blob。 分頁 Blob 接著會轉換為受控磁碟並移至資源群組。

開始複製資料之前，請先檢閱下列考量：

- 一律將 VHD 複製到其中一個預先建立的資料夾。 如果您在這些資料夾外部或您所建立的資料夾中複製 VHD，就會將 VHD 上傳至 Azure 儲存體帳戶以作為分頁 Blob，而非受控磁碟。
- 只能上傳固定的 VHD 來建立受控磁碟。 不支援 VHDX 檔案或動態和差異 VHD。
- 在所有預先建立的資料夾之間，您在一個資源群組中只能有一個具指定名稱的受控磁碟。 這表示上傳至預先建立資料夾的 VHD 應具備唯一名稱。 請確定指定的名稱不會與資源群組中現有的受控磁碟相符。
- 檢閱 [Azure 物件大小限制](data-box-limits.md#azure-object-size-limits)中的受控磁碟限制。

根據您要透過 SMB 還是 NFS 連線，您可以使用：

- [透過 SMB 複製資料](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [透過 NFS 複製資料](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

等待複製作業完成。 請確定複製作業已完成且未出現任何錯誤，然後再進行下一個步驟。

![[連線並複製] 頁面上沒有錯誤](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

如果複製流程中發生錯誤，請從 [連線並複製] 頁面中下載記錄。

- 如果您複製的檔案並非 512 位元組規格，則不會將該檔案以分頁 Blob 形式上傳至您的暫存儲存體帳戶。 您將在記錄中看到錯誤。 移除檔案，並複製 512 位元組規格的檔案。

- 如果您複製了具完整名稱的 VHDX (不支援這些檔案)，則將在記錄中看到錯誤。

    ![來自 [連線並複製] 頁面之記錄中的錯誤](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    繼續下一個步驟之前，請先解決錯誤。

為了確保資料完整性，在複製資料時會計算內嵌的總和檢查碼。 複製完成後，請確認您的裝置上已使用的空間和可用空間。
    
![確認儀表板上的可用空間和已使用的空間](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

複製作業完成後，您就可以移至 [準備寄送]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * 檢閱必要條件
> * 連線至資料箱
> * 將資料複製到資料箱


請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄送給 Microsoft](./data-box-deploy-picked-up.md)

