---
title: 移除 Azure 自動化更新管理功能
description: 本文說明如何停止使用更新管理，以及從 Log Analytics 工作區取消連結自動化帳戶。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4b4946da9f63299c7ba2b383d6c153673595a1ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87449974"
---
# <a name="remove-update-management-from-automation-account"></a>從自動化帳戶移除更新管理

使用 Azure 自動化更新管理在您的虛擬機器上啟用更新管理之後，您可能會決定停止使用它，並從帳戶和連結的 Log Analytics 工作區中移除設定。  本文說明如何從受管理的 Vm、自動化帳戶和 Log Analytics 工作區完全移除更新管理。

## <a name="sign-into-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="remove-management-of-vms"></a>移除 Vm 的管理

移除更新管理之前，您必須先停止管理您的 Vm。 請參閱 [從更新管理移除 vm](update-mgmt-remove-vms.md) ，以從功能中將其取消註冊。

## <a name="remove-updatemanagement-solution"></a>移除 UpdateManagement 方案

您必須遵循下列步驟來完全移除更新管理，才能將自動化帳戶與工作區中斷連結。 您將從工作區中移除 **更新** 方案。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您的輸入來篩選建議。 選取 [Log Analytics]。

3. 在您的 Log Analytics 工作區清單中，選取您啟用更新管理時所選擇的工作區。

4. 選取左側的 [ **方案**]。  

5. 在解決方案清單中，選取 [ **更新] (工作區名稱) **。 在解決方案的 [ **總覽** ] 頁面上，選取 [ **刪除**]。 當系統提示您確認時，請選取 **[是]**。

## <a name="unlink-workspace-from-automation-account"></a>取消工作區與自動化帳戶的連結

1. 在 Azure 入口網站中，選取 [自動化帳戶]。

2. 開啟您的自動化帳戶，並選取 [相關資源] 底下的 [連結的工作區]。

3. 在 [ **取消連結工作區** ] 頁面上，選取 [ **取消連結工作區** ] 並回應提示。

   ![取消連結工作區頁面](media/update-mgmt-remove-feature/automation-unlink-workspace-blade.png)

當它嘗試取消連結 Log Analytics 工作區時，您可以從功能表中的 [ **通知** ] 底下追蹤進度。

或者，您可以將 Log Analytics 工作區與工作區內的自動化帳戶取消連結：

1. 在 Azure 入口網站中，選取 [Log Analytics]****。

2. 從工作區中，選取 [**相關資源**] 下的 [**自動化帳戶**]。

3. 在 [自動化帳戶] 頁面上，選取 [取消連結帳戶]。

當它嘗試取消連結自動化帳戶時，您可以從功能表中的 [ **通知** ] 底下追蹤進度。

## <a name="cleanup-automation-account"></a>清除自動化帳戶

如果更新管理已設定為支援舊版的 Azure SQL 監視，則功能的設定可能已建立您應該移除的自動化資產。 為了進行更新管理，可能需要移除下列不再需要的項目：

   * 更新排程 - 每個排程都有一個名稱符合所建立的更新部署。
   * 針對更新管理建立的混合式背景工作角色群組的命名方式類似于 *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8-26c9-4051-b6b3-227600d715c8-b6b3-26c9-4051-b6b3-227600d715c8) *。

## <a name="next-steps"></a>接下來的步驟

若要重新啟用這項功能，請參閱 [從自動化帳戶啟用更新管理](update-mgmt-enable-automation-account.md)、 [流覽 Azure 入口網站來啟用更新管理](update-mgmt-enable-portal.md)、 [從 runbook 啟用更新管理](update-mgmt-enable-runbook.md)，或 [從 Azure VM 啟用更新管理](update-mgmt-enable-vm.md)。
