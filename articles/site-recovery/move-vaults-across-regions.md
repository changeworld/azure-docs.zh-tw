---
title: 將 Azure Site Recovery 保存庫移至另一個區域
description: 說明如何將復原服務保存庫 (Azure Site Recovery) 移至另一個 Azure 區域
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090302"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>將復原服務保存庫和 Azure Site Recovery 組態移至另一個 Azure 區域

有許多情況會讓您想要將現有的 Azure 資源從某個區域移至另一個區域。 例如，基於管理、控管等原因，或因為公司的合併和收購。 當您移動 Azure VM 時，災害復原組態將是您可能會想要移動的相關資源之一。 

目前並沒有最佳方式可將現有的災害復原組態從某個區域移至另一個區域。 這是因為您根據來源 VM 區域設定了目標區域。 當您決定變更來源區域時，將無法重複使用目標區域既有的組態，而必須重設。 本文會定義重新設定災害復原設定並將其移至不同區域的逐步程序。

在本文件中，您將會：

> [!div class="checklist"]
> * 驗證移動的必要條件。
> * 識別 Azure Site Recovery 所使用的資源。
> * 停用複寫。
> * 刪除資源。
> * 根據 VM 的新來源區域設定 Site Recovery。

> [!IMPORTANT]
> 目前並沒有可將復原服務保存庫和災害復原組態依原狀移至不同區域的最佳方法。 本文會引導您完成停用複寫並在新的區域中加以設定的程序。

## <a name="prerequisites"></a>Prerequisites

- 請先確定您已移除並刪除災害復原設定，再嘗試將 Azure VM 移至不同區域。 

  > [!NOTE]
  > 如果 Azure VM 的新目標區域與災害復原的目標區域相同，則可以使用現有的複寫組態，並進行移動。 請遵循[將 Azure IaaS VM 移至另一個 Azure 區域](azure-to-azure-tutorial-migrate.md)中的步驟。

- 確定您做出了明智的決策，並且已告知專案關係人。 在 VM 的移動完成之前，您的 VM 將不受災害防範的保護。

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>識別 Azure Site Recovery 所使用的資源
建議您先執行此步驟，再繼續進行下一步。 在複寫 VM 時，較容易識別相關資源。

針對所要複寫的每個 Azure VM，移至 [受保護的項目]   > [複寫的項目]   > [屬性]  ，並識別下列資源：

- 目標資源群組
- 快取儲存體帳戶
- 目標儲存體帳戶 (如果是非受控磁碟型 Azure VM) 
- 目標網路


## <a name="disable-the-existing-disaster-recovery-configuration"></a>停用現有的災害復原組態

1. 移至 [復原服務保存庫]。
2. 在 [受保護的項目]   > [已複寫的項目]  中，以滑鼠右鍵按一下機器，然後選取 [停用複寫]  。
3. 針對您要移動的所有 VM 重複此步驟。

> [!NOTE]
> 行動服務不會從受保護的伺服器上解除安裝。 您必須手動加以解除安裝。 如果您打算再次保護伺服器，可以略過解除安裝行動服務。

## <a name="delete-the-resources"></a>刪除資源

1. 移至 [復原服務保存庫]。
2. 選取 [刪除]  。
3. 刪除您[先前識別](#identify-the-resources-that-were-used-by-azure-site-recovery)的所有其他資源。
 
## <a name="move-azure-vms-to-the-new-target-region"></a>將 Azure VM 移至新的目標區域

根據您的需求，依照下列文章中的步驟將 Azure VM 移至目標區域：

- [將 Azure VM 移至其他區域](azure-to-azure-tutorial-migrate.md)
- [將 Azure VM 移至可用性區域中](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>根據 VM 的新來源區域設定 Site Recovery

依照[設定 Azure VM 的災害復原](azure-to-azure-tutorial-enable-replication.md)中的步驟，為已移至新區域的 Azure VM 設定災害復原。
