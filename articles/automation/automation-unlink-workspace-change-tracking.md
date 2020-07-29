---
title: 取消工作區與自動化帳戶的連結，以進行變更追蹤和清查
description: 此文章說明如何從自動化帳戶取消連結 Log Analytics 工作區，以進行變更追蹤和清查
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828248"
---
# <a name="unlink-workspace-from-automation-account"></a>取消工作區與自動化帳戶的連結

啟用[變更追蹤和清查](change-tracking.md)作業時，您可以決定不要整合自動化帳戶與 Log Analytics 工作區。 此文章會告訴您如何將工作區與您的帳戶取消連結。

1. 在 https://portal.azure.com 登入 Azure。

2. 移除 VM 的更新管理。 請參閱[從變更追蹤和清查中移除 VM](automation-remove-vms-from-change-tracking.md)。

3. 如果變更追蹤和清查包含舊版的 Azure SQL 監視，則功能的設定可能已建立您應該移除的自動化資產。 找出這些資產並加以移除。

4. 開啟您的自動化帳戶，並選取 [相關資源] 底下的 [連結的工作區]。

5. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區] 並回應提示。

   ![取消連結工作區頁面](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png)。

6. 當 Azure 自動化嘗試取消連結 Log Analytics 工作區時，您可以從功能表的 [通知] 下追蹤進度。

或者，您可以將 Log Analytics 工作區與工作區內的自動化帳戶取消連結：

1. 在您的工作區上，選取 [相關資源] 底下的 [自動化帳戶]。 
2. 在 [自動化帳戶] 頁面上，選取 [取消連結帳戶]。

## <a name="next-steps"></a>後續步驟

* 若要使用變更追蹤和清查，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要針對一般功能問題進行疑難排解，請參閱[針對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
