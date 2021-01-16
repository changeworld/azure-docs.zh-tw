---
title: 針對 Azure 自動化更新管理問題進行疑難排解
description: 本文說明如何針對 Azure 自動化更新管理進行疑難排解及解決問題。
services: automation
ms.date: 01/13/2021
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: 55e58c92004f4f4cf4ba6a96620b4f037c80cdb4
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246259"
---
# <a name="troubleshoot-update-management-issues"></a>針對更新管理問題進行疑難排解

本文討論在您的電腦上部署更新管理功能時，可能會遇到的問題。 混合式 Runbook 背景工作角色代理程式有代理程式疑難排解員可判斷根本問題。 若要深入了解疑難排解員，請參閱[針對 Windows 更新代理程式問題進行疑難排解](update-agent-issues.md)和[針對 Linux 更新代理程式問題進行疑難排解](update-agent-issues-linux.md)。 對於其他功能部署問題，請參閱[針對功能部署問題進行疑難排解](onboarding.md)。

>[!NOTE]
>如果您在 Windows 電腦上部署更新管理時遇到問題，請開啟 Windows 事件檢視器，然後在本機電腦上的 [**應用程式和服務記錄** 檔] 底下檢查 **Operations Manager** 事件記錄檔。 尋找事件識別碼為 4502 的事件，以及包含 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` 的事件詳細資料。

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>案例： Linux 更新顯示為擱置且已安裝的變更

### <a name="issue"></a>問題

針對 Linux 機器，更新管理會顯示 [分類 **安全性** ] 和 [ **其他**] 下可用的特定更新。 但是，在電腦上執行更新排程時，例如，只安裝符合 **安全性** 分類的更新，安裝的更新會與先前比對該分類的更新子集或部分更新不同。

### <a name="cause"></a>原因

當您的 Linux 機器的作業系統更新擱置中的評定完成時，) 更新管理會使用 Linux 發行版本廠商提供的 [開放弱點和評定語言](https://oval.mitre.org/) (OVAL 來進行分類。 針對 Linux 更新以 **安全性** 或 **其他** 方式執行的分類是以表示更新解決安全性問題或弱點的 OVAL 檔案為基礎。 但是，當更新排程執行時，它會使用適當的套件管理員（例如 YUM、APT 或 ZYPPER）在 Linux 機器上執行，以進行安裝。 Linux 發行版本的套件管理員可能會有不同的機制來分類更新，其中的結果可能會與從 OVAL 檔案取得的結果不同（更新管理）。

### <a name="resolution"></a>解決方案

您可以根據發行版本的封裝管理員，手動檢查 Linux 機器、適用的更新，以及其分類。 若要瞭解您的套件管理員會將哪些更新分類為 **安全性** ，請執行下列命令。

針對 YUM，下列命令會傳回由 Red Hat 分類為 **安全性** 的非零更新清單。 請注意，在 CentOS 的案例中，它一律會傳回空的清單，而且不會進行安全性分類。

```bash
sudo yum -q --security check-update
```

針對 ZYPPER，下列命令會傳回由 SUSE 分類為 **安全性** 的非零更新清單。

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

若為 APT，下列命令會傳回一份非零的更新清單，而這些更新會分類為針對 Ubuntu Linux 散發版本的標準 **安全性** 。

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

從這份清單中，您可以執行命令 `grep ^Inst` 以取得所有擱置中的安全性更新。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>案例：您收到「無法啟用更新解決方案」錯誤

### <a name="issue"></a>問題

當您嘗試在您的自動化帳戶中啟用更新管理時，您收到下列錯誤：

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* Log Analytics 代理程式的網路防火牆需求可能未正確設定。 這種情況會導致代理程式在解析 DNS URL 時失敗。

* 更新管理目標設定不正確，且電腦未如預期般接收更新。

* 您可能也會注意到，電腦在 [合規性] 底下顯示狀態為 `Non-compliant`。 同時，[代理程式電腦分析] 會將代理程式報告為 `Disconnected`。

### <a name="resolution"></a>解決方案

* 視作業系統而定，執行 [Windows](update-agent-issues.md#troubleshoot-offline) 或 [Linux](update-agent-issues-linux.md#troubleshoot-offline) 的疑難排解員。

* 請移至[網路設定](../automation-hybrid-runbook-worker.md#network-planning)，了解必須允許哪些位址和連接埠，才能進行更新管理。  

* 檢查範圍設定問題。 [範圍設定](../update-management/scope-configuration.md)會判斷哪些電腦已針對更新管理進行設定。 如果您的電腦顯示在您的工作區中，但不在更新管理中，您必須將範圍設定設為以機器為目標。 若要了解範圍設定，請參閱[在工作區中啟用電腦](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)。

* 依照[從內部部署 Windows 電腦移除混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker)或[從內部部署 Linux 電腦移除混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)中的步驟，來移除背景工作角色設定。

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>案例：已取代的更新在更新管理中表示為遺失

### <a name="issue"></a>問題

即使舊的更新已被取代，但是針對自動化帳戶顯示為遺失。 已取代的更新是您不需要安裝的更新，因為有可以修正相同弱點的後續更新可供使用。 更新管理會忽略已取代的更新，使其不適用於取代更新。 如需相關問題的詳細資訊，請參閱[已取代更新](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

已取代的更新不會在 Windows Server Update Services (WSUS) 中拒絕，因此可視為不適用。

### <a name="resolution"></a>解決方案

當被取代的更新變成100% 不適用時，您應該在 WSUS 中將該更新的核准狀態變更為 `Declined` 。 若要變更所有更新的核准狀態：

1. 在自動化帳戶中，選取 [更新管理] 來檢視電腦狀態。 請參閱[檢視更新評估](../update-management/view-update-assessments.md)。

2. 檢查已取代的更新，確定其為 100% 不適用。

3. 在電腦報告的 WSUS 伺服器上， [拒絕更新](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates)。

4. 選取 [電腦]，然後在 [合規性] 資料行中，強制重新掃描合規性。 請參閱 [管理 vm 的更新](../update-management/manage-updates-for-vm.md)。

5. 針對其他已取代的更新重複上述步驟。

6. 針對 Windows Server Update Services (WSUS) ，請清除所有被取代的更新，以使用 WSUS [伺服器清除嚮導](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)重新整理基礎結構。

7. 定期重複此程序來更正顯示問題，並將用於更新管理的磁碟空間量降到最低。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>案例：電腦未顯示於更新管理下的入口網站中

### <a name="issue"></a>問題

您的電腦具有下列徵兆：

* 您的電腦從 VM 的更新管理檢視中顯示 `Not configured`。

* 您 Azure 自動化帳戶的更新管理檢視遺漏您的電腦。

* 您的電腦在 [合規性] 底下顯示為 `Not assessed`。 不過，您會在 Azure 監視器記錄中看到混合式 Runbook 背景工作角色的活動訊號資料，但是不會看到更新管理的活動訊號資料。

### <a name="cause"></a>原因

此問題是由本機設定問題或範圍設定的設定不正確所造成。 可能的特定原因有：

* 您可能必須重新註冊並重新安裝混合式 Runbook 背景工作角色。

* 您可能已在工作區中定義已達到的配額，因而導致無法進一步儲存資料。

### <a name="resolution"></a>解決方案

1. 視作業系統而定，執行 [Windows](update-agent-issues.md#troubleshoot-offline) 或 [Linux](update-agent-issues-linux.md#troubleshoot-offline) 的疑難排解員。

2. 請確定您的電腦會向正確的工作區進行報告。 如需有關如何驗證此方面的指引，請參閱 [驗證代理程式與 Azure 監視器的連接](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor)。 也請確定此工作區已連結至您的 Azure 自動化帳戶。 若要確認，請移至您的自動化帳戶，然後選取 [相關資源] 底下的 [連結的工作區]。

3. 請確定電腦出現在與您的自動化帳戶連結的 Log Analytics 工作區中。 在 Log Analytics 工作區中執行下列查詢。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    如果您在查詢結果中看不到您的電腦，則其最近未簽入。 可能是本機設定問題，您應該[重新安裝代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

    如果您的電腦已列在查詢結果中，請在 [ **方案** ] 屬性底下，確認已列出 **更新** 。 這會確認它已向更新管理註冊。 如果不是，請檢查範圍設定問題。 [範圍設定](../update-management/scope-configuration.md)會判斷哪些電腦已針對更新管理進行設定。 若要設定電腦目標的範圍設定，請參閱在 [工作區中啟用電腦](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)。

4. 在您的工作區中，執行此查詢。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   如果您收到 `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 的結果，表示已達到在您的工作區上定義的配額，因而停止儲存資料。 在您的工作區中，移至 [使用量和估計成本] 底下的 [資料量管理]，然後變更或移除配額。

