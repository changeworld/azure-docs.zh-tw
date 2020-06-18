---
title: 取消工作區與自動化帳戶的連結以進行更新管理
description: 本文說明如何取消 Log Analytics 工作區與自動化帳戶的連結以進行更新管理
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835779"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>取消工作區與自動化帳戶的連結以進行更新管理

您可決定不要在[更新管理](automation-update-management.md)作業期間整合自動化帳戶與 Log Analytics 工作區。 本文說明如何取消工作區與帳戶的連結。

1. 在 https://portal.azure.com 登入 Azure。

2. 移除 VM 的更新管理。 請參閱[從更新管理中移除 VM](automation-remove-vms-from-update-management.md)。

3. 如果更新管理包含舊版的 Azure SQL 監視，則設定此功能時可能已建立應該移除的自動化資產。 為了進行更新管理，可能需要移除下列不再需要的項目：

   * 更新排程 - 每個排程都有一個名稱符合所建立的更新部署。
   * 針對更新管理建立的混合式背景工作角色群組 - 每個群組的名稱都會類似於 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)。

4. 開啟自動化帳戶，然後選取左側 [相關資源] 下的 [連結的工作區]。

5. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區] 並回應提示。

   ![取消連結工作區頁面](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png)。

6. 當 Azure 自動化嘗試取消連結 Log Analytics 工作區時，即可從功能表的 [通知] 下追蹤進度。

或者，您可從 Log Analytics 工作區內，取消工作區與自動化帳戶的連結：

1. 在工作區上，選取 [相關資源] 下的 [自動化帳戶]。 
2. 在 [自動化帳戶] 頁面上，選取 [取消連結帳戶]。

## <a name="next-steps"></a>後續步驟

* 若要使用此功能，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 若要針對功能錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式錯誤進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式錯誤進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。
