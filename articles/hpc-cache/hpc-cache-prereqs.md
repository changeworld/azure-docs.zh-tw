---
title: Azure HPC 快取先決條件
description: 使用 Azure HPC 快取的先決條件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 6da35cb60dc5f22be01ae25393bd62327db64867
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655651"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 快取的先決條件

在使用 Azure 門戶創建新的 Azure HPC 緩存之前,請確保環境滿足這些要求。

## <a name="azure-subscription"></a>Azure 訂用帳戶

建議付費訂閱。

> [!NOTE]
> 在 GA 發表的頭幾個月中,Azure HPC 緩存團隊必須將訂閱添加到訪問清單中,然後才能用於創建緩存實例。 此過程有助於確保每個客戶從他們的緩存中獲得高品質的回應。 填寫[此表單](https://aka.ms/onboard-hpc-cache)以請求訪問。

## <a name="network-infrastructure"></a>網路基礎結構

在使用快取之前,應設定兩個與網路相關的先決條件:

* Azure HPC 快取實體的專用子網路
* DNS 支援,以便快取記憶體的記憶體

### <a name="cache-subnet"></a>快取子網

Azure HPC 快取需要具有以下特性的專用子網:

* 子網必須至少有 64 個可用的 IP 位址。
* 子網無法承載任何其他 VM,即使對於用戶端電腦等相關服務也是如此。
* 如果使用多個 Azure HPC 緩存實例,則每個實例都需要其自己的子網。

最佳做法是為每個緩存創建新子網。 作為創建快取的一部分,您可以創建新的虛擬網路和子網路。

### <a name="dns-access"></a>DNS 存取

緩存需要 DNS 訪問其虛擬網路外部的資源。 根據所使用的資源,您可能需要設定自訂 DNS 伺服器並設定該伺服器和 Azure DNS 伺服器之間的轉發:

* 要造訪 Azure Blob 儲存終結點和其他內部資源,需要基於 Azure 的 DNS 伺服器。
* 要存取本地存儲,您需要設定可解析儲存主機名的自訂 DNS 伺服器。

如果只需要存取 Blob 儲存,則可以將預設的 Azure 提供的 DNS 伺服器用於緩存。 但是,如果需要訪問其他資源,則應創建自定義 DNS 伺服器並將其配置為將任何特定於 Azure 的解析請求轉發到 Azure DNS 伺服器。

一個簡單的 DNS 伺服器還可用於在所有可用的快取裝置載入點之間載入平衡客戶端連接。

在[Azure 虛擬網路中的資源名稱解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)中瞭解有關 Azure 虛擬網路和 DNS 伺服器配置的更多資訊。

## <a name="permissions"></a>權限

在開始創建緩存之前,請檢查這些與許可權相關的先決條件。

* 快取實體需要能夠建立虛擬網路介面 (NIC)。 創建快取的用戶必須在訂閱中具有創建 NIC 的足夠許可權。

* 如果使用 Blob 儲存,Azure HPC 快取需要授權才能存取儲存帳戶。 使用基於角色的存取控制 (RBAC) 為快取提供對 Blob 儲存的存取許可權。 需要兩個角色:存儲帳戶參與者和存儲 Blob 數據參與者。

  按照[添加存儲目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的說明添加角色。

## <a name="storage-infrastructure"></a>儲存基礎架構

快取支援 Azure Blob 容器或 NFS 硬體儲存匯出。 建立快取後添加儲存目標。

每種存儲類型都有特定的先決條件。

### <a name="blob-storage-requirements"></a>Blob 儲存要求

如果要將 Azure Blob 儲存與快取一起使用,則需要一個相容的儲存帳戶和一個空 Blob 容器,或者一個容器,該容器填充 Azure HPC 快取格式化的數據,如[將資料移動到 Azure Blob 儲存](hpc-cache-ingest.md)中所述。

在嘗試添加存儲目標之前創建帳戶。 添加目標時,可以創建新容器。

要建立相容的儲存帳戶,請使用以下設定:

* 效能：**標準**
* 帳號型態:**儲存 V2(通用 v2)**
* 複寫：**本地備援儲存體 (LRS)**
* 存取層(預設):**熱**

最好使用與緩存位於同一位置的存儲帳戶。
<!-- clarify location - same region or same resource group or same virtual network? -->

您還必須向快取應用程式授予對 Azure 儲存帳戶的訪問許可權,如上文[的許可權](#permissions)中所述。 按照[「添加存儲目標」](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的過程為緩存提供所需的訪問角色。 如果您不是存儲帳戶擁有者,則讓所有者執行此步驟。

### <a name="nfs-storage-requirements"></a>NFS 儲存要求

如果使用 NFS 儲存系統(例如,本地硬體 NAS 系統),請確保它滿足這些要求。 您可能需要與儲存系統(或資料中心)的網路管理員或防火牆管理員合作,以驗證這些設置。

> [!NOTE]
> 如果快取對 NFS 儲存系統的存取不足,則儲存目標創建將失敗。

有關詳細資訊包含在[故障排除 NAS 配置和 NFS 儲存目標問題](troubleshoot-nas.md)中。

* **網路連線:** Azure HPC 緩存需要在緩存子網和 NFS 系統的數據中心之間進行高頻寬網路存取。 建議[使用 ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或類似訪問。 如果使用 VPN,則可能需要將其配置為將 TCP MSS 夾在 1350 處,以確保大型數據包未被阻止。 讀取[VPN 資料包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions),以解決 VPN 設置的其他説明。

* **連接埠存取:** 快取需要存取儲存系統上的特定 TCP/UDP 連接埠。 不同類型的存儲具有不同的埠要求。

  要檢查存儲系統的設置,請按照此過程操作。

  * 向儲存`rpcinfo`系統發出命令以檢查所需的埠。 下面的命令列出了埠,並在表中格式化相關結果。 (使用系統的 IP 位址代替 *<storage_IP>* 術語。

    可以從安裝了 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果在群集子網中使用用戶端,它還可以幫助驗證子網和存儲系統之間的連接。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  確保``rpcinfo``查詢返回的所有埠都允許來自 Azure HPC 緩存子網的無限制流量。

  * 如果無法使用`rpcinfo`該 指令,請確保這些常用埠允許入站和出站流量:

    | 通訊協定 | 連接埠  | 服務  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | 恩洛克姆格 |
    | TCP/UDP  | 4046  | 已安裝   |
    | TCP/UDP  | 4047  | status   |

    某些系統對這些服務使用不同的埠號 - 請務必查閱存儲系統的文檔。這些埠號為這些服務。

  * 檢查防火牆設置,以確保它們允許所有這些必需埠上的流量。 請務必檢查 Azure 中使用的防火牆以及數據中心中的本地防火牆。

* **目錄存取:** 在`showmount`儲存系統上啟用命令。 Azure HPC Cache 使用此命令檢查儲存目標配置是否指向有效的匯出,並確保多個裝載不存取相同的子目錄(存在文件衝突的風險)。

  > [!NOTE]
  > 如果您的 NFS 儲存系統使用 NetApp 的 ONTAP 9.2 作業系統,**請不要啟用`showmount`**。 [請與 Microsoft 服務和支援聯繫](hpc-cache-support-ticket.md)以尋求説明。

  在 NFS 儲存目標故障排除文章中瞭解有關目錄清單存取[的更多內容](troubleshoot-nas.md#enable-export-listing)。

* **根訪問**(讀/寫):緩存作為使用者 ID 0 連接到後端系統。 檢查儲存系統上的這些設定:
  
  * 啟用 `no_root_squash`。 此選項可確保遠端根使用者可以存取 root 擁有的檔案。

  * 檢查匯出策略,以確保它們不包括對來自緩存子網的根訪問的限制。

  * 如果您的存儲具有作為另一個導出的子目錄的任何匯出,請確保緩存具有對路徑最低段的根訪問許可權。 關於詳細資訊,請閱讀 NFS 儲存目標故障排除文章中[目錄路徑上的根存取](troubleshoot-nas.md#allow-root-access-on-directory-paths)。

* NFS 後端存儲必須是相容的硬體/軟體平臺。 有關詳細資訊,請與 Azure HPC 緩存團隊聯繫。

## <a name="next-steps"></a>後續步驟

* 從 Azure 門戶[建立 Azure HPC 快取實體](hpc-cache-create.md)
