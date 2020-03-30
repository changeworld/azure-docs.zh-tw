---
title: Azure 遷移中的疑難評估和依賴項視覺化
description: 獲取有關 Azure 遷移中的故障排除評估和依賴項視覺化的説明。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127665"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>評量/相依性視覺效果疑難排解

本文可説明您解決 Azure[遷移：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)的評估和依賴項視覺化問題。


## <a name="assessment-readiness-issues"></a>評估準備問題

修復評估就緒問題，如下所示：

**問題** | **修復**
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 啟動類型的 VM。 我們建議您在運行遷移之前將引導類型轉換為 BIOS。 <br/><br/>可以使用 Azure 遷移伺服器遷移來處理此類 VM 的遷移。 它將在遷移期間將 VM 的啟動類型轉換為 BIOS。
有條件支援的 Windows 作業系統 | 作業系統已過了支援終止日期，並且需要自訂支援協定 （CSA） 來[在 Azure 中提供支援](https://aka.ms/WSosstatement)。 在遷移到 Azure 之前請考慮升級。
不支援的 Windows 作業系統 | Azure 僅支援[選定的 Windows 作業系統版本](https://aka.ms/WSosstatement)。 請考慮在遷移到 Azure 之前升級電腦。
有條件地背書 Linux 作業系統 | Azure 僅認可[選定的 Linux 作業系統版本](../virtual-machines/linux/endorsed-distros.md)。 請考慮在遷移到 Azure 之前升級電腦。
未背書的 Linux 作業系統 | 電腦可能在 Azure 中啟動，但 Azure 不提供作業系統支援。 在遷移到 Azure 之前，請考慮升級到[支援的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
作業系統不明 | VM 的作業系統在 vCenter 伺服器中被指定為"其他"。 此行為阻止 Azure 遷移驗證 VM 的 Azure 就緒性。 在遷移電腦之前，請確保 Azure[支援](https://aka.ms/azureoslist)作業系統。
不支援的位版本 | 具有 32 位作業系統的 VM 可能會在 Azure 中啟動，但我們建議您在遷移到 Azure 之前升級到 64 位。
需要微軟視覺化工作室訂閱 | 電腦正在運行 Windows 用戶端作業系統，該作業系統只能通過 Visual Studio 訂閱提供支援。
找不到用於所需存儲性能的 VM | 電腦所需的存儲性能（每秒輸入/輸出操作 [IOPS] 和輸送量）超過了 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到用於所需網路性能的 VM | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
在指定位置找不到 VM | 在移轉之前，使用不同的目標位置。
一個或多個不合適的磁片 | 連接到 VM 的一個或多個磁片不符合 Azure 要求。A<br/><br/> Azure 遷移：伺服器評估當前不支援超 SSD 磁片，並根據高級託管磁片 （32 TB） 的磁片限制評估磁片。<br/><br/> 對於連接到 VM 的每個磁片，請確保磁片的大小< 64 TB（由超 SSD 磁片支援）。<br/><br/> 如果不是，請先在遷移到 Azure 之前減小磁片大小，或者使用 Azure 中的多個磁片，並將[它們帶條](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以獲得更高的存儲限制。 確保 Azure[託管虛擬機器磁片](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)支援每個磁片所需的性能（IOPS 和輸送量）。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure 遷移：伺服器評估當前不支援超 SSD 磁片，並根據高級磁片限制（32 TB）評估磁片。<br/><br/> 但是，Azure 支援大小高達 64 TB 的磁片（由超 SSD 磁片支援）。 在遷移之前將磁片縮減到小於 64 TB，或者使用 Azure 中的多個磁片並將[它們條帶在一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)以獲得更高的存儲限制。
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
由於內部錯誤，無法確定磁片的適用性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到合適的 VM 類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
由於內部錯誤，無法確定 VM 的適用性 | 嘗試建立群組的新評估。
由於內部錯誤，無法確定一個或多個磁片的適用性 | 嘗試建立群組的新評估。
由於內部錯誤，無法確定一個或多個網路介面卡的適用性 | 嘗試建立群組的新評估。

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM 已"有條件地準備就緒"

由於伺服器評估中存在已知的漏洞，伺服器評估將 Linux VM 標記為"有條件就緒"。

- 該間隙可防止其檢測安裝在本地 VM 上的 Linux 作業系統的次要版本。
- 例如，對於 RHEL 6.10，當前伺服器評估僅檢測 RHEL 6 作為作業系統版本。
-  由於 Azure 僅認可 Linux 的特定版本，因此 Linux VM 當前在伺服器評估中標記為有條件就緒。
- 通過查看[Azure Linux 支援](https://aka.ms/migrate/selfhost/azureendorseddistros)，可以確定在本地 VM 上運行的 Linux 作業系統是否在 Azure 中得到認可。
-  驗證認可分發後，可以忽略此警告。

## <a name="azure-skus-bigger-than-on-premises"></a>Azure SKU 大於本地

Azure 遷移伺服器評估可能會根據評估類型建議具有比當前本地分配更多的內核和記憶體的 Azure VM SKU：


- VM SKU 建議取決於評估屬性。
- 這受您在伺服器評估中執行的評估類型（*基於性能*）或 *"作為本地"中*的影響。
- 對於基於性能的評估，伺服器評估會考慮本地 VM 的利用率資料（CPU、記憶體、磁片和網路利用率），以確定本地 VM 的正確目標 VM SKU。 在確定有效利用時，它還增加了一個舒適係數。
- 對於本地大小調整，不考慮效能資料，建議基於本地分配目標 SKU。

為了顯示這如何影響建議，我們舉一個例子：

我們有一個本地 VM，具有四個內核和八 GB 的記憶體，CPU 利用率為 50%，記憶體利用率為 50%，並且指定的舒適係數為 1.3。

-  如果評估**為"本地情況**"，則建議使用具有四個內核和 8 GB 記憶體的 Azure VM SKU。
- 如果評估基於性能，基於有效 CPU 和記憶體利用率（4 個內核的 50% = 1.3 = 2.6 個內核和 50% 的 8 GB 記憶體 = 1.3 = 5.3 GB 記憶體），則 4 個內核（最近支援的核心計數）和 8 GB 記憶體（最近支援）最便宜的 VM SKU建議使用記憶體大小）。
- [詳細瞭解](concepts-assessment-calculation.md#types-of-assessments)評估大小調整。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure 磁片 SKU 大於本地

Azure 遷移伺服器評估可能會建議根據評估類型建立更大的磁片。
- 伺服器評估中的磁片大小調整取決於兩個評估屬性：大小調整條件和存儲類型。
- 如果大小調整條件**基於性能**，並且存儲類型設置為 **"自動**"，則在確定目標磁片類型（標準硬碟、標準 SSD 或高級磁片）時，將考慮磁片的輸送量值和磁片的輸送量值。 然後，建議從磁片類型中使用磁片 SKU，並建議考慮本地磁片的大小要求。
- 如果大小調整條件是基於**性能的**，並且存儲類型為**高級**，則建議根據本地磁片的 IOPS、輸送量和大小要求在 Azure 中使用高級磁片 SKU。 當大小調整條件為 **"本地大小做"** 且存儲類型為**標準硬碟**、**標準 SSD**或**高級**時，使用相同的邏輯執行磁片大小調整。

例如，如果您有一個記憶體為 32 GB 的本地磁片，但磁片的聚合讀寫 IOPS 為 800 IOPS，則伺服器評估建議使用高級磁片（由於 IOPS 要求較高），然後建議提供可支援所需的 IOPS 和大小。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 儘管本地磁片所需的大小為 32 GB，但伺服器評估建議使用更大的磁片，因為本地磁片的 IOPS 要求很高。

## <a name="utilized-corememory-percentage-missing"></a>缺少已使用的核心/記憶體百分比

當 Azure 遷移設備無法收集相關本地 VM 的效能資料時，伺服器評估報告"已使用缺少的記憶體百分比"或"記憶體使用不足百分比"。

- 如果在評估期間關閉 VM，則可能發生這種情況。 關閉 VM 時，設備無法收集其效能資料。
- 如果僅缺少記憶體計數器，並且您正在嘗試評估 Hyper-VM，請檢查這些 VM 上是否啟用了動態記憶體。 僅 Hyper-VM 存在一個已知問題，其中 Azure 遷移設備無法為未啟用動態記憶體的 VM 收集記憶體利用率資料。
- 如果缺少任何效能計數器，Azure 遷移伺服器評估將回退到分配的內核和記憶體，並建議相應的 VM 大小。
- 如果缺少所有效能計數器，請確保滿足評估的埠訪問要求。 詳細瞭解[VMware、Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access)和[物理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)伺服器評估的埠訪問要求。 [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access)

## <a name="is-the-operating-system-license-included"></a>是否包括作業系統許可證？

Azure 遷移伺服器評估當前僅考慮 Windows 電腦的作業系統許可證成本。 目前不考慮 Linux 電腦的許可成本。

## <a name="how-does-performance-based-sizing-work"></a>基於績效的尺寸調整如何工作？

伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 [瞭解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集基於性能的資料。


## <a name="dependency-visualization-in-azure-government"></a>Azure 政府中的依賴項視覺化

Azure 遷移取決於依賴項視覺化功能的服務映射。 由於服務映射在 Azure 政府中當前不可用，因此此功能在 Azure 政府版中不可用。

## <a name="dependencies-dont-show-after-agent-install"></a>代理安裝後不顯示依賴項

在本地 VM 上安裝依賴項視覺化代理後，Azure 遷移通常需要 15-30 分鐘才能在門戶中顯示依賴項。 如果等待超過 30 分鐘，請確保 Microsoft 監視代理 （MMA） 可以連接到日誌分析工作區。

若為 Windows VM：
1. 在控制台中，啟動 MMA。
2. 在**Microsoft 監視代理屬性** > **Azure 日誌分析 （OMS） 中**，請確保工作區**的狀態**為綠色。
3. 如果狀態不是綠色，請嘗試刪除工作區，然後將其再次添加到 MMA。

    ![MMA 狀態](./media/troubleshoot-assessment/mma-properties.png)

對於 Linux VM，請確保 MMA 和依賴項代理的安裝命令成功。

## <a name="supported-operating-systems"></a>支援的作業系統

- **彩信代理**：查看支援的[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)和[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)作業系統。
- **依賴項代理**：支援的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)作業系統。

## <a name="visualize-dependencies-for--hour"></a>視覺化 > 小時的依賴項

儘管 Azure 遷移允許您返回上個月的特定日期，但視覺化依賴項的最大持續時間為 1 小時。

例如，可以使用依賴項映射中的工期功能查看昨天的依賴項，但只能查看一小時期限。

但是，可以使用 Azure 監視器日誌在較長的持續時間內[查詢依賴項資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="visualized-dependencies-for--10-machines"></a>> 10 台電腦的視覺化依賴項

在 Azure 遷移伺服器評估中，可以視覺化具有最多 10 個 VM[的組的依賴項](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 對於較大的組，我們建議您將 VM 拆分為較小的組，以視覺化依賴項。

## <a name="machines-show-install-agent"></a>機器顯示"安裝代理"

將啟用依賴項視覺化的電腦遷移到 Azure 後，由於以下行為，電腦可能會顯示"安裝代理"操作，而不是"查看依賴項"：


- 遷移到 Azure 後，本地電腦將關閉，等效 VM 在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。
- 機器可能也有不同的 IP 位址，具體取決於您是否保留本地 IP 位址。
- 如果 MAC 位址和 IP 位址都與本地位址不同，則 Azure 遷移不會將本地電腦與任何服務映射依賴項資料相關聯。 在這種情況下，它將顯示安裝代理而不是查看依賴項的選項。
- 測試遷移到 Azure 後，本地電腦將保持按預期打開狀態。 Azure 中旋轉的等效電腦獲取不同的 MAC 位址，並可能獲取不同的 IP 位址。 除非阻止從這些電腦傳出 Azure 監視器日誌流量，否則 Azure 遷移不會將本地電腦與任何服務映射依賴項資料相關聯，因此將顯示安裝代理的選項，而不是查看依賴項。


## <a name="capture-network-traffic"></a>捕獲網路流量

收集網路流量日誌，如下所示：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 按 F12 啟動開發人員工具。 如果需要，清除導航設置**上的清除條目**。
3. 選擇 **"網路**"選項卡，並開始捕獲網路流量：
   - 在 Chrome 中，選取 [保留記錄]****。 記錄應該會自動啟動。 紅色圓圈表示正在捕獲流量。 如果未出現紅色圓圈，請選擇黑色圓圈以開始。
   - 在 Microsoft 邊緣和 Internet 資源管理器中，錄製應自動啟動。 如果沒有，請選擇綠色播放按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中，按右鍵並選擇 **"保存為包含內容的 HAR"。** 此操作壓縮日誌並將其匯出為 .har 檔。
   - 在 Microsoft 邊緣或 Internet 資源管理器中，選擇 **"匯出捕獲的流量**"選項。 此操作壓縮並匯出日誌。
6. 選擇 **"主控台"** 選項卡以檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選擇 **"保存為**"以匯出日誌，然後壓縮日誌。
   - 在微軟邊緣或互聯網資源管理器中，按右鍵錯誤並選擇"**複製所有**"。
7. 關閉 Developer Tools。

## <a name="next-steps"></a>後續步驟

[創建](how-to-create-assessment.md)或[自訂](how-to-modify-assessment.md)評估。
