---
title: 使用 Azure Site Recovery 設定以次要區域為目標的 Azure VM 災害復原
description: 使用 Azure Site Recovery 服務快速設定將 Azure VM 移到其他 Azure 區域的災害復原。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: de4d3ce11e23d7ec4f6ad26852e7d7d01eebe590
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75780006"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>設定 Azure VM 到次要 Azure 區域的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本快速入門將說明如何藉由將 Azure VM 複寫至不同 Azure 區域，來設定災害復原。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 本文也是適用於新使用者的快速逐步解說。 其使用預設選項和最低的自訂來提供最簡單的路徑。 如需完整的逐步解說，請參閱[啟用複寫](azure-to-azure-tutorial-enable-replication.md)教學課程。

## <a name="log-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-replication-for-the-azure-vm"></a>啟用 Azure VM 的複寫

1. 在 Azure 入口網站功能表上，選取 [虛擬機器]  ，或在任何頁面上搜尋並選取 [虛擬機器]  。 選取您要複寫的 VM。
2. 在 [作業]  中，選取 [災害復原]  。
3. 在 [設定災害復原]   >  [目標區域]  中，選取您要複寫至的目標區域。
4. 在本快速入門中，接受其他預設設定。
5. 選取 [檢閱 + 開始複寫]  。 然後選取 [開始複寫]  ，以啟動作業來啟用 VM 的複寫。

   ![啟用複寫](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>確認設定

在複寫作業完成之後，您可以檢查複寫狀態、修改複寫設定，以及測試部署。

1. 在 Azure 入口網站功能表上，選取 [虛擬機器]  ，或在任何頁面上搜尋並選取 [虛擬機器]  。 選取您要驗證的 VM。
2. 在 [作業]  中，選取 [災害復原]  。

   您可以確認複寫健康情況、已建立的復原點，以及地圖上的來源和目標區域。

   ![複寫狀態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>清除資源

主要區域中的 VM 會在您停用其複寫功能時停止複寫：

- 來源複寫設定會自動清除。 於複寫期間安裝在 VM 上的 Site Recovery 擴充功能不會移除，而必須以手動方式移除。
- VM 的 Site Recovery 計費會停止。

停止複寫，如下所示︰

1. 在 Azure 入口網站功能表上，選取 [虛擬機器]  ，或在任何頁面上搜尋並選取 [虛擬機器]  。 選取您要修改的 VM。
2. 在 [災害復原]  中，選取 [停用複寫]  。

   ![停用複寫](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>後續步驟

本快速入門中，您以將單一 VM 複寫到次要區域。 現在，請嘗試使用復原方案複寫多個 Azure VM。

> [!div class="nextstepaction"]
> [設定適用於 Azure VM 的災害復原](azure-to-azure-tutorial-enable-replication.md)
