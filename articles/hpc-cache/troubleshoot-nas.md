---
title: 排除 Azure HPC 緩存 NFS 存儲目標
description: 避免和修復創建 NFS 存儲目標時可能導致故障的配置錯誤和其他問題的提示
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652083"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>解決 NAS 配置和 NFS 存儲目標問題

本文提供了一些常見的配置錯誤和其他問題的解決方案，這些問題可能會阻止 Azure HPC 緩存將 NFS 存儲系統添加為存儲目標。

本文包含有關如何檢查埠以及如何啟用對 NAS 系統的根訪問的詳細資訊。 它還包含有關可能導致 NFS 存儲目標創建失敗的不太常見的問題的詳細資訊。

> [!TIP]
> 在使用本指南之前，請閱讀[NFS 存儲目標的先決條件](hpc-cache-prereqs.md#nfs-storage-requirements)。

如果此處未包含您的問題解決方案，請[打開支援票證](hpc-cache-support-ticket.md)，以便 Microsoft 服務和支援部門可以與您合作調查和解決問題。

## <a name="check-port-settings"></a>檢查埠設置

Azure HPC 緩存需要對後端 NAS 存儲系統上的多個 UDP/TCP 埠的讀/寫訪問。 確保這些埠在 NAS 系統上可訪問，並且允許通過存儲系統和緩存子網之間的任何防火牆訪問這些埠。 您可能需要與資料中心的防火牆和網路系統管理員合作以驗證此配置。

來自不同供應商的存儲系統埠不同，因此在設置存儲目標時請檢查系統的要求。

通常，緩存需要訪問這些埠：

| 通訊協定 | 連接埠  | 服務  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | 恩洛克姆格 |
| TCP/UDP  | 4046  | 已安裝   |
| TCP/UDP  | 4047  | status   |

要瞭解系統所需的特定埠，請使用以下``rpcinfo``命令。 下面的此命令列出了埠，並在表中格式化相關結果。 （使用系統的 IP 位址代替 *<storage_IP>* 術語。

可以從安裝了 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果在群集子網中使用用戶端，它還可以説明驗證子網和存儲系統之間的連接。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

確保``rpcinfo``查詢返回的所有埠都允許來自 Azure HPC 緩存子網的無限制流量。

在 NAS 本身以及存儲系統和緩存子網之間的任何防火牆上檢查這些設置。

## <a name="check-root-access"></a>檢查根訪問

Azure HPC 緩存需要訪問存儲系統的匯出以創建存儲目標。 具體而言，它將匯出裝載為使用者 ID 0。

不同的存儲系統使用不同的方法來啟用此訪問：

* Linux 伺服器通常``no_root_squash``添加到 中的``/etc/exports``匯出路徑。
* NetApp 和 EMC 系統通常使用與特定 IP 位址或網路關聯的匯出規則來控制訪問。

如果使用匯出規則，請記住緩存可以使用與緩存子網的多個不同的 IP 位址。 允許從各種可能的子網 IP 位址進行訪問。

與 NAS 存儲供應商合作，為緩存啟用正確的存取層級。

### <a name="allow-root-access-on-directory-paths"></a>允許在目錄路徑上進行根訪問
<!-- linked in prereqs article -->

對於匯出分層目錄的 NAS 系統，Azure HPC 緩存需要對每個匯出級別的根訪問。

例如，系統可能會顯示以下三個匯出：

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

匯出``/ifs/accounting/payroll``是 的``/ifs/accounting``子項，本身就是``/ifs/accounting``的``/ifs``子項。

如果將``payroll``匯出添加為 HPC 緩存存儲目標，則緩存實際上會從那裡裝載``/ifs/``和訪問工資單目錄。 因此，Azure HPC 緩存需要``/ifs``根訪問才能訪問``/ifs/accounting/payroll``匯出。

此要求與緩存索引檔的方式有關，並且使用存儲系統提供的檔案控制代碼避免檔衝突。

如果從不同的匯出檢索該檔，則具有分層匯出的 NAS 系統可以為同一檔提供不同的檔案控制代碼。 例如，用戶端可以裝載``/ifs/accounting``和訪問該檔。 ``payroll/2011.txt`` 另一個用戶端``/ifs/accounting/payroll``裝載和訪問該檔``2011.txt``。 根據存儲系統分配檔案控制代碼的方式，這兩個用戶端可能會收到具有不同檔案控制代碼的同一檔（一個對於``<mount2>/payroll/2011.txt``，一個``<mount3>/2011.txt``用於 ）。

後端存儲系統保留檔案控制代碼的內部別名，但 Azure HPC 緩存無法判斷其索引中哪個檔案控制代碼引用同一項。 因此，緩存可能為同一檔案快取不同的寫入，並且錯誤地應用更改，因為它不知道它們是同一檔。

為了避免多個匯出中的檔可能的檔衝突，Azure HPC Cache 會自動在路徑中裝載最淺的可用匯出（``/ifs``在示例中），並使用該匯出中提供的檔案控制代碼。 如果多個匯出使用相同的基本路徑，Azure HPC 緩存需要對該路徑的根存取權限。

## <a name="enable-export-listing"></a>啟用匯出清單
<!-- link in prereqs article -->

當 Azure HPC 緩存查詢其匯出時，NAS 必須列出其匯出。

在大多數 NFS 存儲系統上，您可以通過從 Linux 用戶端發送以下查詢來測試此情況：``showmount -e <storage IP address>``

如果可能，請使用與緩存相同的虛擬網路中的 Linux 用戶端。

如果該命令未列出匯出，則緩存將難以連接到存儲系統。 與 NAS 供應商合作，啟用匯出清單。

## <a name="adjust-vpn-packet-size-restrictions"></a>調整 VPN 資料包大小限制
<!-- link in prereqs article -->

如果在緩存和 NAS 設備之間具有 VPN，則 VPN 可能會阻止全尺寸 1500 位元組乙太網資料包。 如果 NAS 和 Azure HPC 緩存實例之間的大型交換未完成，但較小的更新按預期工作，則可能存在此問題。

除非您知道 VPN 配置的詳細資訊，否則無法判斷您的系統是否有此問題的簡單方法。 下面是一些可以説明您檢查此問題的方法。

* 使用 VPN 兩側的資料包嗅探器檢測成功傳輸的資料包。
* 如果您的 VPN 允許 ping 命令，則可以測試發送全尺寸資料包。

  使用這些選項將 VPN 上的 ping 命令運行到 NAS。 （使用存儲系統的 IP 位址代替 *<storage_IP>* 值。

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  以下是命令中的選項：

  * ``-M do``- 不要碎片
  * ``-c 1``- 只發送一個資料包
  * ``-s 1472``- 將有效負載的大小設置為 1472 位元組。 這是考慮到乙太網開銷後 1500 位元組資料包的最大大小負載。

  成功的回應看起來會像這樣：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果 ping 以 1472 位元組失敗，則可能需要在 VPN 上配置 MSS 夾緊，以使遠端系統正確檢測最大幀大小。 閱讀[VPN 閘道 IPsec/IKE 參數文檔](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)以瞭解更多資訊。

## <a name="check-for-acl-security-style"></a>檢查 ACL 安全樣式

某些 NAS 系統使用混合安全樣式，將存取控制清單 （ACL） 與傳統 POSIX 或 UNIX 安全性相結合。

如果您的系統將其安全樣式報告為 UNIX 或 POSIX，但不包括首字母縮寫詞"ACL"，則此問題不會影響您。

對於使用 ACL 的系統，Azure HPC 緩存需要跟蹤其他使用者特定的值，以便控制檔訪問。 這是通過啟用訪問緩存來完成的。 沒有面向使用者的控制項來打開訪問緩存，但您可以打開支援票證，請求為緩存系統上受影響的存儲目標啟用該支援票證。

## <a name="next-steps"></a>後續步驟

如果您有本文未解決的問題，[則打開支援票證](hpc-cache-support-ticket.md)以獲得專家説明。
