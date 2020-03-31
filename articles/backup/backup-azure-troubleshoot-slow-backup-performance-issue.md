---
title: 故障解決檔和資料夾備份速度慢
description: 提供疑難排解指導方針，以協助您診斷 Azure 備份效能問題的原因
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408686"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>疑難排解 Azure 備份的檔案和資料夾備份速度緩慢問題

這篇文章提供疑難排解指引，可協助您診斷當您使用 Azure 備份時，檔案與資料夾備份效能緩慢的原因。 當您使用 Azure 備份代理程式來備份檔案時，備份處理程序進行的時間可能比預期的還要久。 此延遲可能是因為下列一或多個原因所造成：

* [正在備份的電腦上存在性能瓶頸。](#cause1)
* [有其他處理程序或防毒軟體在干擾 Azure 備份處理程序。](#cause2)
* [備份代理程式是在 Azure 虛擬機器 (VM) 上執行。](#cause3)  
* [您正在備份大量 (數百萬計) 的檔案。](#cause4)

在開始對問題進行疑難排解之前，建議您下載並安裝 [最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)。 我們會經常更新備份代理程式，以修正各種問題、新增功能和改善效能。

我們也強烈建議您檢閱 [Azure 備份服務常見問題集](backup-azure-backup-faq.md) ，以確定所遇到的問題並非任何常見的組態問題。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>原因：備份作業在未優化模式下運行

* MARS 代理可以通過掃描整個卷檢查目錄或檔中的更改，使用 USN（更新序號）更改日誌或**未優化模式**在**優化模式下**運行備份作業。
* 未優化的模式速度很慢，因為代理必須掃描卷上的每個檔，並與中繼資料進行比較以確定更改的檔。
* 要驗證這一點，請從 MARS 代理主控台打開**作業詳細資訊**並檢查狀態，以查看是否表示**傳輸資料（未優化，可能需要更多時間），** 如下所示：

    ![在未優化模式下運行](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* 以下條件可能導致備份作業在未優化模式下運行：
  * 第一次備份（也稱為初始複製）將始終在未優化模式下運行
  * 如果上一個備份作業失敗，則下一個計畫的備份作業將運行為未優化。

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因：電腦的效能瓶頸

所備份電腦上的瓶頸可能會造成延遲。 例如，電腦讀取或寫入磁碟的能力，或者透過網路傳送資料的可用頻寬，都可能造成瓶頸。

Windows 提供了一個稱為[效能監視器](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481)（Perfmon） 的內置工具來檢測這些瓶頸。

以下是能幫助您診斷最佳化備份瓶頸的一些效能計數器和範圍。

| 計數器 | 狀態 |
| --- | --- |
| 邏輯磁碟 (實體磁碟)--% 閒置 |• 100% 空閒到 50% 空閒 = 正常</br>• 49% 空閒到 20% 空閒 = 警告或監視器</br>• 19% 空閒到 0% 空閒 = 嚴重或規格外 |
| 邏輯磁片（物理磁片）-%平均磁片秒讀或寫入 |• 0.001 ms 至 0.015 ms = 健康</br>• 0.015 ms 至 0.025 ms = 警告或監視器</br>• 0.026 ms 或更長 = 嚴重或規格外 |
| 邏輯磁碟 (實體磁碟)--(所有執行個體) 目前的磁碟佇列長度 |80 個要求超過 6 分鐘 |
| 記憶體--集區未分頁位元組 |• 消耗的池數少於 60% = 健康<br>• 61% 到 80% 的池消耗 • 警告或監視器</br>• 消耗的池數超過 80% = 嚴重或規格不足 |
| 記憶體--集區分頁位元組 |• 消耗的池數少於 60% = 健康</br>• 61% 到 80% 的池消耗 • 警告或監視器</br>• 消耗的池數超過 80% = 嚴重或規格不足 |
| 記憶體--可用 MB 數 |• 可用記憶體的 50% 或更多 = 正常</br>• 可用可用記憶體的 25% = 監視器</br>• 可用可用記憶體的 10% = 警告</br>• 可用記憶體少於 100 MB 或 5% = 關鍵或規格不足 |
| 處理器--\% 處理器時間 (所有執行個體) |• 消耗不到 60% = 健康</br>• 消耗 61% 到 90% = 監視器或警告</br>• 消耗 91% 到 100% = 嚴重 |

> [!NOTE]
> 如果您判斷是基礎結構問題，我們建議您定期執行磁碟重組以提升效能。
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>原因：有其他處理程序或防毒軟體在干擾 Azure 備份

我們曾看過許多例子，Windows 系統中的其他處理程序會對 Azure 備份代理程式處理程序的效能造成不良影響。 例如，如果您同時使用 Azure 備份代理程式和其他程式來備份資料，或是如果防毒軟體正在執行因而鎖定了要備份的檔案，檔案有多個鎖定可能會造成爭用情形。 在此情況下，備份可能會失敗，或作業執行時間可能會比預期的還要久。

此案例的最佳建議是關閉其他備份程式，以查看 Azure 備份代理程式的備份時間是否有變化。 通常只要確定多個備份作業不會在同一時間執行，就足以防止作業彼此干擾。

至於防毒程式，我們建議您排除下列檔案和位置︰

* 將 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe 做為處理程序
* C:\Program Files\Microsoft Azure Recovery Services Agent\ 資料夾
* 暫存位置 (如果您不是使用標準位置)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>原因：Azure 虛擬機器上執行的備份代理程式

如果您在 VM 上執行備份代理程式，其效能會比在實體機器上執行來得慢。 這是預期行為，因為有 IOPS 限制。  不過，您可以藉由將正在備份的資料磁碟機切換到 Azure 進階儲存體來獲得最佳效能。 我們正在努力修正此問題，未來的版本將會有這方面的修正。

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>原因：備份大量的 (數百萬計) 檔案

移動大量資料所花費的時間會比移動較少量資料更久。 在某些情況下，備份時間不僅與資料大小有關，也與檔案或資料夾的數目有關。 尤其是在備份數百萬計的小檔案 (幾位元組到幾千位元組) 時，更是如此。

發生這個行為的原因是因為當您備份資料並將資料移動至 Azure 的時候，Azure 會同時分類您的檔案。 在某些罕見的案例中，目錄作業花費的時間可能比預期時間更長。

下列指標可協助您了解瓶頸並據以處理下一個步驟︰

* **UI 正在顯示資料傳輸的進度**。 資料仍在傳輸中。 網路頻寬或資料大小可能會造成延遲。
* **UI 未顯示資料傳輸的進度**。 請開啟位於 C:\Program Files\Microsoft Azure 復原服務代理程式\Temp 的記錄，然後查看記錄中是否有 FileProvider::EndData 項目。 此項目表示資料傳輸完成，且目錄作業正在進行中。 請勿取消備份工作。 請稍微多等待一些時間讓目錄作業完成。 若問題持續發生，請連絡 [Azure 支援](https://portal.azure.com/#create/Microsoft.Support)。

## <a name="next-steps"></a>後續步驟

* [有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)