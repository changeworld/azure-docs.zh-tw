---
title: 從自動化帳戶啟用 Azure 自動化更新管理
description: 此文章說明如何從自動化帳戶啟用更新管理。
services: automation
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 787338be06c2e30aabb6421a42e7cb3aaabf8a2a
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669509"
---
# <a name="enable-update-management-from-an-automation-account"></a>從自動化帳戶啟用更新管理

本文說明如何使用您的自動化帳戶來為您環境中的 Vm 啟用 [更新管理](update-mgmt-overview.md) 功能，包括向 [Azure Arc 啟用的伺服器](../../azure-arc/servers/overview.md) 註冊的電腦或伺服器 (preview) 。 若要大規模啟用 Azure Vm，您必須使用更新管理啟用現有的 Azure VM。

> [!NOTE]
> 啟用更新管理時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../index.yml)，以管理電腦。
* 在已啟用 Arc 的伺服器 (預覽) 註冊的 [Azure 虛擬機器](../../virtual-machines/windows/quick-create-portal.md)或 VM 或伺服器。 非 Azure Vm 或伺服器必須安裝適用于 Windows 或 Linux 的 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md) ，並向連結至自動化帳戶的工作區報告，更新管理已在中啟用。 您可以藉由部署具有 Azure Arc 的 [Azure Log ANALYTICS VM 擴充](../../azure-arc/servers/manage-vm-extensions.md) 功能，將代理程式安裝在已啟用 Arc 的伺服器上。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-update-management"></a>啟用更新管理

1. 在您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選擇 Log Analytics 工作區和自動化帳戶，然後選取 [ **啟用** ] 以啟用更新管理。 最多需要 15 分鐘才能完成設定。

    ![啟用更新管理](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>啟用 Azure VM

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [ **+ 新增 Azure vm** ]，然後從清單中選取一或多個 vm。 無法啟用的虛擬機器會呈現灰色且無法選取。 Azure VM 可以存在於任何區域中，而不論您的自動化帳戶位置為何。

3. 選取 [ **啟用** ]，將選取的 vm 新增至該功能的 [已儲存的電腦群組] 搜尋。

    ![啟用 Azure VM](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>啟用非 Azure VM

您必須手動加入不在 Azure 中的機器。

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [ **新增非 Azure 電腦**]。 此動作會開啟新的瀏覽器視窗，其中包含 [安裝和設定適用于 Windows 的 Log Analytics 代理程式的指示](../../azure-monitor/platform/log-analytics-agent.md) ，讓電腦可以開始向更新管理報告。 如果您要啟用目前受 Operations Manager 管理的電腦，則不需要新的代理程式。 工作區資訊會新增至代理程式設定。

## <a name="enable-machines-in-the-workspace"></a>啟用工作區中的機器

您必須將手動安裝的機器或已向您工作區報告的機器新增至 Azure 自動化，才能啟用更新管理。

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 選取 [管理機器]。 如果您先前選擇 [在所有可用及未來的機器上啟用] 選項，則 [管理機器] 按鈕可能會呈現灰色

    ![已儲存的搜尋](media/update-mgmt-enable-automation-account/managemachines.png)

3. 若要對所有向工作區報告的可用機器啟用更新管理，請在 [管理電腦] 頁面上選取 [ **在所有可用的電腦上啟用** ]。 此動作會停用個別新增機器的控制項。 此工作會將所有回報給工作區的機器名稱，新增至電腦群組儲存的搜尋查詢 `MicrosoftDefaultComputerGroup` 。 選取時，此動作會停用 [管理機器] 按鈕。

4. 若要為所有可用的機器與未來的機器啟用此功能，請選取 [在所有可用及未來的機器上啟用]。 此選項會從工作區中刪除已儲存的搜尋和範圍設定，並允許此功能包含目前或未來的所有 Azure 與非 Azure 機器，向工作區報告。 選取時，此動作會永久停用 [ **管理電腦** ] 按鈕，因為沒有可用的範圍設定。

    > [!NOTE]
    > 因為此選項會刪除 Log Analytics 內已儲存的搜尋和範圍設定，所以請務必先移除 Log Analytics 工作區上的任何刪除鎖定，然後再選取此選項。 如果沒有，此選項將無法移除設定，您必須手動將其移除。

5. 如有必要，您可以重新新增初始儲存的搜尋查詢，以新增範圍設定。 如需詳細資訊，請參閱[限制更新管理部署範圍](update-mgmt-scope-configuration.md)。

6. 若要啟用一或多部機器的功能，請選取 [ **在選取的電腦上啟用** ]，然後選取 [在每部電腦上 **新增** ]。 此工作會針對此功能，將所選取機器的名稱加入至電腦群組儲存的搜尋查詢。

## <a name="next-steps"></a>後續步驟

* 若要使用 Vm 的更新管理，請參閱 [管理 vm 的更新和修補程式](update-mgmt-manage-updates-for-vm.md)。

* 當您不再需要使用更新管理管理 Vm 或伺服器時，請參閱 [從更新管理移除 vm](update-mgmt-remove-vms.md)。

* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](../troubleshoot/update-management.md)。
