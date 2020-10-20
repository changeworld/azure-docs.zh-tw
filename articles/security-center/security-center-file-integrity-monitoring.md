---
title: Azure 資訊安全中心中的檔案完整性監視 |Microsoft Docs
description: 瞭解如何使用本逐步解說，在 Azure 資訊安全中心中設定檔案完整性監視 (FIM) 。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 45e173f87559368244ab7b0f90af1dbb6fc739d4
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205382"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure 資訊安全中心中的檔案完整性監視
瞭解如何使用本逐步解說，在 Azure 資訊安全中心中設定檔案完整性監視 (FIM) 。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|需要 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md)。<br>FIM 會將資料上傳到 Log Analytics 工作區。 根據您上傳的資料量，需要支付資料費用。 請參閱 [Log Analytics 定價](https://azure.microsoft.com/pricing/details/log-analytics/)以深入了解。|
|必要的角色和權限：|**工作區擁有** 者可以啟用/停用 FIM (如需詳細資訊，請參閱 [適用于 Log Analytics 的 Azure 角色](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)) 。<br>**讀者** 可以看到結果。|
|雲端：|![是 ](./media/icons/yes-icon.png) 商用雲端<br>![是 ](./media/icons/yes-icon.png) US Gov<br>![沒有 ](./media/icons/no-icon.png) 中國 Gov，其他 Gov<br>只有在 Azure 自動化的變更追蹤解決方案可用的區域中才支援。<br>請參閱 [連結的 Log Analytics 工作區支援的區域](../automation/how-to/region-mappings.md)。<br>[深入瞭解變更追蹤](../automation/change-tracking/overview.md)。|
|||

## <a name="what-is-fim-in-security-center"></a>何謂資訊安全中心的 FIM？
檔案完整性監視 (FIM) （也稱為變更監視）會檢查作業系統檔案、Windows 登錄、應用程式軟體、Linux 系統檔案，以及其他可能表示攻擊的變更。 

「安全性中心」建議使用 FIM 來監視的實體，而且您也可以定義自己的 FIM 原則或要監視的實體。 FIM 會警示您有可疑的活動，例如：

- 檔案和登錄機碼建立或移除
- 檔案修改 (檔案大小、存取控制清單和內容雜湊的變更)
- 登錄修改 (大小、存取控制清單、類型和內容的變更)

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 查看要使用 FIM 監視的建議實體清單
> * 定義您自己的自訂 FIM 規則
> * 審核受監視實體的變更
> * 使用萬用字元來簡化跨目錄的追蹤


## <a name="how-does-fim-work"></a>FIM 如何運作？

藉由比較這些專案目前的狀態與前一次掃描期間的狀態，FIM 會在發生可疑的修改時發出警示。

FIM 使用 Azure 變更追蹤解決方案來追蹤及識別您環境中的變更。 啟用檔案完整性監視時，您會有一個**方案**類型的**變更追蹤**資源。 如需資料收集頻率的詳細資料，請參閱 [變更追蹤資料收集詳細資料](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)。

> [!NOTE]
> 如果您移除 **變更追蹤** 資源，也會停用 [安全性中心] 中的 [檔案完整性監視] 功能。

## <a name="which-files-should-i-monitor"></a>我應該監視哪些檔案？
選擇要監視的檔案時，請考慮哪些檔案對您的系統和應用程式而言是重要的。 監視您不想要變更而不需要規劃的檔案。 如果您選擇應用程式或作業系統 (（例如記錄檔和文字檔）經常變更的檔案) 它會產生大量的雜訊，因此難以識別攻擊。

資訊安全中心提供下列建議專案清單，以根據已知的攻擊模式進行監視。

|Linux 檔案|Windows 檔案|Windows 登錄機碼 (HKLM = HKEY_LOCAL_MACHINE) |
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. 會議|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell 資料夾|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell 資料夾|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft 安全性 Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell 資料夾|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell 資料夾|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>啟用檔案完整性監視 

只有 Azure 入口網站中的 [安全性中心] 頁面才會提供 FIM。 目前沒有 REST API 可使用 FIM。

1. 從 **Azure Defender** 儀表板的 [ **先進保護** ] 區域中，選取 [檔案 **完整性監視**]。

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="啟動 FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    [檔案 **完整性監視** 設定] 頁面隨即開啟。

    每個工作區會提供下列資訊：

    - 過去一週發生的變更總數 (如果工作區上未啟用 FIM，您可能會看到虛線 "-")
    - 向工作區報告的電腦和 VM 總數
    - 工作區的地理位置
    - 工作區依據的 Azure 訂用帳戶

1. 使用此頁面可：

    - 存取和查看每個工作區的狀態和設定

    - ![升級計畫圖示將 ][4] 工作區升級為使用 Azure Defender。 此圖示表示工作區或訂用帳戶未受 Azure Defender 保護。 若要使用 FIM 功能，您的訂用帳戶必須受到 Azure Defender 的保護。 [深入了解](security-center-pricing.md)。

    - ![啟用圖示][3] 在工作區下的所有機器上啟用 FIM，並設定 FIM 選項。 此圖示表示工作區未啟用 FIM。

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="啟動 FIM":::


    > [!TIP]
    > 如果沒有 [啟用] 或 [升級] 按鈕，而且空間是空白的，則表示已在工作區上啟用 FIM。


1. 選取 [ **啟用**]。 將會顯示工作區的詳細資料，包括工作區下的 Windows 和 Linux 電腦數目。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="啟動 FIM":::

   也會列出 Windows 和 Linux 的建議設定。  展開 [Windows 檔案]****、[登錄]**** 和 [Linux 檔案]****，以查看完整的建議項目清單。

1. 清除任何您不想要由 FIM 監視之建議實體的核取方塊。

1. 選取 [Apply file integrity monitoring] \(套用檔案完整性監視\)**** 以啟用 FIM。

> [!NOTE]
> 您可以隨時變更設定。 請參閱下面的[編輯受監視的實體](#edit-monitored-entities)以深入了解。



## <a name="audit-monitored-workspaces"></a>Audit 受監視的工作區 

啟用 FIM 的工作區會顯示 [檔案完整性監視]**** 儀表板。 當您在工作區上啟用 FIM，或在 [檔案 **完整性監視** ] 視窗中選取已啟用 fim 的工作區之後，就會開啟 fim 儀表板。

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="啟動 FIM":::

工作區的 FIM 儀表板會顯示下列詳細資料：

- 連線到工作區的電腦總數
- 所選時段內發生的變更總數
- 變更類型的細目 (檔案、登錄)
- 變更類別的細目 (已修改、已新增、已移除)

選取儀表板頂端的 [ **篩選** ]，以變更顯示變更的時間週期。

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="啟動 FIM":::

[ **伺服器** ] 索引標籤會列出向此工作區報告的機器。 針對每部電腦，儀表板會列出：

- 所選時段內發生的變更總計
- 檔案變更或登錄變更時的變更總計細目

當您在 [搜尋] 欄位中輸入電腦名稱稱，或選取 [電腦] 索引標籤下所列的電腦時，**記錄搜尋**就會開啟。記錄搜尋會顯示在所選時段內針對電腦所做的所有變更。 您可以展開某項變更，以取得詳細資訊。

![記錄搜尋][8]

[變更]**** 索引標籤 (如下所示) 會列出工作區在所選時段內的所有變更。 針對已變更的每個實體，儀表板會列出：

- 發生變更的電腦
- 變更的類型 (登錄或檔案)
- 變更的類別 (已修改、已新增、已移除)
- 變更的日期和時間

![工作區的變更][9]

當您在搜尋欄位中輸入變更，或選取 [變更]**** 索引標籤下所列的實體時，[變更詳細資料]**** 會隨即開啟。

![變更詳細資料][10]

## <a name="edit-monitored-entities"></a>編輯受監視的實體

1. 返回 [檔案 **完整性監視] 儀表板** ，然後選取 [ **設定**]。

   ![設定][11]

   [工作區設定]**** 會隨即開啟並顯示三個索引標籤：[Windows 登錄]****、[Windows 檔案]**** 和 [Linux 檔案]****。 每個索引標籤會列出您可以在該類別中編輯的實體。 針對每個列出的實體，資訊安全中心會識別 FIM 已啟用 (True) 或未啟用 (False)。  編輯實體可讓您啟用或停用 FIM。

   ![工作區設定][12]

2. 選取身分識別保護。 在此範例中，已選取 [Windows 登錄] 下的項目。 [Edit for Change Tracking] \(編輯變更追蹤項目\)**** 會隨即開啟。

   ![編輯或變更追蹤][13]

在 [Edit for Change Tracking] \(編輯變更追蹤項目\)**** 下，您可以：

- 啟用 (True) 或停用 (False) 的檔案完整性監視
- 提供或變更實體名稱
- 提供或變更值或路徑
- 刪除實體、捨棄變更或儲存變更

## <a name="add-a-new-entity-to-monitor"></a>新增要監視的實體
1. 返回 [檔案完整性監視]**** 儀表板，然後選取頂端的 [設定]****。 [工作區設定]**** 會隨即開啟。
2. 在 [工作區設定]**** 下，選取您要新增之實體類型的索引標籤：[Windows 登錄]、[Windows 檔案] 或 [Linux 檔案]。 在此範例中，已選取 [Linux 檔案]****。

   ![新增要監視的項目][14]

3. 選取 [新增]。 [Add for Change Tracking] \(新增變更追蹤項目\)**** 會隨即開啟。

   ![輸入所要求的資訊][15]

4. 在 [新增]**** 頁面上，鍵入所要求的資訊，然後選取 [儲存]****。

## <a name="disable-monitored-entities"></a>停用受監視的實體
1. 返回 [檔案 **完整性監視** ] 儀表板。
2. 選取目前啟用 FIM 的工作區。 如果缺少 [啟用] 按鈕或 [升級計劃] 按鈕，則工作區已啟用 FIM。

   ![選取啟用 FIM 的工作區][16]

3. 在 [檔案完整性監視] 底下，選取 [ **設定**]。

   ![選取 [設定]][17]

4. 在 [工作區設定]**** 下，選取 [已啟用]**** 設定為 True 的群組。

   ![工作區設定][18]

5. 在 [Edit for Change Tracking] \(編輯變更追蹤項目\)**** 視窗下，將 [已啟用]**** 設定為 False。

   ![將 [已啟用] 設定為 False][19]

6. 選取 [儲存]。

## <a name="folder-and-path-monitoring-using-wildcards"></a>使用萬用字元監視資料夾與路徑

您可以使用萬用字元來簡化跨目錄的追蹤。 當您使用萬用字元設定資料夾監視時，適用下列規則：
-   追蹤多個檔案時需要萬用字元。
-   萬用字元只能用於路徑的最後一個區段，例如 C:\folder\file or /etc/*.conf
-   如果環境變數包含的路徑無效，驗證仍會成功，但執行清查時，該路徑將會失敗。
-   設定路徑時，請避免使用一般路徑，例如 c:\*.*，因為這會導致過多的資料夾周遊。

## <a name="disable-fim"></a>停用 FIM
您可以停用 FIM。 FIM 使用 Azure 變更追蹤解決方案來追蹤及識別您環境中的變更。 您可以停用 FIM，從所選工作區移除變更追蹤解決方案。

1. 若要停用 FIM，請返回 [檔案 **完整性監視** ] 儀表板。
2. 選取工作區。
3. 在 [檔案 **完整性監視**] 底下，選取 [ **停**用]。

   ![停用 FIM][20]

4. 選取 [移除]**** 以停用。

## <a name="next-steps"></a>後續步驟
在本文中，您已瞭解如何在安全中心 (FIM) 中使用檔案完整性監視。 若要深入了解資訊安全中心，請參閱下列頁面：

* [設定安全性原則](tutorial-security-policy.md) --瞭解如何為您的 Azure 訂用帳戶和資源群組設定安全性原則。
* [管理安全性建議](security-center-recommendations.md) - 了解建議如何協助保護您的 Azure 資源。
* [Azure 安全性部落格](https://docs.microsoft.com/archive/blogs/azuresecurity/)-- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png