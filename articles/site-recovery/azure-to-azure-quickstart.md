---
title: 使用 Azure Site Recovery 設定以次要區域為目標的 Azure VM 災害復原
description: 使用 Azure Site Recovery 服務快速設定將 Azure VM 移到其他 Azure 區域的災害復原。
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135703"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>快速入門：設定 Azure VM 到次要 Azure 區域的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續運作，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本快速入門將說明如何藉由將 Azure VM 複寫至次要 Azure 區域，來設定災害復原。 一般來說，預設設定會用來啟用複寫。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要 Azure 訂用帳戶與 VM。

- 如果您沒有包含作用中訂用帳戶的 Azure 帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 建議 VM 至少要有 1 GB 的 RAM。 [深入了解](../virtual-machines/windows/quick-create-portal.md)如何建立 VM。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-replication-for-the-azure-vm"></a>啟用 Azure VM 的複寫

下列步驟可讓您將 VM 複寫至次要位置。

1. 在 Azure 入口網站的 [首頁]   > [虛擬機器]  功能表中，選取要複寫的 VM。
1. 在 [作業]  中，選取 [災害復原]  。
1. 從 [基本資料]   > [目的地區域]  中，選取目的地區域。
1. 若要檢視複寫設定，請選取 [檢閱 + 開始複寫]  。 如果您需要變更任何預設值，請選取 [進階設定]  。
1. 若要啟動可啟用 VM 複寫的作業，請選取 [啟動複寫]  。

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="啟用複寫。":::

## <a name="verify-settings"></a>確認設定

在複寫作業完成之後，您可以檢查複寫狀態、修改複寫設定，以及測試部署。

1. 在 Azure 入口網站的功能表中，選取 [虛擬機器]  ，然後選取您已複寫的 VM。
1. 在 [作業]  中，選取 [災害復原]  。
1. 若要從 [概觀]  中檢視複寫詳細資料，請選取 [基本資訊]  。 如需詳細資訊，請參閱**健康情況和狀態**、**容錯移轉整備**和**基礎結構檢視**對應項目。

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="複寫狀態。":::

## <a name="clean-up-resources"></a>清除資源

若要停止複寫主要區域中的 VM，您必須停用複寫：

- 來源複寫設定會自動清除。
- 在複寫期間安裝在 VM 上的 Site Recovery 擴充功能不會遭到移除。
- VM 的 Site Recovery 計費會停止。

若要停用複寫，請執行下列步驟：

1. 在 Azure 入口網站的功能表中，選取 [虛擬機器]  ，然後選取您已複寫的 VM。
1. 在 [作業]  中，選取 [災害復原]  。
1. 從 [概觀]  中選取 [停用複寫]  。
1. 若要卸載 Site Recovery 擴充模組，請移至 VM 的 [設定]   > [擴充功能]  。

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="停用複寫。":::

## <a name="next-steps"></a>後續步驟

本快速入門中，您以將單一 VM 複寫到次要區域。 接下來，請為多個 Azure VM 設定複寫。

> [!div class="nextstepaction"]
> [設定適用於 Azure VM 的災害復原](azure-to-azure-tutorial-enable-replication.md)
