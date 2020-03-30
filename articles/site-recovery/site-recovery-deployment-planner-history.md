---
title: Azure 網站恢復部署規劃器版本歷程記錄
description: 已知的不同網站恢復部署規劃器版本修復和已知限制及其發佈日期。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433422"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure 網站恢復部署規劃器版本歷程記錄

本文提供 Azure 網站恢復部署規劃器的所有版本的歷史記錄，以及每個版本及其發佈日期中的修補程式、已知限制。

## <a name="version-251"></a>版本 2.51

**發佈日期：2019 年 8 月 22 日**

**修復：**

- 修復了部署規劃器版本 2.5 的成本建議問題

## <a name="version-25"></a>版本 2.5

**發佈日期：2019 年 7 月 29 日**

**修復：**

- 對於 VMware 虛擬機器和物理電腦，建議更新為基於對託管磁片的複製。
- 添加了對 Windows 10 （x64）、Windows 8.1 （x64）、Windows 8 （x64）、Windows 7 （x64） SP1 或更高版本的支援

## <a name="version-24"></a>版本 2.4

**發佈日期：2019 年 4 月 17 日**

**修復：**

- 改進了作業系統相容性，尤其是在處理基於當地語系化的錯誤時。
- 將具有高達 20 Mbps 資料更改速率（流失）的 VM 添加到相容性檢查表中。
- 改進的錯誤訊息
- 添加了對 vCenter 6.7 的支援。
- 添加了對 Windows 伺服器 2019 和紅帽企業 Linux （RHEL） 工作站的支援。



## <a name="version-23"></a>版本 2.3

**發佈日期：2018 年 12 月 3 日**

**修復：**

- 修復了阻止部署規劃器生成具有提供的目標位置和訂閱的報告的問題。

## <a name="version-22"></a>版本 2.2 

**發佈日期：2018 年 4 月 25 日**

**修復：**

- 獲取VMList操作：
  - 修復了如果指定的資料夾不存在，導致 GetVMList 失敗的問題。 它現在創建預設目錄，或創建輸出檔案參數中指定的目錄。
  - 添加了 GetVMList 的更詳細的失敗原因。
- 在部署規劃器報表的相容 VM 工作表中添加 VM 類型資訊作為列。
- Hyper-V 到 Azure 災害復原：
  - 從分析中排除具有共用 VHD 和"通過"磁片的 VM。 "啟動分析"操作顯示主控台中排除的 VM 的清單。
  - 將具有 64 個以上磁片的 VM 添加到不相容 VM 清單中。
  - 更新了初始複製 （IR） 和增量複製 （DR） 壓縮因數。
  - 添加了對 SMB 存儲的有限支援。

## <a name="version-21"></a>2.1 版

**發佈日期：2018 年 1 月 3 日**

**修復：**

- 更新了 Excel 報表。
- 修復了 Get 輸送量操作中的 Bug。
- 添加了用於限制 VM 數量以分析或生成報表的選項。 預設限制為 1，000 個 VM。
- VMware 到 Azure 災害復原：
  - 修復了 Windows Server 2016 VM 進入不相容表的問題。 
  - 更新了可延伸韌體介面 （EFI） Windows VM 的相容性消息。
- 將 VMware 更新為 Azure，Hyper-V 更新為 Azure，每個 VM 的 VM 資料改動限制。 
- 提高了 VM 清單檔分析的可靠性。

## <a name="version-201"></a>版本 2.0.1

**發佈日期：2017 年 12 月 7 日**

**修復：**

- 添加了優化網路頻寬的建議。

## <a name="version-20"></a>版本 2.0

**發佈日期：2017 年 11 月 28 日**

**修復：**

- 添加了對 Hyper-V 對 Azure 災害復原的支援。
- 添加了成本計算機。
- 添加了 VMware 的作業系統版本檢查到 Azure 災害復原，以確定 VM 是相容的還是不相容的保護。 該工具使用 vCenter 伺服器為該 VM 返回的作業系統版本字串。 它是使用者在 VMware 中創建 VM 時選擇的客體作業系統版本。

**已知限制：**

- `,`對於 Hyper-V 到 Azure 災害復原，不支援名稱包含：、、、、、``` ` ````"``[``]`和 的 VM。 如果分析，報告生成將失敗或結果不正確。
- 對於 VMware 到 Azure 災害復原，不支援名稱包含逗號的 VM。 如果已分析，報告生成將失敗或結果不正確。

## <a name="version-131"></a>1.3.1 版

**發佈日期：2017 年 7 月 19 日** 

**修復：**

- 在報表生成中增加了對大型磁片（> 1 TB）的支援。 現在，您可以使用部署規劃器規劃磁片大小大於 1 TB（最多 4095 GB）的虛擬機器的複製。
深入了解 [Azure Site Recovery 中的大型磁碟支援](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>版本 1.3

**發佈日期：2017 年 5 月 9 日**

**修復：**

- 在報表生成中添加了對託管磁片的支援。 可放置在單個存儲帳戶的 VM 數將根據是否為容錯移轉/測試容錯移轉選擇託管磁片來計算。

## <a name="version-12"></a>版本 1.2

**發佈日期：2017 年 4 月 7 日**

**修復：**

- 添加了引導類型（BIOS 或 EFI）檢查每個 VM，以確定 VM 是否與保護相容或不相容。
- 為相容 VM 和不相容的 VM 工作表中的每個虛擬機器添加了 OS 類型資訊。
- 為美國政府和中國微軟 Azure 區域添加了對 GetThroughput 操作的支援。
- 已針對 vCenter 和 ESXi 伺服器新增更多必要條件檢查。
- 修復了將地區設定設置為非英語時生成不正確的報告的問題。

## <a name="version-11"></a>1.1 版

**發佈日期：2017 年 3 月 9 日**

**修復：**

- 修復了在不同 vCenter ESXi 主機上存在兩個或多個具有相同名稱或 IP 位址的 VM 時阻止分析 VM 的問題。
- 修復了導致禁用相容 VM 和不相容 VM 工作表的複製和搜索的問題。

## <a name="version-10"></a>版本 1.0

**發佈日期：2017 年 2 月 23 日**

**已知限制：**

- 僅支援 VMware 到 Azure 災害復原方案。 對於 Hyper-V 到 Azure 災害復原方案，請使用[Hyper-V 容量規劃工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
- 不支援美國政府和中國 Microsoft Azure 區域的獲取輸送量操作。
- 如果 vCenter 伺服器在各個 ESXi 主機上具有兩個或多個具有相同名稱或 IP 位址的 VM，則該工具無法對 VM 進行設定檔。
在此版本中，此工具會略過 VMListFile 中重複 VM 名稱或 IP 位址的分析。 因應措施是使用 ESXi 主機 (而非 vCenter Server) 剖析 VM。 確保為每個 ESXi 主機運行一個實例。
