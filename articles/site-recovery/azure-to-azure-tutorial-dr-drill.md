---
title: 使用 Azure Site Recovery 執行 Azure VM 災害復原演練
description: 了解如何使用 Azure Site Recovery 服務執行 Azure VM 到次要 Azure 區域的災害復原演練。
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166184"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>執行 Azure VM 到次要 Azure 區域的災害復原演練

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本教學課程說明如何使用測試容錯移轉，執行 Azure VM 的災害復原演練 (從一個 Azure 區域到另一個區域)。 此演練會驗證不遺失資料或不停機的複寫策略，並且不會影響您的生產環境。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查必要條件
> * 執行單一 VM 測試容錯移轉

> [!NOTE]
> 本教學課程可協助您以最少的步驟執行災害復原演練。 若要深入了解有關於執行災害復原演練的各種功能，請參閱 Azure VM 的[複寫](azure-to-azure-how-to-enable-replication.md)、[網路](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)或 [疑難排解](azure-to-azure-troubleshoot-errors.md)的相關文件。

## <a name="prerequisites"></a>Prerequisites

進行本教學課程之前，請先確認下列事項：

- 在執行測試容錯移轉之前，建議您先檢查 VM 的屬性，以確定 VM 已進行災害復原的相關設定。 移至 VM 的 [作業]   > [災害復原]   > [屬性]  ，以檢視複寫和容錯移轉屬性。
- **建議您針對測試容錯移轉，使用個別的 Azure VM 網路**，而非在您啟用複寫時所設定的預設網路。
- 根據您對每個 NIC 所做的來源網路設定，您可以指定 [子網路]  、[私人 IP 位址]  、[公用 IP]  、[網路安全性群組]  或 [負載平衡器]  ，以在執行災害復原演練之前，將其連結至 [計算與網路]  中的測試容錯移轉設定底下的每個 NIC。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

此範例說明如何使用復原服務保存庫來執行 VM 測試容錯移轉。

1. 選取保存庫，並移至 [受保護的項目]   > [複寫的項目]  ，然後選取 VM。
1. 在 [測試容錯移轉]  中，選取要用於容錯移轉的復原點：
   - **最新**：在 Site Recovery 服務中處理所有資料，並提供最低的 RTO (復原時間目標)。
   - **最近處理**：將 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時無須花費時間處理資料，因此可提供較低的 RTO。
   - **最新應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]  ：容錯移轉至特定復原點。 只有當您容錯移轉單一 VM，而不是使用復原方案進行容錯移轉時，才可以使用自訂。
1. 選取次要區域中的 Azure VM 在容錯移轉之後所要連線到的目標 Azure 虛擬網路。

   > [!NOTE]
   > 如果已針對複寫的項目預先設定測試容錯移轉設定，則不會顯示用來選取 Azure 虛擬網路的下拉式功能表。

1. 若要開始進行容錯移轉，請選取 [確定]  。 若要追蹤保存庫的進度，請移至 [監視]   > [Site Recovery 作業]  ，然後並選取 [測試容錯移轉]  作業。
1. 容錯移轉完成後，複本 Azure VM 會出現在 Azure 入口網站的 [虛擬機器]  中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
1. 若要刪除測試容錯移轉期間建立的 VM，請選取複寫的項目或復原方案上的 [清除測試容錯移轉]  。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行產生容錯移轉](azure-to-azure-tutorial-failover-failback.md)
