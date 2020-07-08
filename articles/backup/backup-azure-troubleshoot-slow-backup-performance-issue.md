---
title: 針對檔案和資料夾備份速度緩慢問題進行疑難排解
description: 提供疑難排解指導方針，以協助您診斷 Azure 備份效能問題的原因
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: d65eca685748499f52a990c7ac588bf44cbbba31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85194020"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>疑難排解 Azure 備份的檔案和資料夾備份速度緩慢問題

這篇文章提供疑難排解指引，可協助您診斷當您使用 Azure 備份時，檔案與資料夾備份效能緩慢的原因。 當您使用 Azure 備份代理程式來備份檔案時，備份處理程序進行的時間可能比預期的還要久。 此延遲可能是因為下列一或多個原因所造成：

* [正在備份的電腦有效能瓶頸。](#cause1)
* [有其他處理程序或防毒軟體在干擾 Azure 備份處理程序。](#cause2)
* [備份代理程式是在 Azure 虛擬機器 (VM) 上執行。](#cause3)  
* [您正在備份大量 (數百萬計) 的檔案。](#cause4)

在開始對問題進行疑難排解之前，建議您下載並安裝 [最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)。 我們會經常更新備份代理程式，以修正各種問題、新增功能和改善效能。

我們也強烈建議您檢閱 [Azure 備份服務常見問題集](backup-azure-backup-faq.md) ，以確定所遇到的問題並非任何常見的組態問題。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>原因：在未最佳化模式下執行備份作業

* MARS 代理程式可以使用 USN (更新序號) 變更日誌，在**最佳化模式**下執行備份作業，或藉由掃描整個磁碟區，檢查目錄或檔案中是否有變更，以在**未最佳化模式**下執行備份作業。
* 未最佳化模式會變慢，因為代理程式必須掃描磁碟區上的所有檔案，並與中繼資料進行比較，以判斷變更的檔案。
* 若要確認這一點，請從 MARS 代理程式主控台開啟 [作業詳細資料] 並檢查狀態，以查看是否顯示 [正在傳輸資料 (未最佳化，可能需要更多時間)]，如下所示：

    ![在未最佳化模式下執行](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* 下列情況可能會導致備份作業在未最佳化模式下執行：
  * 第一次備份 (也稱為初始複寫) 一律會在未最佳化模式下執行
  * 如果先前的備份作業失敗，則下一個排定的備份工作將會以未最佳化的方式執行。

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因：電腦的效能瓶頸

所備份電腦上的瓶頸可能會造成延遲。 例如，電腦讀取或寫入磁碟的能力，或者透過網路傳送資料的可用頻寬，都可能造成瓶頸。

Windows 提供了稱為 [效能監視器](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) 的內建工具，以偵測這些瓶頸。

以下是能幫助您診斷最佳化備份瓶頸的一些效能計數器和範圍。

| 計數器 | 狀態 |
| --- | --- |
| 邏輯磁碟 (實體磁碟)--% 閒置 |* 100% 閒置至 50% 閒置 = 狀況良好</br>* 49% 閒置至 20% 閒置 = 警告或監視</br>* 19% 閒置至 0% 閒置 = 重大或超出規範 |
| 邏輯磁碟 (實體磁碟)--% 平均磁碟的讀取或寫入秒數 |* 0.001 毫秒到 0.015 毫秒 = 狀況良好</br>* 0.015 毫秒到 0.025 毫秒 = 警告或監視</br>* 0.026 毫秒或更長 = 嚴重或超出規範 |
| 邏輯磁碟 (實體磁碟)--(所有執行個體) 目前的磁碟佇列長度 |80 個要求超過 6 分鐘 |
| 記憶體--集區未分頁位元組 |* 耗用不到 60% 集區 = 狀況良好<br>* 耗用 61% 到 80% 集區 = 警告或監視</br>* 耗用超過 80% 集區 = 重大或超出規範 |
| 記憶體--集區分頁位元組 |* 耗用不到 60% 集區 = 狀況良好</br>* 耗用 61% 到 80% 集區 = 警告或監視</br>* 耗用超過 80% 集區 = 重大或超出規範 |
| 記憶體--可用 MB 數 |* 50% 以上的可用記憶體可供使用 = 狀況良好</br>* 25% 的可用記憶體可供使用 = 監視</br>* 10% 的可用記憶體可供使用 = 警告</br>* 不到 100 MB 或 5% 的可用記憶體可供使用 = 重大或超出規範 |
| 處理器--\% 處理器時間 (所有執行個體) |* 已消耗低於 60% = 狀況良好</br>* 已消耗 61% 到 90% = 監視或警告</br>* 已消耗 91% 到 100% = 嚴重 |

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

如果您嘗試備份大型磁片，建議您在第一次備份時使用[Azure 資料箱](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box)（初始複寫）。  如果您無法使用資料箱，則在透過網路進行長時間資料傳輸期間，環境中發生的任何暫時性網路問題都會導致備份失敗。  若要避免這些失敗，您可以將幾個資料夾新增至初始備份，並持續以增量方式新增更多資料夾，直到所有資料夾都成功備份至 Azure 為止。  後續的增量備份將會相對較快。

## <a name="next-steps"></a>後續步驟

* [有關備份檔案和資料夾的常見問題](backup-azure-file-folder-backup-faq.md)
