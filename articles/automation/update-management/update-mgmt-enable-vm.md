---
title: 從 Azure VM 啟用 Azure 自動化更新管理
description: 本文說明如何從 Azure VM 啟用更新管理。
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327781"
---
# <a name="enable-update-management-from-an-azure-vm"></a>從 Azure VM 啟用更新管理

本文說明如何使用 Azure VM 啟用其他機器上的[更新管理](update-mgmt-overview.md)功能。 若要大規模啟用 Azure VM，您必須使用更新管理來啟用現有的 VM。

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../index.yml)，以管理電腦。
* [虛擬機器](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-the-feature-for-deployment"></a>啟用此功能以進行部署

1. 在 [Azure 入口網站](https://portal.azure.com)中選取 [虛擬機器]，或從首頁搜尋並選取 [虛擬機器]。

2. 選取您要啟用更新管理的 VM。 VM 可能存在於任何區域中，無論您的自動化帳戶位於何處。 您

3. 在 [VM] 頁面的 [ **作業**] 底下，選取 [ **來賓 + 主機更新**]。

    ![從左側窗格中選取 [來賓 + 主機更新]](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. 您必須具有 `Microsoft.OperationalInsights/workspaces/read` 權限，才能判斷是否已啟用工作區的 VM。 若要了解所需的其他權限，請參閱[啟用機器所需的權限](../automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何同時啟用多台機器，請參閱[從自動化帳戶啟用更新管理](update-mgmt-enable-automation-account.md)。

5. 在 [啟用更新管理] 頁面上，選擇 Log Analytics 工作區和自動化帳戶，然後按一下 [ **啟用** ] 以啟用更新管理。 啟用更新管理之後，可能需要大約15分鐘的時間，才能從 VM 查看更新評定。

    ![啟用更新管理](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>後續步驟

* 若要使用 VM 的更新管理，請參閱[管理 Azure VM 的更新和修補程式](update-mgmt-manage-updates-for-vm.md)。

* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](../troubleshoot/update-management.md)。
