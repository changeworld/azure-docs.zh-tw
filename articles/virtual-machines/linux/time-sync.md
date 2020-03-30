---
title: Azure 中的 Linux VM 時間同步
description: Linux 虛擬機器的時間同步。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: c3571d9ba94e1803259457d473ed3f1669ea67ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330589"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure 中的 Linux VM 時間同步

時間同步對於安全性和事件相互關聯而言相當重要。 有時候時間同步會用於分散式交易實作。 多個電腦系統之間的時間精確度是透過同步來達成。 同步可能會受到多種因素影響，包括重新開機以及時間來源和擷取時間的電腦之間的網路流量。 

Azure 受到執行 Windows Server 2016 之基礎結構的支援。 Windows Server 2016 的演算法經過改善，用來修正時間和調整本機時鐘以便與 UTC 同步。  Windows Server 2016 的準確時間功能大幅改善了 VMICTimeSync 服務控管 VM 與主機以獲得準確時間的方式。 改善功能包括更精確的 VM 開始或 VM 還原初始時間，以及插斷延遲修正。 

>[!NOTE]
>如需 Windows 時間服務的快速概觀，請觀看本[高階概觀影片](https://aka.ms/WS2016TimeVideo)。
>
> 如需詳細資訊，請參閱 [Windows Server 2016 的準確時間](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)。 

## <a name="overview"></a>總覽

電腦時鐘精確度的衡量方式，是依據電腦時鐘與國際標準時間 (UTC) 時間標準的接近程度。 UTC 是由多國的精準原子鐘樣本所定義；原子鐘在 300 年內只會誤差一秒。 但是直接讀取 UTC 需使用專用的硬體。 替代方式為系統將時間伺服器同步為 UTC，並從其他電腦存取以提供延展性和強固性。 每部電腦都有持續運作的時間同步服務，知道需使用哪些時間伺服器，且會定期檢查電腦時鐘是否需要修正，並視需要調整時間。 

Azure 主機會與內部 Microsoft 時間伺服器同步，這些時間伺服器會透過 GPS 天線從 Microsoft 所屬的 Stratum 1 裝置擷取時間。 Azure 中的虛擬機器可以依賴其主機將準確的時間 (「主機時間」**) 傳遞至 VM，也可以由 VM 直接從時間伺服器取得時間，或結合兩種方式。 

在獨立的硬體上，Linux OS 只會在開機時讀取主機硬體時鐘。 之後，則會使用 Linux 核心中的插斷計時器來維護時鐘。 在此設定中，時鐘會隨時間慢慢移動。 在 Azure 上的較新 Linux 發行版本中，VM 可以使用 Linux Integration Services (LIS) 隨附的 VMICTimeSync 提供者，更頻繁地從主機查詢時鐘更新。

虛擬機器與主機的互動也可能會影響時鐘。 進行[記憶體保留維修](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)期間，VM 會暫停最多 30 秒。 比方說，維修開始之前 VM 時鐘顯示上午 10:00:00，並持續 28 秒。 VM 繼續執行後，VM 上的時鐘仍會顯示上午 10:00:00，也就是有 28 秒的誤差。 為了修正這個誤差，VMICTimeSync 服務會監視主機上發生的狀況，並提示您為了進行補償需在 VM 上做的變更。

若沒有時間同步功能，VM 上的時鐘將會累積錯誤。 若只有一部 VM，除非工作負載需要高度精準的計時功能，否則不會造成顯著的影響。 但是在多數情況下，我們都有多部互相連接並使用時間來追蹤交易的 VM，而且整個部署中的時間必須保持一致。 當 VM 之間的時間有所不同，可能會產生下列影響：

- 驗證將會失敗。 Kerberos 這類安全性通訊協定或憑證相依的技術，仰賴整個系統中的時間維持一致。
- 如果記錄 (或其他資料) 認知的時間有所差異，很難想像系統會發生什麼問題。 同一個事件看起來可能像發生在不同時間，導致難以建立相互關聯。
- 當時鐘有所誤差，計費功能也可能會計算錯誤。



## <a name="configuration-options"></a>設定選項

您通常可透過三種方式來為裝載於 Azure 中的 Linux VM 設定時間同步：

- Azure Marketplace 映像的預設設定同時使用 NTP 時間和 VMICTimeSync 主機時間。 
- 使用 VMICTimeSync 僅限主機。
- 使用另一個外部時間伺服器並搭配或不搭配使用 VMICTimeSync 主機時間。


### <a name="use-the-default"></a>使用預設值

根據預設，大多數適用於 Linux 的 Azure Marketplace 映像已設定為從兩個來源進行同步： 

- NTP 為主要，其會從 NTP 伺服器取得時間。 例如，Ubuntu 16.04 LTS Marketplace 映像使用 **ntp.ubuntu.com**。
- VMICTimeSync 服務為次要，用來將主機時間傳遞給 VM，並在 VM 暫停以進行維修後予以修正。 Azure 主機使用 Microsoft 所屬的 Stratum 1 裝置來維持時間的準確性。

在較新的 Linux 發行版本中，VMICTimeSync 服務使用精確時間通訊協定 (Precision Time Protocol，PTP)，但較早的發行版本可能不支援 PTP，並將切換回 NTP 以從主機取得時間。

若要確認 NTP 正確同步，請執行 `ntpq -p` 命令。

### <a name="host-only"></a>僅限主機 

由於 NTP 伺服器 (例如 time.windows.com 和 ntp.ubuntu.com) 為公用，與其同步時間需透過網際網路傳送流量。 不同的資料包延遲會對時間同步的品質產生負面影響。通過切換到僅主機同步來刪除 NTP 有時會提高時間同步結果。

如果在使用預設設定時發生時間同步問題，切換為僅限主機時間同步為合理的解決方式。 嘗試使用僅限主機同步，確認是否可改善您 VM 上的時間同步結果。 

### <a name="external-time-server"></a>外部時間伺服器

如果您有特定的時間同步需求，另提供使用外部時間伺服器的選項。 外部時間伺服器可提供特定的時間，用於測試案例相當實用，可確保與裝載於非 Microsoft 資料中心的機器之間的時間一致性，或以特殊方式處理閏秒。

您可以將外部時間伺服器與 VMICTimeSync 服務結合，以便提供與預設設定類似的結果。 將外部時間伺服器與 VMICTimeSync 結合，是處理 VM 暫停以進行維修時可能導致問題的最佳選擇。 

## <a name="tools-and-resources"></a>工具及資源

您可以透過一些基本命令來檢查時間同步設定。 Linux 發行版本的文件會針對設定該發行版本時間同步的最佳方式提供更多詳細資料。

### <a name="integration-services"></a>整合服務

檢查是否已載入整合服務 (hv_utils)。

```bash
lsmod | grep hv_utils
```
您應該會看到類似如下的內容：

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

查看 Hyper-V 整合服務精靈是否正在執行。

```bash
ps -ef | grep hv
```

您應該會看到類似如下的內容：

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>檢查是否 PTP

在新版的 Linux 中，精確時間通訊協定 (Precision Time Protocol，PTP) 時鐘來源會當作 VMICTimeSync 提供者的一部分來提供。 在舊版的 Red Hat Enterprise Linux 或 CentOS 7.x 上，可下載 [Linux Integration Services](https://github.com/LIS/lis-next) 並用來安裝已更新的驅動程式。 使用 PTP 時，Linux 裝置的格式會是 /dev/ptp*x*。 

查看哪些 PTP 時鐘來源可供使用。

```bash
ls /sys/class/ptp
```

在此範例中，傳回的值是 *ptp0*，因此我們會使用該值來檢查時鐘名稱。 若要驗證裝置，請檢查時鐘名稱。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

這應該會傳回 **hyperv**。

### <a name="chrony"></a>chrony

在 Ubuntu 19.10 和更高版本紅帽企業 Linux 和 CentOS 7.x 上，[計時](https://chrony.tuxfamily.org/)配置為使用 PTP 源時鐘。 較舊的 Linux 版本使用不支援 PTP 源的網路時間協定守護進程 （ntpd），而不是計時版本。 要在這些版本中啟用 PTP，必須使用以下代碼手動安裝和配置 chrony（在 chrony.conf 中）：

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

有關 Ubuntu 和 NTP 的詳細資訊，請參閱[時間同步](https://help.ubuntu.com/lts/serverguide/NTP.html)。

有關紅帽和 NTP 的詳細資訊，請參閱[配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp)。 

有關計時的詳細資訊，請參閱[使用計時](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony)。

如果同時啟用了 chrony 和 TimeSync 源，則可以將一個源標記為**首選**，這將另一個源集為備份。 由於 NTP 服務不會更新時鐘的大幅誤差 (除非經過很長的一段時間)，因此 VMICTimeSync 從暫停的 VM 事件復原時鐘的速度會比 NTP 工具更快。

預設情況下，計時加速或減慢系統時鐘以修復任何時間漂移。 如果漂移變得太大，色度無法修復漂移。 為了克服這種情況，可以`makestep`更改 **/etc/chrony.conf**中的參數，以強制時間同步（如果漂移超過指定的閾值）。

 ```bash
makestep 1.0 -1
```

在這裡，如果漂移大於 1 秒，計時將強制時間更新。 要應用更改，請重新開機計時服務：

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

在 19.10 之前的 SUSE 和 Ubuntu 版本中，使用[系統組態](https://www.freedesktop.org/wiki/Software/systemd/)時間同步。 有關 Ubuntu 的詳細資訊，請參閱[時間同步](https://help.ubuntu.com/lts/serverguide/NTP.html)。 有關 SUSE 的詳細資訊，請參閱[SUSE Linux 企業伺服器 12 SP3 版本資訊](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)中的第 4.5.8 節。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Windows Server 2016 的準確時間](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)。


