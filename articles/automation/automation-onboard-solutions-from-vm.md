---
title: 從 Azure VM 啟用 Azure 自動化更新管理
description: 本文說明如何從 Azure VM 啟用更新管理。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743952"
---
# <a name="enable-update-management-from-an-azure-vm"></a>從 Azure VM 啟用更新管理

本文說明如何使用 Azure VM 啟用其他機器上的[更新管理](automation-update-management.md)功能。 若要大規模啟用 Azure VM，您必須使用更新管理來啟用現有的 VM。 

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)，以管理電腦。
* [虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="enable-update-management"></a>啟用更新管理

1. 在 [Azure 入口網站](https://portal.azure.com)中選取 [虛擬機器]，或從首頁搜尋並選取 [虛擬機器]。

2. 選取您要啟用更新管理的 VM。 VM 可能存在於任何區域中，無論您的自動化帳戶位於何處。 您

3. 在 VM 頁面的 [作業] 下，選取 [更新管理]。

4. 您必須具有 `Microsoft.OperationalInsights/workspaces/read` 權限，才能判斷是否已啟用工作區的 VM。 若要了解所需的其他權限，請參閱[啟用機器所需的權限](automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何同時啟用多台機器，請參閱[從自動化帳戶啟用更新管理](automation-onboard-solutions-from-automation-account.md)。

5. 選擇 Log Analytics 工作區與自動化帳戶，然後按一下 [啟用] 以啟用更新管理。 最多需要 15 分鐘才能完成設定。 

    ![啟用更新管理](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>檢查範圍設定

更新管理會使用工作區中的範圍設定，來設定要啟用此功能的目標電腦。 範圍設定是一筆或多筆已儲存搜尋的群組，用以將此功能的範圍限定於特定電腦。 如需詳細資訊，請參閱[使用更新管理的範圍設定](automation-scope-configurations-update-management.md) (英文)。

## <a name="next-steps"></a>後續步驟

* 若要使用 VM 的更新管理，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 關於範圍設定，請參閱[使用更新管理範圍設定](automation-scope-configurations-update-management.md) (英文)。
* 如果您不再需要 Log Analytics 工作區，請參閱[從更新管理的自動化帳戶取消連結工作區](automation-unlink-workspace-update-management.md)中的指示。
* 若要從更新管理中刪除 VM，請參閱[從更新管理中移除 VM](automation-remove-vms-from-update-management.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。
