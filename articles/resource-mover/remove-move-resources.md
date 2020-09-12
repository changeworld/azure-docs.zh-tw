---
title: 移除 Azure 資源移動器中移動集合的資源
description: 瞭解如何從 Azure 資源移動器的移動集合中移除資源。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652838"
---
# <a name="remove-resources-from-a-move-collection"></a>從移動集合中移除資源

本文說明如何從 [Azure 資源移動器](overview.md)的移動集合中移除資源。 移動集合是在 Azure 區域之間移動 Azure 資源時使用。

## <a name="remove-a-resource-portal"></a>移除資源 (入口網站) 

在資源移動器入口網站中移除，如下所示：

1. 在 [ **跨區域**] 中，選取您要從集合移除的資源 > **移除**]。

    ![要選取以移除的按鈕](./media/remove-move-resources/portal-select-resources.png)

1. 在 [ **移除資源**] 中，按一下 [ **移除**]。

    ![用來選取要從移動集合移除資源的按鈕](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a> (PowerShell) 移除資源

使用 PowerShell 將 PSDemoVM 機器從集合) 的範例中移除資源 (，如下所示：

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**預期的輸出** 
 ![從移動集合移除資源之後的輸出文字](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a> (PowerShell) 移除集合

使用 PowerShell 移除整個移動集合，如下所示：

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**預期的輸出** 
 ![移除移動集合之後的輸出文字](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>移除後的 VM 資源狀態

當您從移動集合移除 VM 資源時，會發生什麼事，取決於資源狀態，如表格中所摘要。

###  <a name="remove-vm-state"></a>移除 VM 狀態
**資源狀態** | **VM** | **網路功能**
--- | --- | --- 
**已新增至移動集合** | 從移動集合中刪除。 | 從移動集合中刪除。 
**相依性已解決/準備擱置** | 從移動集合刪除  | 從移動集合中刪除。 
**準備進行中**<br/>  (或任何其他狀態進行中)  | 刪除操作失敗，發生錯誤。  | 刪除操作失敗，發生錯誤。
**準備失敗** | 從移動集合中刪除。<br/>刪除在目的地區域中建立的任何作業，包括複本磁片。 <br/><br/> 移動期間所建立的基礎結構資源必須以手動方式刪除。 | 從移動集合中刪除。  
**起始移動暫止** | 從移動集合中刪除。<br/><br/> 刪除在目的地區域中建立的任何作業，包括 VM、複本磁片等等。  <br/><br/> 移動期間所建立的基礎結構資源必須以手動方式刪除。 | 從移動集合中刪除。
**起始移動失敗** | 從移動集合中刪除。<br/><br/> 刪除在目的地區域中建立的任何作業，包括 VM、複本磁片等等。  <br/><br/> 移動期間所建立的基礎結構資源必須以手動方式刪除。 | 從移動集合中刪除。
**認可暫止** | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 
**認可失敗** | 建議您捨棄，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。
**捨棄已完成** | 資源會回到 **起始移動暫** 止狀態。<br/><br/> 它會從移動集合中刪除，以及在目標 VM、複本磁片、保存庫等上建立的任何作業。  <br/><br/> 移動期間所建立的基礎結構資源必須以手動方式刪除。 <br/><br/> 移動期間所建立的基礎結構資源必須以手動方式刪除。 |  資源會回到 **起始移動暫** 止狀態。<br/><br/> 它會從移動集合中刪除。
**捨棄失敗** | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 之後，資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 之後，資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。
**刪除來源暫止** | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。  | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。
**刪除來源失敗** | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。 | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。

## <a name="sql-resource-state-after-removing"></a>移除後的 SQL 資源狀態

當您從移動集合移除 Azure SQL 資源時，會發生什麼事，視資源狀態而定，如表格中所摘要。

**資源狀態** | **SQL** 
--- | --- 
**已新增至移動集合** | 從移動集合中刪除。 
**相依性已解決/準備擱置** | 從移動集合刪除 
**準備進行中**<br/>  (或任何其他狀態進行中)   | 刪除操作失敗，發生錯誤。 
**準備失敗** | 從移動集合刪除<br/><br/>刪除在目的地區域中建立的任何事物。 
**起始移動暫止** |  從移動集合刪除<br/><br/>刪除在目的地區域中建立的任何事物。 SQL 資料庫此時存在，將會刪除。 
**起始移動失敗** | 從移動集合刪除<br/><br/>刪除在目的地區域中建立的任何事物。 SQL 資料庫此時存在，必須加以刪除。 
**認可暫止** | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。
**認可失敗** | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 
**捨棄已完成** |  資源會回到 **起始移動暫** 止狀態。<br/><br/> 它會從移動集合中刪除，以及在目標上建立的任何檔案，包括 SQL 資料庫。 
**捨棄失敗** | 建議您捨棄移動，以便先刪除目標資源。<br/><br/> 之後，資源會回到 **起始移動暫** 止狀態，您可以從該處繼續。 
**刪除來源暫止** | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。 
**刪除來源失敗** | 已從移動集合中刪除。<br/><br/> 它不會刪除在目的地區域中建立的任何事物。 

## <a name="next-steps"></a>接下來的步驟

請嘗試使用資源移動器將 [VM 移](tutorial-move-region-virtual-machines.md) 至另一個區域。