---
title: 從 Azure VM 啟用 Azure 自動化更新管理
description: 本文說明如何從 Azure VM 啟用更新管理。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 481d59bd2a06103629dee88868d9e33de810fdab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204900"
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

## <a name="enable-the-feature-for-deployment"></a>啟用部署的功能

1. 在 [Azure 入口網站](https://portal.azure.com)中選取 [虛擬機器]，或從首頁搜尋並選取 [虛擬機器]。

2. 選取您要啟用更新管理的 VM。 VM 可能存在於任何區域中，無論您的自動化帳戶位於何處。 您

3. 在 VM 頁面的 [作業] 下，選取 [更新管理]。

4. 您必須具有 `Microsoft.OperationalInsights/workspaces/read` 權限，才能判斷是否已啟用工作區的 VM。 若要了解所需的其他權限，請參閱[啟用機器所需的權限](automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何同時啟用多台機器，請參閱[從自動化帳戶啟用更新管理](automation-onboard-solutions-from-automation-account.md)。

5. 選擇 Log Analytics 工作區與自動化帳戶，然後按一下 [啟用] 以啟用更新管理。 最多需要 15 分鐘才能完成設定。 

    ![啟用更新管理](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="next-steps"></a>後續步驟

* 若要使用 VM 的更新管理，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。
