---
title: 排除 Azure HPC 快取 NFS 儲存目標
description: 避免與修復建立 NFS 儲存目標時可能導致故障的設定錯誤和其他問題的提示
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rohogue
ms.openlocfilehash: 0a24530810a448a713c01efbc8933b9f22d15b3b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536364"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>解決 NAS 設定與 NFS 儲存目標問題

本文提供了一些常見的配置錯誤和其他問題的解決方案,這些問題可能會阻止 Azure HPC 緩存將 NFS 儲存系統添加為存儲目標。

本文包含有關如何檢查埠以及如何啟用對 NAS 系統的根訪問的詳細資訊。 它還包含有關可能導致 NFS 儲存目標創建失敗的不太常見的問題的詳細資訊。

> [!TIP]
> 在使用本指南之前,請閱讀[NFS 儲存目標的先決條件](hpc-cache-prereqs.md#nfs-storage-requirements)。

如果此處未包含您的問題解決方案,請[打開支援票證](hpc-cache-support-ticket.md),以便 Microsoft 服務和支援部門可以與您合作調查和解決問題。

## <a name="check-port-settings"></a>檢查連接埠設定

Azure HPC 快取需要對後端 NAS 儲存系統上的多個 UDP/TCP 埠的讀/寫存取。 確保這些埠在 NAS 系統上可訪問,並且允許透過儲存系統和緩存子網之間的任何防火牆訪問這些埠。 您可能需要與資料中心的防火牆和網路管理員合作以驗證此配置。

來自不同供應商的儲存系統埠不同,因此在設置存儲目標時請檢查系統的要求。

通常,快取需要存取這些埠:

| 通訊協定 | 連接埠  | 服務  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | 恩洛克姆格 |
| TCP/UDP  | 4046  | 已安裝   |
| TCP/UDP  | 4047  | status   |

要瞭解系統所需的特定埠,請使用以下``rpcinfo``命令。 下面的此命令列出了埠,並在表中格式化相關結果。 (使用系統的 IP 位址代替 *<storage_IP>* 術語。

可以從安裝了 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果在群集子網中使用用戶端,它還可以幫助驗證子網和存儲系統之間的連接。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

確保``rpcinfo``查詢返回的所有埠都允許來自 Azure HPC 緩存子網的無限制流量。

在 NAS 本身以及儲存系統和緩存子網之間的任何防火牆上檢查這些設置。

## <a name="check-root-access"></a>檢查根存取

Azure HPC 快取需要存取儲存系統的匯出以創建儲存目標。 具體而言,它將匯出裝載為使用者 ID 0。

不同的儲存系統使用不同的方法來啟用此存取:

* Linux 伺服器``no_root_squash``通常 添加``/etc/exports``到 中的 導出路徑。
* NetApp 和 EMC 系統通常使用與特定 IP 位址或網路關聯的匯出規則來控制存取。

如果使用匯出規則,請記住快取可以使用與快取子網路的多個不同的 IP 位址。 允許從各種可能的子網 IP 位址進行訪問。

> [!NOTE]
> 默認情況下,Azure HPC 緩存會擠壓根訪問。 閱讀[配置其他緩存設置](configuration.md#configure-root-squash)的詳細資訊。

與 NAS 儲存供應商合作,為緩存啟用正確的存取級別。

### <a name="allow-root-access-on-directory-paths"></a>允許在目錄路徑上進行根存取
<!-- linked in prereqs article -->

對於匯出分層目錄的 NAS 系統,Azure HPC 緩存需要對每個匯出級別的根訪問。

例如,系統可能會顯示以下三個匯出:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

匯出``/ifs/accounting/payroll``是``/ifs/accounting``的 子項``/ifs/accounting``,``/ifs``本身就是 的 子項。

如果將``payroll``匯出添加為 HPC 快取儲存目標,則緩存實際上會從``/ifs/``那裡裝載 和訪問工資單目錄。 因此,Azure HPC 緩``/ifs``存需要 根``/ifs/accounting/payroll``訪問才能存取 匯出。

此要求與快取索引檔的方式有關,並且使用儲存系統提供的檔句柄避免文件衝突。

如果從不同的匯出檢索該檔,則具有分層匯出的 NAS 系統可以為同一檔提供不同的檔句柄。 例如,用戶端可以裝載``/ifs/accounting``和訪問該檔。 ``payroll/2011.txt`` 另一個客戶``/ifs/accounting/payroll``端載入及存取``2011.txt``此檔案 。 根據儲存系統分配檔句柄的方式,這兩個用戶端可能會收到具有不同檔句柄的同一檔(一個對於``<mount2>/payroll/2011.txt``,一``<mount3>/2011.txt``個用於)。

後端儲存系統保留檔句柄的內部別名,但 Azure HPC 緩存無法判斷其索引中哪個檔句柄引用同一項。 因此,緩存可能為同一檔緩存不同的寫入,並且錯誤地應用更改,因為它不知道它們是同一檔。

為了避免多個匯出中的檔可能的文件衝突,Azure HPC Cache 會自動在路徑中裝載最淺的可用``/ifs``匯出( 在示例中),並使用該匯出中提供的檔句柄。 如果多個匯出使用相同的基本路徑,Azure HPC 緩存需要對該路徑的根訪問許可權。

## <a name="enable-export-listing"></a>開啟匯出清單
<!-- link in prereqs article -->

當 Azure HPC 緩存查詢其匯出時,NAS 必須列出其匯出。

在大多數 NFS 儲存系統上,您可以透過從 Linux 客戶端送出以下查詢來測試此情況:``showmount -e <storage IP address>``

如果可能,請使用與快取相同的虛擬網路中的 Linux 用戶端。

如果該命令未列出匯出,則緩存將難以連接到存儲系統。 與 NAS 供應商合作,啟用匯出清單。

## <a name="adjust-vpn-packet-size-restrictions"></a>調整 VPN 封包大小限制
<!-- link in prereqs article and configuration article -->

如果在緩存和 NAS 設備之間具有 VPN,則 VPN 可能會阻止全尺寸 1500 位元組乙太網數據包。 如果 NAS 和 Azure HPC 緩存實例之間的大型交換未完成,但較小的更新按預期工作,則可能存在此問題。

除非您知道 VPN 配置的詳細資訊,否則無法判斷您的系統是否有此問題的簡單方法。 下面是一些可以幫助您檢查此問題的方法。

* 使用 VPN 兩側的資料包嗅探器檢測成功傳輸的資料包。
* 如果您的 VPN 允許 ping 命令,則可以測試發送全尺寸封包。

  使用這些選項將 VPN 上的 ping 命令執行到 NAS。 (使用儲存系統的 IP 位址代替 *<storage_IP>* 值。

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  以下是指令中的選項:

  * ``-M do``- 不要碎片
  * ``-c 1``- 只傳送資料封包
  * ``-s 1472``- 將有效負載的大小設置為 1472 位元組。 這是考慮到乙太網開銷后 1500 位元組數據包的最大大小負載。

  成功的回應看起來會像這樣：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果 ping 以 1472 位元組失敗,則可能存在封包大小問題。

要解決此問題,您可能需要配置 VPN 上的 MSS 夾緊功能,以使遠端系統正確檢測最大幀大小。 閱讀[VPN 閘道 IPsec/IKE 參數文件](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)以瞭解更多資訊。

在某些情況下,將 Azure HPC 緩存的 MTU 設置更改為 1400 可能會有所説明。 但是,如果將 MTU 限制在緩存上,則還必須限制與緩存交互的用戶端和後端儲存系統的 MTU 設置。 閱讀[配置其他 Azure HPC 緩存設定](configuration.md#adjust-mtu-value)以瞭解詳細資訊。

## <a name="check-for-acl-security-style"></a>檢查 ACL 安全樣式

某些 NAS 系統使用混合安全樣式,將存取控制列表 (ACL) 與傳統 POSIX 或 UNIX 安全性相結合。

如果您的系統將其安全樣式報告為 UNIX 或 POSIX,但不包括首字母縮寫詞"ACL",則此問題不會影響您。

對於使用 ACL 的系統,Azure HPC 快取需要追蹤其他使用者特定的值,以便控制檔案存取。 這是通過啟用訪問緩存來完成的。 沒有面向使用者的控制項來打開存取緩存,但您可以打開支援票證,請求為快取系統上受影響的儲存目標啟用該支援票證。

## <a name="next-steps"></a>後續步驟

如果您有本文未解決的問題,[則打開支援票證](hpc-cache-support-ticket.md)以獲得專家説明。
