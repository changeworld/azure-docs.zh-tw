---
title: 從 Azure VM 讓更新管理、變更追蹤和清查解決方案上線
description: 了解如何在 Azure 虛擬機器上讓屬於 Azure 自動化一部分的更新管理、變更追蹤和清查解決方案上線。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537027"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>從 Azure 虛擬機器讓更新管理、變更追蹤和清查解決方案上線

Azure 自動化提供的解決方案可協助您管理作業系統安全性更新、追蹤變更，以及清查您的電腦上安裝的項目。 讓機器上線的方法很多。 您可以從虛擬機器、[從自動化帳戶](automation-onboard-solutions-from-automation-account.md)、[透過瀏覽多部機器](automation-onboard-solutions-from-browse.md)或藉由使用 [Runbook](automation-onboard-solutions.md) 來讓解決方案上線。 本文說明如何從 Azure 虛擬機器讓這些解決方案上線。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="enable-the-solutions"></a>啟用解決方案

首先,在 VM 上啟用一個或所有三個解決方案:

1. 在[Azure 門戶](https://portal.azure.com)中,選擇**虛擬機器**或從主頁搜尋和選擇**虛擬機器**。
2. 選擇要為其啟用解決方案的 VM。
3. 在 VM 頁上,在 **「操作」** 下,選擇 **「更新管理**、**庫存**」或 **「更改追蹤**」。 無論自動化帳戶位於何處,虛擬機都可以存在於任何區域。 從 VM 載入解決方案時,需要具有確定`Microsoft.OperationalInsights/workspaces/read`VM 是否已載入工作區的許可權。 要瞭解所需的其他權限,請參考[板載入式權限](automation-role-based-access-control.md#onboarding-permissions)。 若要了解如何讓多部機器一次上線，請參閱[讓更新管理、變更追蹤和清查解決方案上線](automation-onboard-solutions-from-automation-account.md)。

4. 選擇 Azure 紀錄分析工作區和自動化帳戶,然後按一下「**啟用」** 以啟用解決方案。 啟用解決方案最多需要 15 分鐘。

![讓更新管理解決方案上線](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. 移至其他解決方案，然後選取 [啟用]****。 日誌分析工作區和自動化帳戶下拉清單被禁用,因為這些解決方案使用與以前啟用的解決方案相同的工作區和自動化帳戶。

> [!NOTE]
> [變更追蹤] 和 [清查] 使用相同的解決方案。 當其中的某個解決方案啟用時，另一個也會啟用。

## <a name="scope-configuration"></a>範圍設定

每個解決方案都會使用工作區中的範圍設定，來鎖定會取得解決方案的電腦。 範圍設定是一或多個已儲存搜尋的群組，用以將解決方案的範圍限定於特定電腦。 要存取範圍設定:

1. 在自動化帳戶中,在 **「相關資源**」下,選擇**工作區**。 
2. 在工作區中,在**工作區數據源**下,選擇 **「範圍配置**」。
3. 如果選取工作區尚未具有更新管理或更改追蹤解決方案,則建立以下範圍設定:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    如果選取的工作區已有解決方案，則不會重新部署解決方案，也不會新增範圍設定。

4. 選擇任何配置上的橢圓(**...),** 然後按下「**編輯**」 。 
5. 在 [編輯範圍設定]**** 窗格中選取 [選取電腦群組]****。 [電腦群組]**** 窗格會顯示用來建立範圍設定的已儲存搜尋。

## <a name="saved-searches"></a>已儲存的搜尋

將電腦添加到更新管理、更改跟蹤或清單解決方案時,電腦將添加到工作區中保存的兩個搜索之一。 這兩個已儲存的搜尋是包含這些解決方案所鎖定電腦的查詢。

移至工作區。 在 [一般]**** 下，選取 [已儲存的搜尋]****。 下表會顯示這些解決方案所使用的兩個已儲存搜尋：

|名稱     |類別  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

選取任一個已儲存的搜尋，以檢視用來填入群組的查詢。 下圖顯示查詢與其結果：

![已儲存的搜尋](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>取消連結工作區

下列解決方案相依於 Log Analytics 工作區：

* [更新管理](automation-update-management.md)
* [變更追蹤](automation-change-tracking.md)
* [非工作時間啟動/停止 VM](automation-solution-vm-management.md)

如果您決定不再希望將自動化帳戶與日誌分析工作區集成,則可以直接從 Azure 門戶取消連結帳戶。  繼續之前，您必須先移除稍早所述的解決方案，否則無法進行此程序。 檢閱已匯入特定解決方案的相關文章，以了解移除解決方案所需的步驟。

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

> [!NOTE]
> 某些包含舊版 Azure SQL 監視解決方案的解決方案可能已建立自動化資產，在取消連結工作區之前，可能也需要先加以移除。

1. 從 Azure 門戶中,打開自動化帳戶,並在左側的「**相關資源**」 部分下選擇 **「連結工作區**」。

2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]****。

   ![取消連結工作區頁面](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   您會收到提示，確認您想要繼續。

3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知]**** 下追蹤進度。

若使用「更新管理」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 更新計劃 - 每個計畫將具有與您創建的更新部署相匹配的名稱。

* 為解決方案創建的混合工作組 - 每個組將命名為類似機器1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)。

若使用「於下班時間啟動/停止 VM」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 啟動及停止 VM Runbook 排程
* 啟動及停止 VM Runbook
* 變數

或者,您也可以從日誌分析工作區取消將工作區從自動化帳戶中取消連結。 在工作區上,選擇 **「相關資源**」下的 **「自動化帳戶**」。。 在"自動化帳戶"頁上,選擇 **"取消連結帳戶**"。

## <a name="clean-up-resources"></a>清除資源

從「更新管理」中移除 VM：

* 在您的 Log Analytics 工作區中，從 `MicrosoftDefaultScopeConfig-Updates` 「範圍設定」的已儲存搜尋中移除 VM。 您可以在工作區中的 [一般]**** 底下找到儲存的搜尋。
* 移除[Windows 的紀錄分析代理](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources)或[Linux 的紀錄分析代理](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)程式 。

## <a name="next-steps"></a>後續步驟

繼續進行解決方案的教學課程以了解其使用方式：

* [教學 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教學課程 - 識別 VM 上的軟體](automation-tutorial-installed-software.md)

* [教學課程 - 對 VM 的變更進行疑難排解](automation-tutorial-troubleshoot-changes.md)
