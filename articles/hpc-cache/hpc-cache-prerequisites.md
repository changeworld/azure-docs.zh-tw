---
title: Azure HPC 快取必要條件
description: 使用 Azure HPC 快取的必要條件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/24/2020
ms.author: v-erkel
ms.openlocfilehash: 1ead2a34b3617093fcbbb63d053f223fc96d698d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096309"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 快取的必要條件

在使用 Azure 入口網站建立新的 Azure HPC 快取之前，請確定您的環境符合這些需求。

## <a name="video-overviews"></a>影片概覽

觀看這些影片以快速瞭解系統的元件，以及它們需要如何搭配使用。

（按一下影片影像或要監看的連結。）

* [運作方式](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)-說明 Azure HPC 快取如何與儲存體和用戶端互動

  [![影片縮圖影像： Azure HPC 快取：運作方式（按一下以流覽影片頁面）](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [必要條件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)-描述 NAS 儲存體、Azure Blob 儲存體、網路存取和用戶端存取的需求

  [![影片縮圖影像： Azure HPC 快取：必要條件（按一下以流覽影片頁面）](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

如需特定建議，請閱讀本文的其餘部分。

## <a name="azure-subscription"></a>Azure 訂用帳戶

建議使用付費訂用帳戶。

> [!NOTE]
> Azure HPC 快取小組必須先將您的訂用帳戶新增至存取清單，才能用來建立快取實例。 此程式有助於確保每個客戶從其快取取得高品質的回應能力。 填寫[此表單](https://aka.ms/onboard-hpc-cache)以要求存取權。

## <a name="network-infrastructure"></a>網路基礎結構

您必須先設定兩個網路相關的必要條件，才能使用您的快取：

* Azure HPC Cache 實例的專用子網
* DNS 支援，讓快取可以存取儲存體和其他資源

### <a name="cache-subnet"></a>快取子網

Azure HPC 快取需要具有下列品質的專用子網：

* 子網必須至少有64個可用的 IP 位址。
* 子網無法裝載任何其他 Vm，即使是適用于用戶端電腦的相關服務也一樣。
* 如果您使用多個 Azure HPC 快取實例，每個實例都需要自己的子網。

最佳做法是為每個快取建立新的子網。 您可以建立新的虛擬網路和子網，做為建立快取的一部分。

### <a name="dns-access"></a>DNS 存取

快取需要 DNS 才能存取其虛擬網路以外的資源。 視您使用的資源而定，您可能需要設定自訂的 DNS 伺服器，並設定該伺服器與 Azure DNS 伺服器之間的轉送：

* 若要存取 Azure Blob 儲存體端點和其他內部資源，您需要以 Azure 為基礎的 DNS 伺服器。
* 若要存取內部部署存放裝置，您必須設定可解析儲存體主機名稱的自訂 DNS 伺服器。

如果您只需要存取 Blob 儲存體，您可以針對您的快取使用預設的 Azure 提供的 DNS 伺服器。 不過，如果您需要存取其他資源，您應該建立自訂 DNS 伺服器，並將其設定為將任何 Azure 特定的解析要求轉送至 Azure DNS 伺服器。

您也可以使用簡單的 DNS 伺服器，在所有可用的快取掛接點之間進行用戶端連線的負載平衡。

[針對 azure 虛擬網路中的資源](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)，深入瞭解 azure 虛擬網路和 DNS 伺服器設定的名稱解析。

## <a name="permissions"></a>權限

開始建立快取之前，請先檢查這些許可權相關的必要條件。

* 快取實例必須能夠建立虛擬網路介面（Nic）。 建立快取的使用者在訂用帳戶中必須具有足夠的許可權，才能建立 Nic。

* 如果使用 Blob 儲存體，Azure HPC Cache 需要授權才能存取您的儲存體帳戶。 使用角色型存取控制（RBAC），為您的 Blob 儲存體提供快取存取權。 需要兩個角色：儲存體帳戶參與者和儲存體 Blob 資料參與者。

  遵循[新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的指示來新增角色。

## <a name="storage-infrastructure"></a>儲存體基礎結構

快取支援 Azure Blob 容器或 NFS 硬體儲存體匯出。 建立快取之後，新增儲存體目標。

每種儲存體類型都有特定的必要條件。

### <a name="blob-storage-requirements"></a>Blob 儲存體需求

如果您想要在快取中使用 Azure Blob 儲存體，您需要相容的儲存體帳戶，以及使用 Azure HPC 快取格式化資料填入的空白 Blob 容器或容器，如[將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)中所述。

請先建立帳戶，再嘗試新增儲存體目標。 當您新增目標時，可以建立新的容器。

若要建立相容的儲存體帳戶，請使用下列設定：

* 效能：**標準**
* 帳戶種類： **StorageV2 （一般用途 v2）**
* 複寫：**本機多餘的儲存體（LRS）**
* 存取層（預設）：**熱**

最佳做法是在與快取相同的位置中使用儲存體帳戶。

您也必須授與快取應用程式對您 Azure 儲存體帳戶的存取權，如上面的[許可權](#permissions)中所述。 依照[新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的程式，為快取提供必要的存取角色。 如果您不是儲存體帳戶擁有者，請讓擁有者執行此步驟。

### <a name="nfs-storage-requirements"></a>NFS 儲存體需求
<!-- linked from configuration.md -->

如果使用 NFS 儲存體系統（例如，內部部署硬體 NAS 系統），請確定它符合這些需求。 您可能需要與您的儲存系統（或資料中心）的網路系統管理員或防火牆管理員合作，以確認這些設定。

> [!NOTE]
> 如果快取沒有足夠的 NFS 儲存體系統存取權，儲存體目標的建立將會失敗。

[疑難排解 NAS 設定和 NFS 儲存體目標問題](troubleshoot-nas.md)中包含的詳細資訊。

* **網路連線能力：** 在快取子網與 NFS 系統的資料中心之間，Azure HPC Cache 需要高頻寬的網路存取。 建議使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或類似的存取權。 如果使用 VPN，您可能需要將它設定為將 TCP MSS 固定在1350，以確保不會封鎖大型封包。 如需 VPN 設定的疑難排解詳細資訊，請參閱[vpn 封包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)。

* **埠存取：** 快取需要存取儲存系統上的特定 TCP/UDP 埠。 不同類型的存放裝置有不同的埠需求。

  若要檢查您的儲存系統設定，請遵循此程式。

  * 發出 `rpcinfo` 命令給您的儲存系統，以檢查所需的埠。 下列命令會列出埠，並將相關的結果格式化為資料表。 （使用您系統的 IP 位址來取代 *<storage_IP>* 期限）。

    您可以從已安裝 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果您使用叢集子網內的用戶端，它也可以協助驗證子網與儲存體系統之間的連線能力。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  請確定查詢所傳回的所有埠都 ``rpcinfo`` 允許來自 AZURE HPC 快取子網的不受限制流量。

  * 如果您無法使用此 `rpcinfo` 命令，請確定這些常用的埠允許輸入和輸出流量：

    | 通訊協定 | Port  | 服務  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    某些系統會針對這些服務使用不同的埠號碼-請參閱儲存體系統的檔以確定。

  * 檢查防火牆設定，確定它們允許所有這些必要端口上的流量。 請務必檢查 Azure 中所使用的防火牆，以及您資料中心內的內部部署防火牆。

* **目錄存取：**`showmount`在儲存系統上啟用命令。 Azure HPC 快取會使用此命令來檢查您的儲存體目標設定是否指向有效的匯出，同時確保多個掛接不會存取相同的子目錄（發生檔案衝突的風險）。

  > [!NOTE]
  > 如果您的 NFS 儲存體系統使用 NetApp 的 ONTAP 9.2 作業系統，**請勿啟用 `showmount` **。 [請洽詢 Microsoft 服務和支援](hpc-cache-support-ticket.md)以取得協助。

  若要深入瞭解目錄清單存取權，請參閱 NFS 儲存體目標[疑難排解一文](troubleshoot-nas.md#enable-export-listing)。

* **根存取**（讀取/寫入）：快取會連線到後端系統作為使用者識別碼0。 在您的儲存系統上檢查這些設定：
  
  * 啟用 `no_root_squash`。 此選項可確保遠端根使用者可以存取 root 所擁有的檔案。

  * 核取 [匯出原則]，確保它們不包含從快取子網的根存取限制。

  * 如果您的儲存體具有任何屬於另一個匯出之子目錄的匯出，請確定快取具有路徑最低區段的根目錄存取權。 如需詳細資訊，請參閱 NFS 儲存體目標疑難排解一文中[目錄路徑的根存取](troubleshoot-nas.md#allow-root-access-on-directory-paths)。

* NFS 後端存放裝置必須是相容的硬體/軟體平臺。 如需詳細資訊，請洽詢 Azure HPC 快取小組。

## <a name="set-up-azure-cli-access-optional"></a>設定 Azure CLI 存取（選擇性）

如果您想要從 Azure 命令列介面（Azure CLI）建立或管理 Azure HPC 快取，您必須安裝 CLI 軟體和 HPC 快取延伸模組。 依照[設定 AZURE HPC Cache 的 Azure CLI](az-cli-prerequisites.md)中的指示進行。

## <a name="next-steps"></a>後續步驟

* 從 Azure 入口網站[建立 AZURE HPC](hpc-cache-create.md)快取實例