5. 如果您的問題仍然未解決，請遵循[部署 Windows 混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md)中的步驟，重新安裝適用於 Windows 的混合式背景工作角色。 針對 Linux，請遵循[部署 Linux 混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md)中的步驟。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>案例：無法為訂用帳戶註冊自動化資源提供者

### <a name="issue"></a>問題

當您在自動化帳戶中使用功能部署時，會發生下列錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

自動化資源提供者未在訂用帳戶中註冊。

### <a name="resolution"></a>解決方案

若要註冊自動化資源提供者，請在 Azure 入口網站中執行下列步驟。

1. 在入口網站底部的 Azure 服務清單中，選取 [所有服務]，然後選取 [一般] 服務群組中的 [訂用帳戶]。

2. 選取您的訂用帳戶。

3. 在 [設定] 下，選取 [資源提供者]。

4. 從資源提供者清單中，確認已註冊 Microsoft.Automation 資源提供者。

5. 如果未列出，請遵循[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)中的步驟來註冊 Microsoft.Automation 提供者。

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>案例：具有動態排程的排程更新遺漏一些電腦

### <a name="issue"></a>問題

更新預覽中包含的電腦不會全部出現在排程執行期間修補的電腦清單中。

### <a name="cause"></a>原因

此問題有下列其中一種原因：

