---
title: 自動更新 Azure Site Recovery 中的行動服務
description: 使用 Azure Site Recovery 複寫 Azure Vm 時，自動更新行動服務的總覽。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135849"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure 到 Azure 複寫中的行動服務自動更新

Azure Site Recovery 會使用每月發行頻率來修正任何問題，並增強現有功能或加入新功能。 若要讓服務保持最新狀態，您必須規劃每個月的修補程式部署。 若要避免與每次升級相關聯的額外負荷，您可以允許 Site Recovery 管理元件更新。

如[azure 到 azure](azure-to-azure-architecture.md)的嚴重損壞修復架構中所述，行動服務會安裝在已從一個 Azure 區域啟用複寫的所有 azure 虛擬機器（vm）上。 當您使用自動更新時，每個新版本都會更新行動服務延伸模組。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>自動更新的工作方式

當您使用 Site Recovery 來管理更新時，它會透過與保存庫相同的訂用帳戶中所建立的自動化帳戶，部署 Azure 服務所使用的全域 runbook。 每個保存庫會使用一個自動化帳戶。 針對保存庫中的每個 VM，runbook 會檢查是否有作用中的自動更新。 如果有較新版本的行動服務延伸模組可供使用，則會安裝更新。

預設 runbook 排程會在複寫 VM 地理位置的時區上午12:00，每天執行一次。 您也可以透過自動化帳戶變更 runbook 排程。

> [!NOTE]
> 從[更新彙總套件 35](site-recovery-whats-new.md#updates-march-2019)開始，您可以選擇要用於更新的現有自動化帳戶。 在更新彙總套件35之前，Site Recovery 預設會建立自動化帳戶。 當您啟用 VM 的複寫時，您只能選取此選項。 它不適用於已啟用複寫功能的 VM。 您選取的設定會套用至相同保存庫中受保護的所有 Azure Vm。

開啟自動更新不需要重新開機您的 Azure Vm 或影響進行中的複寫。

自動化帳戶中的作業計費是以一個月內使用的作業執行時間分鐘數為基礎。 作業執行需要幾秒鐘到大約一分鐘的時間，並以免費單位來涵蓋。 根據預設，500分鐘會包含為自動化帳戶的免費單位，如下表所示：

| 內含免費單位（每月） | Price |
|---|---|
| 作業執行時間500分鐘 | ₹ 0.14/minute

## <a name="enable-automatic-updates"></a>啟用自動更新

Site Recovery 可以管理延伸模組更新的方法有好幾種：

- [作為啟用複寫步驟的一部分進行管理](#manage-as-part-of-the-enable-replication-step)
- [切換保存庫內的擴充功能更新設定](#toggle-the-extension-update-settings-inside-the-vault)
- [手動管理更新](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>作為啟用複寫步驟的一部分進行管理

當您[從 vm](azure-to-azure-quickstart.md)或復原[服務保存庫](azure-to-azure-how-to-enable-replication.md)啟動 vm 的複寫時，您可以允許 Site Recovery 管理 Site Recovery 延伸模組的更新，或以手動方式進行管理。

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="延伸模組設定":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>切換保存庫內的擴充功能更新設定

1. 從復原服務保存庫，移至 [**管理**  >  **Site Recovery 基礎結構**]。
1. 在 [ **Azure 虛擬機器**延伸模組更新設定] 下的 [  >  **Extension Update Settings**  >  **允許 Site Recovery 管理**] 底下，選取 [**開啟**]。

   若要手動管理延伸模組，請選取 [**關閉**]。

1. 選取 [儲存]。

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="延伸模組更新設定":::

> [!IMPORTANT]
> 當您選擇 [**允許 Site Recovery 管理**] 時，此設定會套用至保存庫中的所有 vm。

> [!NOTE]
> 其中一個選項會通知您用於管理更新的自動化帳戶。 如果您是第一次在保存庫中使用這項功能，預設會建立新的自動化帳戶。 或者，您可以自訂設定，並選擇現有的自動化帳戶。 後續在相同保存庫中啟用複寫的所有動作都會使用先前建立的自動化帳戶。 目前，下拉式功能表只會列出與保存庫位於相同資源群組中的自動化帳戶。

### <a name="manage-updates-manually"></a>手動管理更新

1. 如果您的 Vm 上已安裝行動服務的新更新，您會看到下列通知：**新的 Site Recovery 複寫代理程式更新可供使用。按一下以安裝。**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="[複寫的項目] 視窗":::

1. 選取通知以開啟 [VM 選擇] 頁面。
1. 選擇您想要升級的 Vm，然後選取 **[確定]**。 系統會針對每個選取的 VM 啟動更新行動服務。

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="複寫的項目 VM 清單":::

## <a name="common-issues-and-troubleshooting"></a>常見的問題和疑難排解

如果自動更新發生問題，您會在保存庫儀表板的 [設定**問題**] 底下看到錯誤通知。

如果您無法啟用自動更新，請參閱下列常見的錯誤和建議的動作：

- **錯誤**：您沒有權限，無法建立 Azure 執行身分帳戶 (服務主體) 以及將參與者角色授與服務主體。

  **建議動作**：請確定已將登入的帳戶指派為參與者，然後再試一次。 如需指派許可權的詳細資訊，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)的必要許可權一節。

  若要在啟用自動更新後修正大部分的問題，請選取 [**修復**]。 如果 [修復] 按鈕無法使用，請參閱 [擴充功能更新設定] 窗格中顯示的錯誤訊息。

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="延伸模組更新設定中的 Site Recovery 服務修復按鈕":::

- **錯誤**：執行身分帳戶沒有存取復原服務資源的權限。

  **建議的動作**：刪除然後[重新建立執行身分帳戶](../automation/manage-runas-account.md)。 或者，請確定自動化執行身分帳戶的 Azure Active Directory 應用程式可以存取復原服務資源。

- **錯誤**：找不到執行身分帳戶。 下列其中一個項目已刪除或未建立：Azure Active Directory 應用程式、服務主體、角色、自動化憑證資產、自動化連線資產，或憑證與連線之間的指紋不相同。

  **建議的動作**：刪除然後[重新建立執行身分帳戶](../automation/manage-runas-account.md)。

- **錯誤**：自動化帳戶所使用的 Azure 執行身分憑證即將到期。

  針對執行身分帳戶所建立的自我簽署憑證，會從建立日期起算一年後到期。 您可以在該憑證到期前隨時更新憑證。 如果您已註冊電子郵件通知，當您需要執行動作時，也會收到電子郵件。 此錯誤會在到期日之前的兩個月內顯示，如果憑證已過期，將會變更為重大錯誤。 憑證過期後，自動更新將無法正常運作，直到您更新相同的為止。

  **建議動作**：若要解決此問題，請選取 [**修復**]，然後按一下 [**更新憑證**]。

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="續訂-cert":::

  > [!NOTE]
  > 更新憑證之後，請重新整理頁面，以顯示目前的狀態。
