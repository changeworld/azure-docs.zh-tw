---
title: 支援 Azure 遷移中的超 V 遷移
description: 瞭解使用 Azure 遷移對 Hyper-V 遷移的支援。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245820"
---
# <a name="support-matrix-for-hyper-v-migration"></a>支援超 V 遷移矩陣

本文總結了使用[Azure 遷移：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)遷移超級 VM 的支援設置和限制。 如果要查找有關評估 Hyper-V VM 以遷移到 Azure 的資訊，請查看[評估支援矩陣](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>移轉限制

一次最多可以選擇 10 個 VM 進行複製。 如果要遷移更多電腦，以 10 的組進行複製。


## <a name="hyper-v-hosts"></a>Hyper-V 主機

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-V 主機可以是獨立的，也可以部署在群集中。 <br/>Azure 遷移複製軟體（Hyper-V 複製提供程式）需要在 Hyper-V 主機上安裝。|
| **許可權**           | 您需要對 Hyper-V 主機的管理員許可權。 |
| **主機作業系統** | Windows 伺服器 2019、Windows 伺服器 2016 或 Windows 伺服器 2012 R2。 |
| **URL 訪問** | Hyper-V 主機上的複製提供程式軟體需要訪問這些 URLS：<br/><br/> - login.microsoftonline.com：使用活動目錄進行存取控制和標識管理。<br/><br/> - *.backup.windowsazure.com：複製資料傳輸和協調。 遷移服務 URL。<br/><br/> - *blob.core.windows.net：將資料上載到存儲帳戶。<br/><br/> - dc.services.visualstudio.com：上傳用於內部監視的應用日誌。<br/><br/> - time.windows.com：驗證系統與全域時間之間的時間同步。
| **埠訪問** |  HTTPS 埠 443 上的出站連接以發送 VM 複製資料。

## <a name="hyper-v-vms"></a>Hyper-V VM

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **Azure 所需的更改** | 有些 VM 可能需要變更，才能在 Azure 中執行。 您需要在遷移之前手動進行調整。 相關文章包含有關如何執行此操作的說明。 |
| **Linux 啟動**                 | 如果 /boot 位於專用分區上，則應駐留在作業系統磁片上，並且不應跨多個磁片分佈。<br/> 如果 /boot 是根 （/） 分區的一部分，則'/' 分區應位於 OS 磁片上，而不是跨其他磁片。 |
| **UEFI 啟動**                  | Azure 中遷移的 VM 將自動轉換為 BIOS 引導 VM。 VM 應僅運行 Windows Server 2012 及更高版本。 OS 磁片應最多具有五個或更少的分區，並且 OS 磁片的大小應小於 300 GB。
  |
| **磁碟大小**                  | 2 TB 用於 OS 磁片，4 TB 用於資料磁片。
| **磁片編號** | 每個 VM 最多 16 個磁片。
| **加密磁片/卷**    | 不支援遷移。 |
| **RDM/直通磁片**      | 不支援遷移。 |
| **共用磁片** | 遷移不支援使用共用磁片的 VM。
| **NFS**                        | 在 VM 上裝載的卷的 NFS 卷不會複製。 |
| **ISCSI**                      | 不支援具有 iSCSI 目標的 VM 進行遷移。
| **目標磁片**                | 只能遷移到具有託管磁片的 Azure VM。 |
| **IPv6** | 不支援。
| **NIC 團隊** | 不支援。
| **Azure 網站恢復** | 如果啟用 VM 進行 Azure 網站恢復複製，則無法使用 Azure 遷移伺服器遷移進行複製。
| **連接埠** | HTTPS 埠 443 上的出站連接以發送 VM 複製資料。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複製到 Azure 的所有本地 VM 必須滿足此表中總結的 Azure VM 要求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 16 或更少。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
遷移後連接 - Windows | 要在遷移後連接到運行 Windows 的 Azure VM，請執行：<br/> - 在遷移在本地 VM 上啟用 RDP 之前。 確定已針對 [公用]**** 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/> 對於網站到網站 VPN 訪問，請在**Windows 防火牆** -> **允許的應用和功能**中啟用 RDP。 **Domain and Private** 此外，請檢查作業系統的 SAN 策略是否設置為**OnlineAll**。 [深入了解](prepare-for-migration.md)。 |
遷移後連接 -Linux | 要使用 SSH 在遷移後連接到 Azure VM，<br/> 在遷移之前，在本地電腦上，檢查安全外殼服務是否設置為"開始"，並且防火牆規則是否允許 SSH 連接。<br/> 容錯移轉後，在 Azure VM 上，允許傳入連接到 SSH 埠，用於通過 VM 故障的網路安全性群組規則以及連接到它的 Azure 子網。 此外，為 VM 添加公共 IP 位址。 |  

## <a name="next-steps"></a>後續步驟

[遷移用於遷移的超 VM。](tutorial-migrate-hyper-v.md)