* 未針對已註冊的自動化資源提供者設定在動態查詢範圍中定義的訂用帳戶。

* 執行排程時，電腦無法使用或沒有適當的標記。

### <a name="resolution"></a>解決方案

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>未針對已註冊的自動化資源提供者設定訂用帳戶

如果您的訂用帳戶未針對自動化資源提供者進行設定，您就無法查詢或提取該訂用帳戶中電腦的資訊。 使用下列步驟來驗證訂用帳戶的註冊。

1. 在 [Azure 入口網站](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中，存取 Azure 服務清單。

2. 選取 [所有服務]，然後選取 [一般] 服務群組中的 [訂用帳戶]。

3. 尋找在您的部署範圍中定義的訂用帳戶。

4. 在 [設定] 下，選擇 [資源提供者]。

5. 確認已註冊 Microsoft.Automation 資源提供者。

6. 如果未列出，請遵循[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)中的步驟來註冊 Microsoft.Automation 提供者。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>執行排程時電腦無法使用或未正確標記

如果您的訂用帳戶已針對自動化資源提供者進行設定，但是以指定[動態群組](../update-management/configure-groups.md)執行更新排程時遺漏一些電腦，請使用下列程序。

1. 在 Azure 入口網站中，開啟自動化帳戶，然後選取 [更新管理]。

2. 檢查[更新管理歷程記錄](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)，以判斷執行更新部署的確切時間。

3. 對於您懷疑遭到更新管理遺漏的電腦，請使用 Azure Resource Graph (ARG) [尋找電腦變更](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)。

4. 在執行更新部署之前，搜尋長時間 (例如一天) 的變更。

5. 檢查搜尋結果，在這段期間內電腦是否有系統變更，例如刪除或更新變更。 這些變更可以改變電腦狀態或標記，讓電腦在部署更新時，不會在電腦清單中選取。

6. 視需要調整電腦和資源設定，以修正電腦狀態或標記問題。

7. 重新執行更新排程，以確保具有指定動態群組的部署包含所有電腦。

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>案例：預期的電腦不會針對動態群組顯示在預覽中

### <a name="issue"></a>問題

動態群組所選範圍的 VM 不會顯示在 Azure 入口網站預覽清單中。 這份清單是由所選範圍 ARG 查詢所擷取的所有電腦組成。 系統會針對已安裝混合式 Runbook 背景工作角色，且您有存取權限的電腦篩選範圍。

### <a name="cause"></a>原因

以下是此問題的可能原因：

* 您在選取的範圍上沒有正確的存取權。
* ARG 查詢未擷取預期的電腦。
* 混合式 Runbook 背景工作角色未安裝在電腦上。

### <a name="resolution"></a>解決方案 

#### <a name="incorrect-access-on-selected-scopes"></a>選取範圍的存取權不正確

Azure 入口網站只會顯示您在給定範圍中具有寫入存取權的電腦。 如果您沒有範圍的正確存取權，請參閱 [教學課程：使用 Azure 入口網站為使用者授與 Azure 資源的存取權](../../role-based-access-control/quickstart-assign-role-user-portal.md)。

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG 查詢未傳回預期的電腦

請遵循下列步驟來找出您的查詢是否正確運作。

1. 在 Azure 入口網站的 [Resource Graph 總管] 刀鋒視窗中，執行格式如下的 ARG 查詢。 當您在更新管理中建立動態群組時，此查詢會模擬您選取的篩選。 請參閱[搭配更新管理使用動態群組](../update-management/configure-groups.md)。

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   範例如下：

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. 檢查您要尋找的電腦是否列在查詢結果中。

3. 如果未列出電腦，在動態群組中選取的篩選可能有問題。 視需要調整群組設定。

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>混合式 Runbook 背景工作角色未安裝在電腦上

電腦出現在 ARG 查詢結果中，但是仍然未顯示在動態群組預覽中。 在此情況下，電腦可能不會指定為混合式背景工作角色，因此無法執行 Azure 自動化和更新管理作業。 若要確保您預期看到的電腦已設定為混合式 Runbook 背景工作角色：

1. 在 Azure 入口網站中，移至未正確顯示電腦的自動化帳戶。

2. 在 [程序自動化] 底下選取 [混合式背景工作角色群組]。

3. 選取 [系統混合式背景工作角色群組] 索引標籤。

4. 驗證該電腦的混合式背景工作角色是否存在。

5. 如果電腦未設定為混合式背景工作角色，請使用[使用混合式 Runbook 背景工作角色將資料中心內或雲端的資源自動化](../automation-hybrid-runbook-worker.md)中的指示，以進行調整。

6. 將電腦加入混合式 Runbook 背景工作角色群組。

7. 針對尚未顯示在預覽中的所有電腦，重複上述步驟。

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>案例：已啟用更新管理元件，而 VM 會繼續顯示為已設定

### <a name="issue"></a>問題

部署開始之後 15 分鐘，您會繼續在 VM 上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 正在封鎖與自動化帳戶的通訊。

* 有一個重複的電腦名稱具有不同的來源電腦識別碼。 當具有特定電腦名稱的 VM 是在不同的資源群組中建立，並回報給訂用帳戶中的相同 Log Analytics 代理程式工作區時，就會發生此情況。

* 正在部署的 VM 映像可能來自於未使用系統準備 (sysprep) 做準備，且已安裝適用於 Windows 的 Log Analytics 代理程式的複製電腦。

### <a name="resolution"></a>解決方案

若要協助判斷 VM 的確切問題，請在與您的自動化帳戶連結的 Log Analytics 工作區中執行下列查詢。

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>封鎖與自動化帳戶的通訊

請移至[網路規劃](../update-management/overview.md#ports)，了解必須允許哪些位址和連接埠，才能進行更新管理。

#### <a name="duplicate-computer-name"></a>重複的電腦名稱

重新命名您的 VM，以確保在其環境中是唯一的名稱。

#### <a name="deployed-image-from-cloned-machine"></a>已從複製的電腦部署映像

如果您使用複製的映像，則不同的電腦名稱會有相同的來源電腦識別碼。 在此案例中：

1. 在您的 Log Analytics 工作區中，如果顯示，則從 `MicrosoftDefaultScopeConfig-Updates` 範圍設定的已儲存搜尋中移除 VM。 您可以在工作區中的 [一般] 底下找到儲存的搜尋。

2. 執行下列 Cmdlet。

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 執行 `Restart-Service HealthService` 以重新啟動健全狀況服務。 這項作業會重新建立金鑰，並產生新的 UUID。

4. 如果此方法無法運作，請先在映射上執行 sysprep，然後再安裝適用于 Windows 的 Log Analytics 代理程式。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>案例：您在另一個 Azure 租用戶中建立電腦的更新部署時，發生連結訂用帳戶錯誤

### <a name="issue"></a>問題

您在另一個 Azure 租用戶中嘗試為電腦建立更新部署時，遇到下列錯誤：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

當您建立的更新部署中有 Azure VM 位在某個更新部署中的其他租用戶內，就會發生此錯誤。

### <a name="resolution"></a>解決方案

使用以下因應措施將這些項目安排妥當。 您可以使用 [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) Cmdlet 搭配 `ForUpdateConfiguration` 參數來建立排程。 然後，使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) Cmdlet，並將其他租用戶中的電腦傳遞至 `NonAzureComputer` 參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>案例：無法解釋的重新開機

### <a name="issue"></a>問題

即使您已將 [重新開機控制] 選項設定為 [永不重新開機]，在安裝更新之後，電腦仍會重新開機。

### <a name="cause"></a>原因

Windows Update 可以由數個登錄機碼修改，其中任何一個都可以修改重新開機行為。

### <a name="resolution"></a>解決方案

檢閱[藉由編輯登錄來設定自動更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)和[用來管理重新啟動的登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)底下列出的登錄機碼，以確定您的電腦已正確設定。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>案例：更新部署中的電腦顯示「無法啟動」

### <a name="issue"></a>問題

電腦會顯示 `Failed to start` 狀態。 當您檢視電腦的特定詳細資料時，您會看到下列錯誤：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* 電腦不再存在。
* 電腦已關閉且無法連線。
* 電腦發生網路連線問題，因此無法連線到電腦上的混合式背景工作角色。
* 已變更來源電腦識別碼的 Log Analytics 代理程式更新。
* 如果您在自動化帳戶中達到200個並行作業的限制，則會對您的更新執行進行節流處理。 系統會將每個部署視為一項作業，而更新部署中的每部電腦會計算為一項作業。 目前在您的自動化帳戶中執行的任何其他自動化作業或更新部署，都會計入並行作業限制。

### <a name="resolution"></a>解決方案

適用時，請針對您的更新部署使用[動態群組](../update-management/configure-groups.md)。 此外，您可以執行下列步驟。

1. 確認您的電腦或伺服器符合 [需求](../update-management/overview.md#system-requirements)。
2. 使用混合式 Runbook 背景工作角色代理程式疑難排解員驗證與混合式 Runbook 背景工作角色的連線。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>案例：在未部署的情況下安裝更新

### <a name="issue"></a>問題

當您在更新管理中註冊 Windows 電腦時，您會看到在未部署的情況下安裝更新。

### <a name="cause"></a>原因

在 Windows 上，更新會在可用時立即自動安裝。 如果您未排程將更新部署到電腦上，這種行為可能會造成混淆。

### <a name="resolution"></a>解決方案

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 登錄機碼預設為 4 的設定：`auto download and install`。

針對更新管理用戶端，我們建議您將此機碼設為 3：`auto download but do not auto install`。

如需詳細資訊，請參閱[設定自動更新](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

電腦已部署至另一個工作區以進行「更新管理」。

### <a name="resolution"></a>解決方案

1. 依照[電腦未顯示於更新管理下的入口網站中](#nologs)底下的步驟，以確定電腦回報至正確的工作區。
2. 藉由[刪除混合式 Runbook 群組](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)來清除電腦上的成品，然後再試一次。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>案例：電腦無法與服務進行通訊

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>原因

Proxy、閘道或防火牆可能封鎖網路通訊。

### <a name="resolution"></a>解決方案

檢閱您的網路並確定已允許適當的連接埠和位址。 如需「更新管理」和「混合式 Runbook 背景工作角色」所需的連接埠和位址清單，請參閱[網路需求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合式 Runbook 背景工作角色無法產生自我簽署憑證。

### <a name="resolution"></a>解決方案

確認系統帳戶具有 **C:\ProgramData\Microsoft\Crypto\RSA** 資料夾的讀取存取權，然後再試一次。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>案例：排定更新因為 MaintenanceWindowExceeded 錯誤而失敗

### <a name="issue"></a>問題

更新的預設維護期間是 120 分鐘。 您可以將維護期間增加到最多 6 小時或 360 分鐘。

### <a name="resolution"></a>解決方案

若要瞭解在更新執行期間，在更新執行期間發生這種情況，請檢查執行中受影響機器的 [工作輸出](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) 。 您可能可以從電腦找到可供研究和採取動作的特定錯誤訊息。  

編輯任何失敗的排程更新部署，並增加維護期間。

如需維護期間的詳細資訊，請參閱[安裝更新](../update-management/deploy-updates.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>案例：電腦顯示為 [未評估] 並顯示 HRESULT 例外狀況

### <a name="issue"></a>問題

* 您有在 [合規性] 底下顯示為 `Not assessed` 的電腦，並在其下方看到例外狀況訊息。
* 您會在入口網站中看到 HRESULT 錯誤碼。

### <a name="cause"></a>原因

更新代理程式 (Windows 上的 Windows Update 代理程式、Linux 發行版本的套件管理員) 未正確設定。 「更新管理」依賴電腦的更新代理程式提供所需的更新、修補程式的狀態，以及所部署修補程式的結果。 若無此資訊，「更新管理」無法正確回報是否需要或安裝了修補程式。

### <a name="resolution"></a>解決方案

嘗試在電腦本機上執行更新。 如果此作業失敗，通常表示有更新代理程式設定錯誤。

此問題通常是因為網路設定和防火牆問題所造成。 使用下列檢查來更正此問題。

* 針對 Linux，請檢查適當的文件，確定您可以連線到套件存放庫的網路端點。

* 針對 Windows，請檢查您的代理程式設定，如[更新不是從內部網路端點 (WSUS/SCCM) 下載](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)中所列。

  * 如果電腦已針對 Windows Update 進行設定，請確定您可以連線到[與 HTTP/Proxy相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中所述的端點。
  * 如果電腦針對 Windows Server Update Services (WSUS) 進行設定，請確定您可以連線到 [WUServer 登錄機碼](/windows/deployment/update/waas-wu-settings)所設定的 WSUS 伺服器。

如果您看到 HRESULT，請按兩下以紅色顯示的例外狀況，以查看整個例外狀況訊息。 檢閱下表以了解可能解決方案或建議動作。

|例外狀況  |解決方案或動作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在 [Windows 更新錯誤碼清單](https://support.microsoft.com/help/938205/windows-update-error-code-list)中搜尋相關的錯誤碼，以尋找有關例外狀況原因的額外詳細資料。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這些項目指出是網路連線問題。 請確定您的電腦有網路連線能力來連線到「更新管理」。 如需必要連接埠和位址清單，請參閱[網路規劃](../update-management/overview.md#ports)一節。        |
|`0x8024001E`| 更新作業未完成，因為正在關閉服務或系統。|
|`0x8024002E`| Windows Update 服務已停用。|
|`0x8024402C`     | 如果您使用 WSUS 伺服器，請確定 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 登錄機碼底下 `WUServer` 和 `WUStatusServer` 的登錄值指定正確的 WSUS 伺服器。        |
|`0x80072EE2`|有網路連線問題或與已設定的 WSUS 伺服器交談時遇到問題。 檢查 WSUS 設定，並確定可從用戶端存取服務。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 確定 Windows Update 服務 (wuauserv) 正在執行而未停用。        |
|`0x80070005`| 拒絕存取錯誤可能是下列任何一項所造成：<br> 受感染的電腦<br> Windows Update 設定未正確設定<br> %WinDir%\SoftwareDistribution 資料夾的檔案權限錯誤<br> 系統磁碟機 (C:) 的磁碟空間不足。
|任何其他一般例外狀況     | 搜尋網際網路來尋找可能的解決方案，並與您當地的 IT 支援人員合作。         |

檢閱 **%Windir%\Windowsupdate.log** 檔案也可以協助您判斷可能原因。 如需如何讀取記錄的詳細資訊，請參閱[如何讀取 Windowsupdate.log 檔案](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)。

您也可以下載並執行 [Windows Update 疑難排解員](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以檢查電腦上的 Windows Update 是否有任何問題。

> [!NOTE]
> [Windows Update 疑難排解員](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)文件指出其用於 Windows 用戶端，但是也適用於 Windows Server。

## <a name="scenario-update-run-returns-failed-status-linux"></a>案例：更新執行傳回「失敗」狀態 (Linux)

### <a name="issue"></a>問題

更新執行開始，但在執行時發生錯誤。

### <a name="cause"></a>原因

可能的原因：

* 套件管理員狀況不良。
* 更新代理程式 (適用於 Windows 的 WUA，Linux 的發行版本特定套件管理員) 設定錯誤。
* 特定套件干擾雲端式修補。
* 無法連線到電腦。
* 更新有無法解析的相依性。

### <a name="resolution"></a>解決方案

如果在更新執行成功開始後，於更新執行期間發生失敗，請從來自該執行中受影響電腦[檢查作業輸出](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)。 您可能可以從電腦找到可供研究和採取動作的特定錯誤訊息。 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

如果在作業失敗之前立即看到特定的修補程式、套件或更新，您可以嘗試從下一個更新部署中[排除](../update-management/deploy-updates.md#schedule-an-update-deployment)這些項目。 若要從 Windows Update 收集記錄資訊，請參閱 [Windows Update 記錄檔](/windows/deployment/update/windows-update-logs)。

如果您無法解決修補問題，請在下一次更新部署開始之前，建立 **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** 檔案的複本，並且保留以供疑難排解之用。

## <a name="patches-arent-installed"></a>未安裝修補程式

### <a name="machines-dont-install-updates"></a>電腦未安裝更新

請嘗試直接在機器上執行更新。 如果電腦無法套用更新，請參閱[疑難排解指南中的潛在錯誤清單](#hresult)。

如果在本機執行更新，請遵循[從更新管理中移除 VM](../update-management/remove-vms.md) 的指引，來嘗試移除電腦上的代理程式，並將其重新安裝。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道有可用的更新，但在我的電腦上並未顯示為可用更新

這通常會發生在電腦設定為從 WSUS 或 Microsoft Endpoint Configuration Manager 取得更新，但是 WSUS 和 Configuration Manager 尚未核准更新。

您可以對[本文件的「藉由編輯登錄來設定自動更新」一節](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)中的登錄機碼交互參照 `UseWUServer` 登錄機碼，藉以檢查機器是否設定了 WSUS 和 SCCM。

如果未在 WSUS 中核准更新，就不會安裝。 您可以藉由執行下列查詢，在 Log Analytics 中檢查未核准的更新。

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新會顯示為已安裝，但我在電腦上找不到這些更新

更新常會被其他更新取代。 如需詳細資訊，請參閱 Windows Update 疑難排解指南中的[更新已被取代](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 這些限制記載在[更新管理概觀頁面](../update-management/overview.md#linux)上。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 持續遺漏

KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 會每日更新。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個管道以取得其他支援。

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
