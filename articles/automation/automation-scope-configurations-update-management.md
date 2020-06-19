---
title: 使用 Azure 自動化更新管理的範圍設定
description: 本文說明當您使用更新管理時，如何使用範圍設定。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832022"
---
# <a name="work-with-scope-configurations-for-update-management"></a>使用更新管理的範圍設定

本文說明在 VM 上啟用[更新管理](automation-update-management.md)功能時，您可以如何使用範圍設定。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>檢查範圍設定

更新管理會使用 Log Analytics 工作區中的範圍設定，來設定要針對此功能啟用的目標電腦。 範圍設定是一筆或多筆已儲存搜尋的群組，用以將此功能的範圍限定於特定電腦。 如要存取範圍設定：

1. 在 [相關資源] 底下的自動化帳戶中，選取 [工作區]。 

2. 選擇 [工作區資料來源] 底下的工作區，然後選取 [範圍設定]。

3. 如果選取的工作區尚未啟用更新管理功能，則會建立 `MicrosoftDefaultScopeConfig-Updates` 範圍設定。 

4. 如果選取的工作區已啟用此功能，則不會重新部署，也不會新增範圍設定。 

5. 選取任何範圍設定上的省略符號，然後按一下 [編輯]。 

6. 在編輯窗格中，選取 [選取電腦群組]。 電腦群組窗格會顯示用來建立範圍設定的已儲存搜尋。

## <a name="view-a-saved-search"></a>檢視已儲存的搜尋

當電腦新增至更新管理時，也會將它新增至工作區中已儲存的搜尋。 已儲存的搜尋是包含目標電腦的查詢。

1. 瀏覽至您的 Log Analytics 工作區，並選取 [一般] 下的 [已儲存的搜尋]。 更新管理所使用的已儲存搜尋是：

|名稱     |類別  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

2. 選取已儲存的搜尋來檢視用於填入群組的查詢。 下圖顯示查詢與其結果：

    ![已儲存的搜尋](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>後續步驟

* 如需使用此功能，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 若要針對功能疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的錯誤疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的錯誤疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。