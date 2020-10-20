---
title: 移除 Azure 自動化變更追蹤和清查功能
description: 本文說明如何停止使用變更追蹤和清查，以及從 Log Analytics 工作區取消連結自動化帳戶。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209584"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>從自動化帳戶移除變更追蹤和清查

當您使用 Azure 自動化變更追蹤和清查來啟用虛擬機器的管理後，您可能會決定停止使用它，並從帳戶和連結的 Log Analytics 工作區中移除設定。 本文說明如何從受管理的 Vm、自動化帳戶和 Log Analytics 工作區完全移除變更追蹤和清查。

## <a name="sign-into-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="remove-management-of-vms"></a>移除 Vm 的管理

移除變更追蹤和清查之前，您必須先停止管理您的 Vm。 請參閱 [從變更追蹤移除 vm](remove-vms-from-change-tracking.md) ，以從功能中將其取消註冊。

## <a name="remove-changetracking-solution"></a>移除變更追蹤方案

您必須遵循下列步驟來完全移除變更追蹤和清查，才能將自動化帳戶與工作區中斷連結。 您將從工作區中移除 **變更追蹤** 方案。

1. 在 Azure 入口網站中，選取 [所有服務]  。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您的輸入來篩選建議。 選取 [Log Analytics]。

2. 在您的 Log Analytics 工作區清單中，選取您啟用變更追蹤和清查時所選擇的工作區。

3. 選取左側的 [ **方案**]。  

4. 在解決方案清單中，選取 [ **變更追蹤 (工作區名稱]) **。 在解決方案的 [ **總覽** ] 頁面上，選取 [ **刪除**]。 當系統提示您確認時，請選取 **[是]**。

## <a name="unlink-workspace-from-automation-account"></a>取消工作區與自動化帳戶的連結

1. 在 Azure 入口網站中，選取 [自動化帳戶]。

2. 開啟您的自動化帳戶，並選取 [相關資源] 底下的 [連結的工作區]。

3. 在 [ **取消連結工作區** ] 頁面上，選取 [ **取消連結工作區** ] 並回應提示。

   ![取消連結工作區頁面](media/remove-feature/automation-unlink-workspace-blade.png)

當它嘗試取消連結 Log Analytics 工作區時，您可以從功能表中的 [ **通知** ] 底下追蹤進度。

或者，您可以將 Log Analytics 工作區與工作區內的自動化帳戶取消連結：

1. 在 Azure 入口網站中，選取 [Log Analytics]****。

2. 從工作區中，選取 [**相關資源**] 下的 [**自動化帳戶**]。

3. 在 [自動化帳戶] 頁面上，選取 [取消連結帳戶]。

當它嘗試取消連結自動化帳戶時，您可以從功能表中的 [ **通知** ] 底下追蹤進度。

## <a name="next-steps"></a>後續步驟

若要重新啟用這項功能，請參閱 [從自動化帳戶啟用變更追蹤和清查](enable-from-automation-account.md)、 [流覽 Azure 入口網站來啟用變更追蹤和清查](enable-from-portal.md)、 [從 runbook 啟用變更追蹤和清查](enable-from-runbook.md)，或 [從 Azure VM 啟用變更追蹤和清查](enable-from-vm.md)。
