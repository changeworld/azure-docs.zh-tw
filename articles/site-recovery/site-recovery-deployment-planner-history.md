---
title: Azure Site Recovery 部署規劃工具版本歷程記錄
description: 已知不同的 Site Recovery 部署規劃工具版本修正和已知的限制，以及其發行日期。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133795"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery 部署規劃工具版本歷程記錄

本文提供所有版本 Azure Site Recovery 部署規劃工具的歷程記錄，以及修正程式、每個版本的已知限制和發行日期。

## <a name="version-252"></a>版本2.52

**發行日期：2020年6月4日**

**補丁**

- 已新增 vCenter 7.0 的支援
- 已新增下列作業系統的支援：

    - SUSE Linux Enterprise 15 （含所有次要版本）
    - Red Hat Enterprise Linux 8 （包含所有次要版本）


## <a name="version-251"></a>版本2.51

**發行日期：2019年8月22日**

**補丁**

- 已修正部署規劃工具2.5 版的成本建議問題

## <a name="version-25"></a>版本2。5

**發行日期：2019年7月29日**

**補丁**

- 針對 VMware 虛擬機器和實體機器，建議會根據複寫到受控磁碟進行更新。
- 已新增 Windows 10 （x64）、Windows 8.1 （x64）、Windows 8 （x64）、Windows 7 （x64） SP1 或更新版本的支援

## <a name="version-24"></a>版本2。4

**發行日期：2019年4月17日**

**補丁**

- 改善作業系統的相容性，特別是在處理以當地語系化為基礎的錯誤時。
- 已將 Vm 新增至相容性檢查清單，最高可達 20 Mbps 的資料變更率（變換）。
- 改善錯誤訊息
- 已新增對 vCenter 6.7 的支援。
- 已新增 Windows Server 2019 和 Red Hat Enterprise Linux （RHEL）工作站的支援。



## <a name="version-23"></a>版本2。3

**發行日期：2018年12月3日**

**補丁**

- 已修正導致部署規劃工具無法使用提供的目標位置和訂用帳戶產生報告的問題。

## <a name="version-22"></a>版本2。2 

**發行日期：2018年4月25日**

**補丁**

- GetVMList 作業：
  - 已修正當指定的資料夾不存在時，導致 GetVMList 失敗的問題。 它現在會建立預設目錄，或建立在 outputfile 參數中指定的目錄。
  - 新增更詳細的 GetVMList 失敗原因。
- 已將 VM 類型資訊新增為部署規劃工具報表之 [相容的 Vm] 工作表中的資料行。
- Hyper-v 到 Azure 的嚴重損壞修復：
  - 已排除具有共用 Vhd 的 Vm，以及分析的傳遞磁片。 Startprofiling 作業會顯示主控台中已排除的 Vm 清單。
  - 已將具有超過64個磁片的 Vm 新增至不相容的 Vm 清單。
  - 已更新初始複寫（IR）和差異複寫（DR）壓縮因素。
  - 已新增 SMB 儲存體的有限支援。

## <a name="version-21"></a>2.1 版

**發行日期：2018年1月3日**

**補丁**

- 已更新 Excel 報表。
- 已修正 GetThroughput 作業中的 bug。
- 已新增選項，以限制要分析或產生報告的 Vm 數目。 預設限制為 1000 Vm。
- VMware 至 Azure 的嚴重損壞修復：
  - 已修正 Windows Server 2016 VM 進入不相容資料表的問題。 
  - 已更新可延伸韌體介面（EFI） Windows Vm 的相容性訊息。
- 已將 VMware 升級至 Azure 和 Hyper-v 至 Azure，每個 VM 的 VM 資料變換限制。 
- 改善了 VM 清單檔案剖析的可靠性。

## <a name="version-201"></a>版本 2.0.1

**發行日期：2017年12月7日**

**補丁**

- 已新增建議以優化網路頻寬。

## <a name="version-20"></a>版本2。0

**發行日期：2017年11月28日**

**補丁**

- 已新增 Hyper-v 至 Azure 嚴重損壞修復的支援。
- 已新增成本計算機。
- 已新增 VMware 至 Azure 嚴重損壞修復的 OS 版本檢查，以判斷 VM 是否與保護相容。 此工具會使用 vCenter server 為該 VM 傳回的作業系統版本字串。 這是使用者在 VMware 中建立 VM 時所選取的客體作業系統版本。

**已知的限制：**

- 針對 hyper-v 到 Azure 的嚴重損壞修復，不支援名稱包含下列字元的 VM： `,` 、 `"` 、 `[` 、 `]` 和 ``` ` ``` 。 如果已進行分析，報告產生將會失敗，或會有不正確的結果。
- 針對 VMware 到 Azure 的嚴重損壞修復，不支援名稱包含逗號的 VM。 如果已進行分析，則報表產生會失敗或會有不正確的結果。

## <a name="version-131"></a>1.3.1 版

**發行日期：2017年7月19日** 

**補丁**

- 已在報告產生中新增對大型磁片（> 1 TB）的支援。 現在您可以使用部署規劃工具來規劃磁片大小大於 1 TB （最多 4095 GB）之虛擬機器的複寫。
深入了解 [Azure Site Recovery 中的大型磁碟支援](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>版本1。3

**發行日期：2017年5月9日**

**補丁**

- 已在報告產生中新增對受控磁片的支援。 根據是否選取受控磁片來進行容錯移轉/測試容錯移轉，計算可放入單一儲存體帳戶的 Vm 數目。

## <a name="version-12"></a>版本1。2

**發行日期：2017年4月7日**

**補丁**

- 已新增每個 VM 的開機類型（BIOS 或 EFI）檢查，以判斷 VM 是否相容或不相容于保護。
- 在 [相容的 Vm] 和 [不相容的 Vm] 工作表中，新增每部虛擬機器的 OS 類型資訊。
- 已新增美國政府和中國 Microsoft Azure 區域的 GetThroughput 作業支援。
- 已針對 vCenter 和 ESXi 伺服器新增更多必要條件檢查。
- 修正當地區設定設為非英文時，產生不正確之報表的問題。

## <a name="version-11"></a>1.1 版

**發行日期：2017年3月9日**

**補丁**

- 已修正在不同 vCenter ESXi 主機上有兩個或多個 Vm 具有相同的名稱或 IP 位址時，無法分析 Vm 的問題。
- 已修正針對相容的 Vm 和不相容的 Vm 工作表而停用複製和搜尋的問題。

## <a name="version-10"></a>版本 1.0

**發行日期：2017年2月23日**

**已知的限制：**

- 僅支援 VMware 到 Azure 的嚴重損壞修復案例。 針對 Hyper-v 到 Azure 的嚴重損壞修復案例，請使用[hyper-v 容量規劃工具](./hyper-v-deployment-planner-overview.md)。
- 不支援美國政府和中國 Microsoft Azure 地區的 GetThroughput 作業。
- 如果 vCenter server 在不同的 ESXi 主機上有兩個或多個 Vm 具有相同的名稱或 IP 位址，此工具會 cann't 分析 Vm。
在此版本中，此工具會略過 VMListFile 中重複 VM 名稱或 IP 位址的分析。 因應措施是使用 ESXi 主機 (而非 vCenter Server) 剖析 VM。 請確定為每個 ESXi 主控制項執行一個實例。
