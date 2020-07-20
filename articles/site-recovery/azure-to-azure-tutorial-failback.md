---
title: 使用 Azure Site Recovery 服務將 Azure VM 容錯回復至主要區域。
description: 說明如何使用 Azure Site Recovery 服務將 Azure VM 容錯回復至主要區域。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e6257311473987ee053caeade6bfe14533353b73
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134450"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>在 Azure 區域之間容錯回復 Azure VM

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何容錯回復單一 Azure VM。 在容錯移轉之後，您必須在主要區域可供使用時容錯回復到該處。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> 
> * 在次要區域中容錯回復 VM。
> * 重新保護回復到次要區域的主要 VM。
> 
> [!NOTE]
> 
> 本教學課程可協助您將一些 VM 容錯移轉到目標區域，再容錯回復到來源區域 (需要最少自訂)。 如需詳細的深入指示，請檢閱 [Azure VM 的操作指南](../virtual-machines/windows/index.yml)。

## <a name="before-you-start"></a>開始之前

* 確定 VM 的狀態為 [容錯移轉已認可]  。
* 確認主要區域可供使用，而且您可以建立及存取其中的新資源。
* 確定已啟用重新保護。

## <a name="fail-back-to-the-primary-region"></a>容錯回復至主要區域

重新保護 VM 之後，您可以視需要容錯回復到主要區域。

1. 在保存庫中，選取 [複寫的項目]  ，然後選取已受到重新保護的 VM。

    ![容錯回復到主要區域](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. 在 [複寫的項目]  中選取 VM，然後選取 [容錯移轉]  。
3. 在 [容錯移轉]  中，選取要容錯移轉的目標復原點：
    - **最新 (預設值)** ：在 Site Recovery 服務中處理所有資料，並提供最低的復原點目標 (RPO)。
    - **最近處理**：將 VM 還原到 Site Recovery 所處理的最新復原點。
    - [自訂]  ：容錯移轉至特定復原點。 此選項適合用於執行測試容錯移轉。
4. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將 DR 區域中的 VM 關機，請選取 [先將機器關機再開始容錯移轉]  。 即使關機失敗，仍會繼續容錯移轉。 
5. 在 [作業]  頁面上追蹤容錯移轉進度。
6. 容錯移轉完成後，請登入 VM 進行驗證。 您可以視需要變更復原點。
7. 驗證容錯移轉之後，請選取 [認可容錯移轉]  。 認可作業會刪除所有可用的復原點。 無法再使用 [變更復原點] 選項。
8. VM 應會顯示為已容錯移轉且已容錯回復。

    ![主要和次要區域上的 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> 對於執行 Site Recovery 延伸模組版本 9.28.x.x 以上的電腦，[更新彙總套件 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery 會在容錯回復完成且 VM 重新受到保護後，清除次要災害復原區域中的電腦。 不需要手動刪除次要區域中的 VM 和 NIC。 如果您在容錯回復之後完全停用複寫，除了 VM 和 NIC 之外，Site Recovery 還會清除災害復原區域中的磁碟。

## <a name="next-steps"></a>後續步驟

[深入了解](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)重新保護流程。
