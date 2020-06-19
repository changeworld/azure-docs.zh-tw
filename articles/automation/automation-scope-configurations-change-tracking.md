---
title: 使用 Azure 自動化變更追蹤和清查的範圍設定
description: 本文說明當您使用變更追蹤和清查時，如何使用範圍設定。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832158"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>使用變更追蹤和清查的範圍設定

本文說明在 VM 上啟用[更新管理](automation-update-management.md)功能時，您可以如何使用範圍設定。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>檢查範圍設定

更新管理會使用 Log Analytics 工作區中的範圍設定，來設定要啟用更新管理的目標電腦。 範圍設定是一筆或多筆已儲存搜尋的群組，用以將此功能的範圍限定於特定電腦。 如要存取範圍設定：

1. 在 [相關資源] 底下的自動化帳戶中，選取 [工作區]。 

2. 選擇 [工作區資料來源] 底下的工作區，然後選取 [範圍設定]。

3. 如果選取的工作區尚未啟用更新管理功能，則會建立 `MicrosoftDefaultScopeConfig-ChangeTracking` 範圍設定。 

4. 如果選取的工作區已啟用此功能，則不會重新部署，也不會新增範圍設定。 

5. 選取任何範圍設定上的省略符號，然後按一下 [編輯]。 

6. 在編輯窗格中，選取 [選取電腦群組]。 電腦群組窗格會顯示用來建立範圍設定的已儲存搜尋。

## <a name="view-a-saved-search"></a>檢視已儲存的搜尋

當電腦新增至變更追蹤和清查時，也會將它新增至工作區中已儲存的搜尋。 已儲存的搜尋是包含目標電腦的查詢。

1. 瀏覽至您的 Log Analytics 工作區，並選取 [一般] 下的 [已儲存的搜尋]。 更新管理所使用的已儲存搜尋是：

    |名稱     |類別  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. 選取已儲存的搜尋來檢視用於填入群組的查詢。 下圖顯示查詢與其結果：

    ![已儲存的搜尋](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>後續步驟

* 若要使用變更追蹤和清查，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要為此功能的一般問題疑難排解，請參閱[為變更追蹤和清查問題疑難排解](troubleshoot/change-tracking.md)。