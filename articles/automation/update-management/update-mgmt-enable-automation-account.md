---
title: 從自動化帳戶啟用 Azure 自動化更新管理
description: 此文章說明如何從自動化帳戶啟用更新管理。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450022"
---
# <a name="enable-update-management-from-an-automation-account"></a>從自動化帳戶啟用更新管理

此文章說明如何使用自動化帳戶，為您環境中的 VM 啟用[更新管理](update-mgmt-overview.md)功能。 若要大規模啟用 Azure VM，您必須使用更新管理來啟用現有的 VM。

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../index.yml)，以管理電腦。
* [虛擬機器](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-update-management"></a>啟用更新管理

1. 在您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選擇 Log Analytics 工作區和自動化帳戶，然後選取 [**啟用**] 以啟用更新管理。 最多需要 15 分鐘才能完成設定。

    ![啟用更新管理](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>啟用 Azure VM

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [ **+ 新增 Azure vm** ]，並從清單中選取一或多個 vm。 無法啟用的虛擬機器會呈現灰色且無法選取。 Azure VM 可以存在於任何區域中，而不論您的自動化帳戶位置為何。

3. 選取 [**啟用**]，將選取的 vm 新增至電腦群組已儲存的功能搜尋。

    ![啟用 Azure VM](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>啟用非 Azure VM

您必須手動加入不在 Azure 中的機器。

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [**新增非 Azure 電腦**]。 此動作會開啟新的瀏覽器視窗，其中包含[安裝和設定適用于 Windows 的 Log Analytics 代理程式的指示](../../azure-monitor/platform/log-analytics-agent.md)，讓電腦可以開始向更新管理報告。 如果您要啟用目前由 Operations Manager 管理的電腦，則不需要新的代理程式。 工作區資訊會新增至代理程式設定。

## <a name="enable-machines-in-the-workspace"></a>啟用工作區中的機器

您必須將手動安裝的機器或已向您工作區報告的機器新增至 Azure 自動化，才能啟用更新管理。

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [管理機器]。 如果您先前選擇 [在所有可用及未來的機器上啟用] 選項，則 [管理機器] 按鈕可能會呈現灰色

    ![已儲存的搜尋](media/update-mgmt-enable-automation-account/managemachines.png)

3. 若要為所有可用的機器啟用更新管理，可在 [管理機器] 頁面上選取 [在所有可用的機器上啟用]。 此動作會停用個別新增機器的控制項。 此工作會將向工作區回報之所有機器的名稱新增至電腦群組儲存的搜尋查詢。 選取時，此動作會停用 [管理機器] 按鈕。

4. 若要為所有可用的機器與未來的機器啟用此功能，請選取 [在所有可用及未來的機器上啟用]。 此選項會從工作區刪除已儲存的搜尋和範圍設定，並針對向工作區報告的所有 Azure 和非 Azure 機器開啟此功能。 選取時，此動作會永久停用 [管理機器] 按鈕，因為沒留下任何範圍設定。

5. 如有需要，您可以藉由重新新增初始儲存的搜尋，重新新增範圍設定。 如需詳細資訊，請參閱[限制更新管理部署範圍](update-mgmt-scope-configuration.md)。

6. 若要啟用一或多部機器的功能，請選取 [**在選取的機器上啟用**]，然後選取每部機器旁的 [**新增**]。 此工作會針對此功能，將所選取機器的名稱加入至電腦群組儲存的搜尋查詢。

## <a name="next-steps"></a>後續步驟

* 若要使用 Vm 的更新管理，請參閱[管理 vm 的更新和修補程式](update-mgmt-manage-updates-for-vm.md)。

* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](../troubleshoot/update-management.md)。