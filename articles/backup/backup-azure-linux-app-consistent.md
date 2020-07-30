---
title: Linux Vm 的應用程式一致備份
description: 在 Azure 建立 Linux 虛擬機器的應用程式一致備份。 本文說明如何設定指令碼架構，備份以 Azure 部署的 Linux VM。 本文另包含疑難排解資訊。
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 1ebf1b4148c43b07c0fddee67970abe8381e4c30
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407093"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure Linux VM 的應用程式一致備份

在擷取 VM 的備份快照集時，應用程式一致性代表當 VM 還原後，應用程式隨著 VM 開機而啟動。 可想而知，應用程式一致性是多麼重要的環節。 為了確保 Linux VM 符合應用程式一致性，您可以使用 Linux 前置指令碼和後置指令碼架構來製作應用程式一致備份。 前置指令碼和後置指令碼架構支援以 Azure Resource Manager 部署的 Linux 虛擬機器。 應用程式一致性的腳本不支援 Service Manager 部署的虛擬機器或 Windows 虛擬機器。

## <a name="how-the-framework-works"></a>架構的運作方式

此架構讓您在擷取 VM 快照集時可選擇執行自訂前置指令碼和後置指令碼。 前置指令碼會在您擷取 VM 快照集的前一刻執行，而後置指令碼會緊接在您擷取 VM 快照集之後執行。 前置指令碼和後置指令碼讓您在擷取 VM 快照集時，能夠彈性地控制應用程式與環境。

前置指令碼會叫用原生應用程式 API 以停止 IO，並將記憶體中的內容排清到磁碟。 這些動作能確保快照集符合應用程式一致性。 後置腳本使用原生應用程式 Api 來解除凍結 IOs，讓應用程式能夠在 VM 快照集後繼續正常作業。

## <a name="steps-to-configure-pre-script-and-post-script"></a>設定前指令碼和後置指令碼的步驟

1. 以根使用者身分登入您想要備份的 Linux VM。

2. 從 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載 **VMSnapshotScriptPluginConfig.json**，並將它複製到所有要備份之 VM 上的 **/etc/azure** 資料夾。 如果 **/etc/azure** 資料夾不存在，請予以建立。

3. 在所有打算備份的 VM 上，複製應用程式的前置指令碼與後置指令碼。 您可以將指令碼複製到 VM 上的任何位置。 請務必更新 **VMSnapshotScriptPluginConfig.json** 檔案中指令碼檔案的完整路徑。

4. 請確定這些檔案的下列權限︰

   - **VMSnapshotScriptPluginConfig.json**：權限 “600”。 例如，只有「根」使用者能夠擁有此檔案的「讀取」與「寫入」權限，且沒有任何使用者應擁有「執行」權限。

   - **前置指令碼檔案**：權限 “700”。  例如，只有「根」使用者擁有此檔案的「讀取」、「寫入」及「執行」權限。

   - **後置指令碼**：權限 “700”。 例如，只有「根」使用者擁有此檔案的「讀取」、「寫入」及「執行」權限。

   > [!IMPORTANT]
   > 此架構讓使用者擁有強大的能力。 請妥善保護架構，並確認只有「根」使用者能夠存取重要的 JSON 與指令碼檔案。
   > 如果需求不符，指令碼將不會執行，因而導致檔案系統毀損和不一致的備份。
   >

