---
title: 從 Azure 入口網站啟用 Azure 自動化更新管理
description: 本文說明如何從 Azure 入口網站啟用更新管理。
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 681c5e169acc30cc6708b56b5ba180d2729919e5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222096"
---
# <a name="enable-update-management-from-the-azure-portal"></a>從 Azure 入口網站啟用更新管理

本文說明如何瀏覽 Azure 入口網站，以啟用 VM 的[更新管理](overview.md)功能。 若要大規模啟用 Azure VM，您必須使用更新管理來啟用現有的 VM。

可用於管理 VM 的資源群組數目受限於 [Resource Manager 部署限制](../../azure-resource-manager/templates/cross-scope-deployment.md)。 Resource Manager 部署 (請勿與更新部署混淆) 受限於每個部署 5 個資源群組。 其中 2 個資源群組保留來設定 Log Analytics 工作區、自動化帳戶和相關資源。 這樣剩下三個資源群組，可供您選取由更新管理來管理。 此限制僅適用於同時的設定，而不是可由自動化功能管理的資源群組數目。

> [!NOTE]
> 啟用更新管理時，僅支援特定區域來連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../index.yml)，以管理電腦。
* [虛擬機器](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure。

## <a name="enable-update-management"></a>啟用更新管理

1. 在 Azure 入口網站中，瀏覽至 [虛擬機器]。

2. 使用核取方塊來選擇要新增至更新管理的 VM。 您一次最多可以將機器新增至三個不同的資源群組。 不論自動化帳戶在哪裡，Azure VM 可以存在於任何區域。

    ![VM 清單](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > 使用篩選控制項，從不同訂用帳戶、位置和資源群組中選取 VM。 您可以按一下頂端核取方塊，以選取清單中的所有虛擬機器。

    [![啟用更新管理](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. 選取 [ **服務** ]，然後選取 [更新管理] 功能的 [ **更新管理** ]。

4. 虛擬機器清單會加以篩選，只顯示位在相同訂用帳戶及位置中的虛擬機器。 如果您的虛擬機器位於三個以上的資源群組中，則會選取前三個資源群組。

5. 預設會選取現有的 Log Analytics 工作區和自動化帳戶。 如果您想要使用不同的 Log Analytics 工作區和自動化帳戶，請選取 [ **自** 定義]，從 [自訂設定] 頁面選取它們。 當您選擇 Log Analytics 工作區時，系統會檢查此工作區是否與自動化帳戶連結。 如果找到連結的自動化帳戶，您會看到下列畫面。 完成時，選取 [確認]****。

    [![選取工作區和帳戶](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. 如果所選的工作區未連結到自動化帳戶，您會看到下列畫面。 選取自動化帳戶，並在完成時選取 **[確定]** 。

    ![沒有工作區](media/enable-from-portal/no-workspace.png)

7. 取消選取您不想要啟用的任何虛擬機器。 無法啟用的 VM 已取消選取。

8. 選取 [ **啟用** ] 以啟用此功能。 在您啟用更新管理之後，可能需要大約15分鐘的時間，您才能從中查看更新評定。

## <a name="next-steps"></a>後續步驟

* 若要使用 Vm 的更新管理，請參閱 [管理 vm 的更新和修補程式](manage-updates-for-vm.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](../troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](../troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](../troubleshoot/update-agent-issues-linux.md)。
