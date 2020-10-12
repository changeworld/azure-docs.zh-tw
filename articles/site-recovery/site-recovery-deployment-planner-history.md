---
title: Azure Site Recovery 部署規劃工具版本歷程記錄
description: 已知的不同 Site Recovery 部署規劃工具版本修正和已知的限制，以及其發行日期。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133795"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery 部署規劃工具版本歷程記錄

本文提供所有 Azure Site Recovery 部署規劃工具版本的歷程記錄，以及每個版本的修正、已知的限制，以及其發行日期。

## <a name="version-252"></a>版本2.52

**發行日期：2020年6月4日**

**修復：**

- 已新增 vCenter 7.0 的支援
- 已新增下列作業系統的支援：

    - SUSE Linux Enterprise 15 (的所有次要版本) 
    - 所有次要版本的 Red Hat Enterprise Linux 8 () 


## <a name="version-251"></a>版本2.51

**發行日期：2019年8月22日**

**修復：**

- 已修正部署規劃工具版本2.5 的成本建議問題

## <a name="version-25"></a>版本2。5

**發行日期：2019年7月29日**

**修復：**

- 若為 VMware 虛擬機器和實體機器，建議會更新為以受控磁碟的複寫為基礎。
- 新增 Windows 10 (x64) 、Windows 8.1 (x64) 、Windows 8 (x64) 、Windows 7 (x64) SP1 或更新版本的支援

## <a name="version-24"></a>版本2。4

**發行日期：2019年4月17日**

**修復：**

- 改進作業系統相容性，特別是在處理以當地語系化為基礎的錯誤時。
- 已新增最多 20 Mbps 資料變更率的 Vm (變換檢查清單) 。
- 改善錯誤訊息
- 已新增 vCenter 6.7 的支援。
- 已新增 Windows Server 2019 和 Red Hat Enterprise Linux (RHEL) 工作站的支援。



## <a name="version-23"></a>版本2。3

**發行日期：2018年12月3日**

**修復：**

- 修正了防止部署規劃工具產生具有所提供目標位置和訂用帳戶之報表的問題。

## <a name="version-22"></a>版本2。2 

**發行日期：2018年4月25日**

**修復：**

- GetVMList 作業：
  - 修正如果指定的資料夾不存在，導致 GetVMList 失敗的問題。 它現在會建立預設目錄，或建立在 outputfile 參數中指定的目錄。
  - 針對 GetVMList 新增更詳細的失敗原因。
- 將 VM 類型資訊新增為部署規劃工具報表相容 Vm 工作表中的資料行。
- Hyper-v 到 Azure 的損毀修復：
  - 已排除具有共用 Vhd 和傳遞磁片的 Vm，無法進行程式碼剖析。 Startprofiling 操作會在主控台中顯示已排除 Vm 的清單。
  - 已將超過64個磁片的 Vm 新增至不相容 Vm 的清單。
  - 已更新 (IR) 和差異複寫 (DR) 壓縮因素的初始複寫。
  - 新增 SMB 儲存體的有限支援。

## <a name="version-21"></a>2.1 版

**發行日期：2018年1月3日**

**修復：**

- 已更新 Excel 報表。
- 修正了 GetThroughput 作業中的 bug。
- 已新增選項來限制要分析或產生報告的 Vm 數目。 預設限制為1000個 Vm。
- VMware 到 Azure 的損毀修復：
  - 修正了 Windows Server 2016 VM 進入不相容資料表的問題。 
  - 已更新可延伸固件介面 (EFI) Windows Vm 的相容性訊息。
- 將 VMware 更新為 Azure 和 Hyper-v 到 Azure，每個 VM 的 VM 資料變換限制。 
- 改進了 VM 清單檔剖析的可靠性。

## <a name="version-201"></a>版本 2.0.1

**發行日期：2017年12月7日**

**修復：**

- 已新增將網路頻寬優化的建議。

## <a name="version-20"></a>版本2。0

**發行日期：2017年11月28日**

**修復：**

- 已將 Hyper-v 的支援新增至 Azure 嚴重損壞修復。
- 已新增成本計算機。
- 已將 VMware 至 Azure 嚴重損壞修復的 OS 版本檢查新增至 VM，以判斷 VM 是否與保護相容或不相容。 此工具會使用 vCenter server 針對該 VM 所傳回的作業系統版本字串。 它是使用者在 VMware 中建立 VM 時選取的客體作業系統版本。

**已知的限制：**

- 針對 hyper-v 到 Azure 的嚴重損壞修復，不支援名稱包含下列字元的 VM：、、、 `,` `"` `[` `]` 和 ``` ` ``` 。 如果已進行分析，報告產生將會失敗，或將會產生不正確的結果。
- 針對 VMware 到 Azure 的嚴重損壞修復，不支援名稱包含逗號的 VM。 如果已進行分析，則報表產生會失敗或產生不正確的結果。

## <a name="version-131"></a>1.3.1 版

**發行日期：2017年7月19日** 

**修復：**

- 已新增在報表產生中 ( # A0 1 TB) 的大型磁片支援。 現在您可以使用部署規劃工具來規劃磁片大小大於 1 TB 的虛擬機器複寫， (高達 4095 GB) 。
深入了解 [Azure Site Recovery 中的大型磁碟支援](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>版本1。3

**發行日期：2017年5月9日**

**修復：**

- 在產生報告時新增了受控磁片的支援。 您可以根據是否針對容錯移轉/測試容錯移轉選取受控磁片，來計算可放入單一儲存體帳戶的 Vm 數目。

## <a name="version-12"></a>版本1。2

**發行日期：2017年4月7日**

**修復：**

- 已新增開機類型 (BIOS 或 EFI) 檢查每個 VM，以判斷 VM 是否與保護相容或不相容。
- 已在相容的 Vm 和不相容的 Vm 工作表中新增每部虛擬機器的 OS 類型資訊。
- 已針對美國政府和中國 Microsoft Azure 區域新增 GetThroughput 作業的支援。
- 已針對 vCenter 和 ESXi 伺服器新增更多必要條件檢查。
- 修正當地區設定設定為非英文時產生錯誤報表的問題。

## <a name="version-11"></a>1.1 版

**發行日期：2017年3月9日**

**修復：**

- 修正當不同 vCenter ESXi 主機上有兩部或多部 Vm 具有相同的名稱或 IP 位址時，會導致無法分析 Vm 的問題。
- 修正了針對相容的 Vm 和不相容的 Vm 工作表而停用複製和搜尋的問題。

## <a name="version-10"></a>版本 1.0

**發行日期：2017年2月23日**

**已知的限制：**

- 僅支援 VMware 到 Azure 的損毀修復案例。 針對 Hyper-v 到 Azure 的嚴重損壞修復案例，請使用 [hyper-v 容量規劃工具](./hyper-v-deployment-planner-overview.md)。
- 不支援美國政府和中國 Microsoft Azure 地區的 GetThroughput 作業。
- 如果 vCenter server 在不同的 ESXi 主機上有兩部或多部 Vm 具有相同的名稱或 IP 位址，此工具就會 cann't 設定檔 Vm。
在此版本中，此工具會略過 VMListFile 中重複 VM 名稱或 IP 位址的分析。 因應措施是使用 ESXi 主機 (而非 vCenter Server) 剖析 VM。 請務必為每個 ESXi 主機執行一個實例。
