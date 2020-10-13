---
title: 針對 Azure HPC Cache NFS 儲存體目標進行疑難排解
description: 避免和修正建立 NFS 儲存體目標時可能導致失敗的設定錯誤和其他問題的秘訣
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: efa163a2c10a7dc93bf5d26865a0e7eb43f11dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082761"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>針對 NAS 設定和 NFS 儲存體目標問題進行疑難排解

本文提供一些常見設定錯誤和其他問題的解決方案，這些問題可能會讓 Azure HPC Cache 無法將 NFS 儲存體系統新增為儲存體目標。

本文包含如何檢查埠，以及如何啟用對 NAS 系統的根目錄存取的詳細資料。 其中也包含較不常見的問題的詳細資訊，這些問題可能會導致建立 NFS 儲存體目標失敗。

> [!TIP]
> 使用本指南之前，請先閱讀 [NFS 儲存體目標的先決條件](hpc-cache-prerequisites.md#nfs-storage-requirements)。

如果此處未包含您問題的解決方案，請 [開啟支援票證](hpc-cache-support-ticket.md) ，讓 Microsoft 服務和支援人員可以與您合作，以調查並解決問題。

## <a name="check-port-settings"></a>檢查埠設定

Azure HPC Cache 需要後端 NAS 儲存體系統上數個 UDP/TCP 埠的讀取/寫入存取權。 請確定這些埠可在 NAS 系統上存取，而且透過儲存體系統和快取子網之間的任何防火牆，允許流量通過這些埠。 您可能需要為您的資料中心與防火牆和網路系統管理員合作，以確認此設定。

不同廠商的儲存體系統都有不同的埠，因此在設定儲存體目標時，請檢查您的系統需求。

一般而言，快取需要存取這些埠：

| 通訊協定 | 連接埠  | 服務  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | status   |

若要瞭解您的系統所需的特定埠，請使用下列 ``rpcinfo`` 命令。 下列命令會列出埠，並在資料表中將相關的結果格式化。  (使用系統的 IP 位址來取代 *<storage_IP>* 詞彙。 ) 

您可以從已安裝 NFS 基礎結構的任何 Linux 用戶端發出此命令。 如果您在叢集子網內使用用戶端，它也可以協助驗證子網與儲存體系統之間的連線能力。

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

請確定查詢所傳回的所有埠都 ``rpcinfo`` 允許來自 Azure HPC Cache 子網的不受限制流量。

請在 NAS 本身以及儲存體系統與快取子網之間的任何防火牆上檢查這些設定。

## <a name="check-root-access"></a>檢查根存取

Azure HPC Cache 需要存取儲存體系統的匯出，才能建立儲存體目標。 具體而言，它會以使用者識別碼0的形式掛接匯出。

不同的儲存體系統會使用不同的方法來啟用此存取：

* Linux 伺服器通常會新增 ``no_root_squash`` 至中的匯出路徑 ``/etc/exports`` 。
* NetApp 和 EMC 系統通常會使用系結至特定 IP 位址或網路的匯出規則來控制存取權。

如果使用匯出規則，請記住快取可以使用來自快取子網的多個不同 IP 位址。 允許從可能的子網 IP 位址的完整範圍進行存取。

> [!NOTE]
> 根據預設，Azure HPC Cache squashes 根目錄存取。 如需詳細資料，請參閱 [設定其他](configuration.md#configure-root-squash) 快取設定。

請與您的 NAS 存放裝置廠商合作，為快取啟用適當的存取層級。

### <a name="allow-root-access-on-directory-paths"></a>允許目錄路徑的根目錄存取
<!-- linked in prereqs article -->

針對匯出階層式目錄的 NAS 系統，Azure HPC Cache 需要每個匯出層級的根目錄存取權。

例如，系統可能會顯示三個匯出，如下所示：

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

匯出 ``/ifs/accounting/payroll`` 是的子系 ``/ifs/accounting`` ，本身為的子系 ``/ifs/accounting`` ``/ifs`` 。

如果您將 ``payroll`` 匯出新增為 HPC cache 儲存目標，快取實際上會 ``/ifs/`` 從該處掛接和存取薪資目錄。 因此 Azure HPC Cache 需要存取的根目錄才能 ``/ifs`` 存取 ``/ifs/accounting/payroll`` 匯出。

這項需求與快取使用儲存系統提供的檔案控制代碼來編制檔案的索引和避免檔案衝突的方式有關。

如果從不同的匯出抓取檔案，具有階層式匯出的 NAS 系統可以為相同的檔案提供不同的檔案控制代碼。 例如，用戶端可以掛接 ``/ifs/accounting`` 和存取檔案 ``payroll/2011.txt`` 。 另一個用戶端會掛接 ``/ifs/accounting/payroll`` 和存取檔案 ``2011.txt`` 。 根據儲存體系統如何指派檔案控制代碼，這兩個用戶端可能會收到相同檔案，其中包含不同的檔案控制代碼 (一個 ``<mount2>/payroll/2011.txt`` ，另一個用於 ``<mount3>/2011.txt``) 。

後端儲存系統會保留檔案控制代碼的內部別名，但 Azure HPC Cache 無法分辨其索引中的哪些檔案控制代碼參考相同的專案。 因此，快取可能會針對相同檔案快取不同的寫入，並不正確地套用變更，因為它不知道它們是相同的檔案。

為了避免多個匯出中的檔案可能發生檔案衝突，Azure HPC Cache 會自動在範例) 的路徑 (中裝載 shallowest 可用的匯出， ``/ifs`` 並且使用該匯出提供的檔案控制代碼。 如果多個匯出使用相同的基底路徑，Azure HPC Cache 需要存取該路徑的根目錄。

## <a name="enable-export-listing"></a>啟用匯出清單
<!-- link in prereqs article -->

當 Azure HPC Cache 進行查詢時，NAS 必須列出其匯出。

在大部分 NFS 儲存體系統上，您可以從 Linux 用戶端傳送下列查詢來進行測試： ``showmount -e <storage IP address>``

如果可能的話，請使用與您的快取相同的虛擬網路中的 Linux 用戶端。

如果該命令未列出匯出，則快取會無法連接到您的儲存系統。 請與您的 NAS 廠商合作，以啟用匯出清單。

## <a name="adjust-vpn-packet-size-restrictions"></a>調整 VPN 封包大小限制
<!-- link in prereqs article and configuration article -->

如果您的快取與 NAS 裝置之間有 VPN，VPN 可能會封鎖完整大小的1500位元組乙太網路封包。 如果 NAS 與 Azure HPC Cache 實例之間的大型交換未完成，但較小的更新如預期般運作，您可能會遇到這個問題。

除非您知道 VPN 設定的詳細資料，否則不會有簡單的方法可分辨您的系統是否有此問題。 以下是一些可協助您檢查此問題的方法。

* 請在 VPN 的雙方使用封包嗅探，以偵測哪些封包傳輸成功。
* 如果您的 VPN 允許 ping 命令，您可以測試傳送完整大小的封包。

  使用這些選項，透過 VPN 對 NAS 執行 ping 命令。  (使用儲存體系統的 IP 位址來取代 *<storage_IP>* 值。 ) 

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  以下是命令中的選項：

  * ``-M do`` -不要片段
  * ``-c 1`` -只傳送一個封包
  * ``-s 1472`` -將承載的大小設定為1472個位元組。 這是在計算乙太網路額外負荷之後，1500位元組封包的大小上限。

  成功的回應看起來會像這樣：

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  如果 ping 失敗，並出現1472個位元組，可能是封包大小的問題。

若要修正此問題，您可能需要在 VPN 上設定 MSS 固定，讓遠端系統正確地偵測框架大小上限。 若要深入瞭解，請參閱 [VPN 閘道 IPsec/IKE 參數檔](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) 。

在某些情況下，將 Azure HPC Cache 的 MTU 設定變更為1400可以提供協助。 但是，如果您限制快取上的 MTU，您也必須針對與快取互動的用戶端和後端儲存體系統限制 MTU 設定。 如需詳細資料，請參閱 [設定額外的 Azure HPC Cache 設定](configuration.md#adjust-mtu-value) 。

## <a name="check-for-acl-security-style"></a>檢查 ACL 安全性樣式

有些 NAS 系統使用混合式安全性樣式，將 (Acl) 的存取控制清單與傳統 POSIX 或 UNIX 安全性結合在一起。

如果您的系統將其安全性樣式報告為 UNIX 或 POSIX，而不包含縮寫「ACL」，則此問題不會影響您。

若為使用 Acl 的系統，Azure HPC Cache 需要追蹤其他的使用者特定值，才能控制檔案存取。 這是藉由啟用存取快取來完成。 沒有使用者面向的控制項可以開啟存取快取，但您可以開啟支援票證，要求針對您快取系統上受影響的儲存體目標啟用支援票證。

## <a name="next-steps"></a>接下來的步驟

如果您有本文未解決的問題，請 [開啟支援票證](hpc-cache-support-ticket.md) 以取得專家協助。
