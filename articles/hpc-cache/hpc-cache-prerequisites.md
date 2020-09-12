---
title: Azure HPC Cache 必要條件
description: 使用 Azure HPC Cache 的必要條件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/03/2020
ms.author: v-erkel
ms.openlocfilehash: 7731839d23ec49ddfee814cc6b2f6b3459372f03
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613998"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC Cache 的必要條件

使用 Azure 入口網站建立新的 Azure HPC Cache 之前，請確定您的環境符合這些需求。

## <a name="video-overviews"></a>影片總覽

觀看這些影片，以快速瞭解系統的元件，以及需要如何共同作業。

 (按一下影片影像或要監看的連結。 ) 

* [運作方式](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) -說明 Azure HPC Cache 如何與儲存體和用戶端互動

  [![影片縮圖影像： Azure HPC Cache：其運作方式 (按一下以流覽影片頁面) ](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [必要條件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) -描述 NAS 儲存體、Azure Blob 儲存體、網路存取及用戶端存取的需求

  [![影片縮圖影像： Azure HPC Cache：必要條件 (按一下以流覽影片頁面) ](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

閱讀本文的其餘部分，以取得特定建議。

## <a name="azure-subscription"></a>Azure 訂用帳戶

建議使用付費訂用帳戶。
<!-- 
> [!NOTE]
> The Azure HPC Cache team must add your subscription to the access list before it can be used to create a cache instance. This procedure helps ensure that each customer gets high-quality responsiveness from their caches. Fill out [this form](https://aka.ms/onboard-hpc-cache) to request access.  -->

## <a name="network-infrastructure"></a>網路基礎結構

您應該先設定兩個網路相關的必要條件，才能使用您的快取：

* Azure HPC Cache 實例的專用子網
* DNS 支援，讓快取可以存取儲存體和其他資源

### <a name="cache-subnet"></a>快取子網

Azure HPC Cache 需要具有下列品質的專用子網：

* 子網必須至少有64個可用的 IP 位址。
* 子網無法裝載任何其他 Vm，即使是用戶端電腦等相關服務也是如此。
* 如果您使用多個 Azure HPC Cache 實例，每一個實例都需要它自己的子網。

最佳做法是為每個快取建立新的子網。 您可以建立新的虛擬網路和子網，作為建立快取的一部分。

### <a name="dns-access"></a>DNS 存取

快取需要 DNS 才能存取其虛擬網路之外的資源。 視您使用的資源而定，您可能需要設定自訂的 DNS 伺服器，並設定該伺服器與 Azure DNS 伺服器之間的轉送：

* 若要存取 Azure Blob 儲存體端點和其他內部資源，您需要以 Azure 為基礎的 DNS 伺服器。
* 若要存取內部部署儲存體，您需要設定可解析儲存體主機名稱的自訂 DNS 伺服器。

如果您只需要存取 Blob 儲存體，您可以使用 Azure 提供的預設 DNS 伺服器做為您的快取。 但是，如果您需要存取其他資源，您應該建立自訂的 DNS 伺服器，並將其設定為將任何 Azure 特定的解析要求轉送至 Azure DNS 伺服器。

您也可以使用簡單的 DNS 伺服器，在所有可用的快取掛接點之間進行用戶端連線的負載平衡。

深入瞭解 azure 虛擬網路中 [資源的名稱解析中的](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)azure 虛擬網路和 DNS 伺服器設定。

## <a name="permissions"></a>權限

請先檢查這些許可權相關的必要條件，再開始建立您的快取。

* 快取實例必須能夠 (Nic) 建立虛擬網路介面。 建立快取的使用者必須在訂用帳戶中有足夠的許可權，才能建立 Nic。

* 如果使用 Blob 儲存體，Azure HPC Cache 需要授權才能存取您的儲存體帳戶。 使用角色型存取控制 (RBAC) 將您 Blob 儲存體的存取權授與快取。 需要兩個角色：儲存體帳戶參與者和儲存體 Blob 資料參與者。

  遵循 [新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 中的指示來新增角色。

## <a name="storage-infrastructure"></a>儲存體基礎結構

快取支援 Azure Blob 容器或 NFS 硬體儲存體匯出。 建立快取之後，請新增儲存體目標。

每個儲存體類型都有特定的必要條件。

### <a name="blob-storage-requirements"></a>Blob 儲存體需求

如果您想要將 Azure Blob 儲存體與您的快取搭配使用，您需要相容的儲存體帳戶，以及使用 Azure HPC Cache 格式化資料（如 [將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)中所述）的空白 Blob 容器或容器。

嘗試新增儲存體目標之前，請先建立帳戶。 您可以在新增目標時建立新的容器。

若要建立相容的儲存體帳戶，請使用下列設定：

* 效能： **標準**
* 帳戶種類： **StorageV2 (一般用途 v2) **
* 複寫： **本機冗余儲存體 (LRS) **
* 存取層 (預設) ： **熱**

在與您的快取相同的位置中使用儲存體帳戶是很好的作法。

您也必須將 Azure 儲存體帳戶的存取權授與您的 Azure 儲存體帳戶，如上面的 [許可權](#permissions)中所述。 依照「 [新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 」中的程式，為快取提供所需的存取角色。 如果您不是儲存體帳戶擁有者，請讓擁有者執行此步驟。

### <a name="nfs-storage-requirements"></a>NFS 儲存體需求
<!-- linked from configuration.md -->

如果使用 NFS 儲存體系統 (例如，內部部署硬體 NAS 系統) ，請確定它符合這些需求。 您可能需要與存放裝置系統 (或資料中心) 的網路系統管理員或防火牆管理員合作，以驗證這些設定。

> [!NOTE]
> 如果快取對 NFS 儲存體系統沒有足夠的存取權，則建立儲存體目標將會失敗。

[疑難排解 NAS 設定和 NFS 儲存體目標問題](troubleshoot-nas.md)的詳細資訊。

* **網路連線能力：** Azure HPC Cache 需要在快取子網和 NFS 系統的資料中心之間進行高頻寬的網路存取。 建議使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或類似的存取。 如果使用 VPN，您可能需要將它設定為在1350上設定 TCP MSS，以確保不會封鎖大型封包。 如需其他協助疑難排解 VPN 設定的詳細說明，請參閱 [vpn 封包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 。

* **埠存取：** 快取需要存取儲存體系統上的特定 TCP/UDP 埠。 不同類型的儲存體具有不同的埠需求。

  若要檢查儲存系統的設定，請遵循此程式。

  * 發出 `rpcinfo` 命令給您的儲存系統以檢查所需的埠。 下列命令會列出埠，並在資料表中將相關的結果格式化。  (使用系統的 IP 位址來取代 *<storage_IP>* 詞彙。 ) 

    您可以從已安裝 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果您在叢集子網內使用用戶端，它也可以協助驗證子網與儲存體系統之間的連線能力。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  請確定查詢所傳回的所有埠都 ``rpcinfo`` 允許來自 Azure HPC Cache 子網的不受限制流量。

  * 如果您無法使用 `rpcinfo` 命令，請確定這些常用的埠允許連入和連出流量：

    | 通訊協定 | Port  | 服務  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    某些系統使用這些服務的不同埠號碼-請查閱您的儲存體系統檔，以確定。

  * 檢查防火牆設定，確定它們允許所有必要端口上的流量。 請務必檢查 Azure 中使用的防火牆，以及資料中心內的內部部署防火牆。

* **目錄存取：** 在 `showmount` 儲存體系統上啟用命令。 Azure HPC Cache 使用此命令來檢查您的儲存體目標設定是否指向有效的匯出，也可確保多個裝載不會存取相同的子目錄， (有檔案衝突) 的風險。

  > [!NOTE]
  > 如果您的 NFS 儲存體系統使用 NetApp 的 ONTAP 9.2 作業系統，請不要**啟用 `showmount` **。 [請聯絡 Microsoft 服務及支援](hpc-cache-support-ticket.md) 以取得協助。

  深入瞭解 NFS 儲存體目標 [疑難排解文章](troubleshoot-nas.md#enable-export-listing)中的目錄清單存取。

* **根目錄存取** (讀取/寫入) ：快取會以使用者識別碼0的形式連接到後端系統。 在您的儲存體系統上檢查這些設定：
  
  * 啟用 `no_root_squash`。 此選項可確保遠端根使用者可以存取 root 所擁有的檔案。

  * 核取 [匯出原則]，確定它們不包含從快取子網進行根存取的限制。

  * 如果您的儲存體具有任何屬於另一個匯出子目錄的匯出，請確定快取具有路徑最低區段的根目錄存取權。 如需詳細資訊，請參閱 NFS 儲存體目標疑難排解文章中 [目錄路徑的根目錄存取](troubleshoot-nas.md#allow-root-access-on-directory-paths) 。

* NFS 後端存放裝置必須是相容的硬體/軟體平臺。 請聯絡 Azure HPC Cache 小組以取得詳細資料。

## <a name="set-up-azure-cli-access-optional"></a>設定 Azure CLI 存取 (選擇性) 

如果您想要從 Azure 命令列介面建立或管理 Azure HPC Cache (Azure CLI) ，則必須安裝 CLI 軟體和 HPC Cache 擴充功能。 依照 [Azure HPC Cache 的設定 Azure CLI](az-cli-prerequisites.md)中的指示進行。

## <a name="next-steps"></a>接下來的步驟

* 從 Azure 入口網站[建立 Azure HPC Cache 實例](hpc-cache-create.md)