5. 如以下所述設定 VMSnapshotScriptPluginConfig.json****：
    - **pluginName**：將此欄位保留原狀，否則您的腳本可能無法如預期般運作。

    - **preScriptLocation** ：在要備份的 VM 上提供前置指令碼完整路徑。

    - **postScriptLocation**：在要備份的 VM 上提供後置指令碼完整路徑。

    - **preScriptParams**：提供必須傳遞給前置指令碼的選擇性參數。 所有參數都應該以引號括住。 如果您使用多個參數，請以逗號分隔參數。

    - **postScriptParams**：提供必須傳遞給前置指令碼的選擇性參數。 所有參數都應該以引號括住。 如果您使用多個參數，請以逗號分隔參數。

    - **preScriptNoOfRetries**：設定在終止前，如果發生任何錯誤，應重試前置腳本的次數。 零表示只有一次嘗試，如果失敗，則不重試。

    - **postScriptNoOfRetries**：設定在終止前如果發生任何錯誤，應重試後置腳本的次數。 零表示只有一次嘗試，如果失敗，則不重試。

    - **timeoutInSeconds**：指定前置腳本和後置腳本的個別超時（最大值可以是1800）。

    - **continueBackupOnFailure**：如果您在前置或後置指令碼失敗時想要 Azure 備份回復使用檔案系統一致/損毀一致備份，請將這個值設定為 true****。 若將此設定為**false** ，則會在發生腳本失敗時導致備份失敗（但當您的單一磁片 VM 切換回損毀一致備份時，不論此設定為何）。 當**continueBackupOnFailure**值設定為 false 時，如果備份失敗，則會根據服務中的重試邏輯（針對約定嘗試次數），再次嘗試備份作業。

    - **fsFreezeEnabled**：指定當您在擷取 VM 快照時是否應該呼叫 Linux fsfreeze，以確保檔案系統一致性。 我們建議您將這個設定保持為 true****，除非您的應用程式在已停用的 fsfreeze 上具有相依性。

    - **ScriptsExecutionPollTimeSeconds**：設定在每次輪詢與腳本執行之間，延伸模組必須進入睡眠狀態的時間。 例如，如果值為2，延伸模組會檢查前置/後置腳本執行是否每2秒完成一次。 其可接受的最小和最大值分別為1和5。 此值應嚴格為整數。

6. 此指令碼架構現在已設定完成。 如果已經設定 VM 備份，則下次備份會叫用指令碼，並觸發應用程式一致備份。 如果未設定 VM 備份，請使用將[Azure 虛擬機器備份至復原服務保存庫來](./backup-azure-vms-first-look-arm.md)設定它。

## <a name="troubleshooting"></a>疑難排解

確定您在寫入前置指令碼和後置指令碼時新增適當的記錄，並檢閱您的指令碼記錄以修正任何指令碼的問題。 如果仍有執行指令碼的問題，請參閱下列表格以取得詳細資訊。

| 錯誤 | 錯誤訊息 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |前置指令碼傳回錯誤，所以備份可能無法應用程式一致。| 查看您指令碼的失敗記錄來修正此問題。|  
|Post-ScriptExecutionFailed |後置指令碼傳回可能會影響應用程式狀態的錯誤。 |查看您指令碼的失敗記錄並檢查應用程式狀態來修正此問題。 |
| Pre-ScriptNotFound |在 VMSnapshotScriptPluginConfig.json**** 組態檔中指定的位置找不到前置指令碼。 |確定前置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| Post-ScriptNotFound |在 VMSnapshotScriptPluginConfig.json**** 組態檔中指定的位置找不到後置指令碼。 |確定後置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| IncorrectPluginhostFile |VmSnapshotLinux 延伸模組隨附的 Pluginhost**** 檔案已損毀，因此前置指令碼和後置指令碼無法執行，且備份為應用程式不一致。| 解除安裝 VmSnapshotLinux**** 延伸模組，它會自動與下一次備份重新安裝以解決問題。 |
| IncorrectJSONConfigFile | VMSnapshotScriptPluginConfig.json**** 檔不正確，所以前置指令碼和後置指令碼無法執行，且備份為應用程式不一致。 | 從 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載副本並再設定一次。 |
| InsufficientPermissionforPre-Script | 針對執行指令碼，「根」使用者應該是檔案擁有者，且檔案應具有 “700” 權限 (也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限)。 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| InsufficientPermissionforPost-Script | 針對執行指令碼，根使用者應該是檔案擁有者，且檔案應具有 “700” 權限 (也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限)。 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| Pre-ScriptTimeout | 執行應用程式一致備份前置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時********。 |
| Post-ScriptTimeout | 執行應用程式一致備份後置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時********。 |

## <a name="next-steps"></a>後續步驟

[設定 VM 備份至復原服務保存庫](./backup-azure-vms-first-look-arm.md)
