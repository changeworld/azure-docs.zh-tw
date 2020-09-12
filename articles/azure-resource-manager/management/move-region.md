---
title: 將 Azure 資源移至另一個區域
description: 提供跨 Azure 區域移動 Azure 資源的總覽。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007633"
---
# <a name="moving-azure-resources-across-regions"></a>跨區域移動 Azure 資源

本文提供在 Azure 區域之間移動 Azure 資源的相關資訊。

Azure 地理位置、區域和可用性區域形成 Azure 全球基礎結構的基礎。 Azure [地理](https://azure.microsoft.com/global-infrastructure/geographies/) 位置通常包含兩個或多個 [azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 區域是地理位置內的區域，包含可用性區域和多個資料中心。 

在特定 Azure 區域中部署資源之後，有幾個原因可能會讓您將資源移至不同的區域。

- **對應至區域啟動**：將您的資源移至先前未提供的新推出 Azure 區域。
- **服務/功能的對齊**：移動資源以利用特定區域中可用的服務或功能。
- **回應商務開發**：將資源移至區域以回應業務變更，例如合併或收購。
- **對齊鄰近**性：將資源移至您公司的本機區域。
- **符合資料需求**：移動資源以便與資料落地需求或資料分類需求一致。 [深入了解](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **回應部署需求**：移動發生錯誤的資源，或移動以回應容量需求。 
- **回應解除**委任：由於區域解除委任而移動資源。

## <a name="move-resources-with-resource-mover"></a>使用資源移動器移動資源

您可以使用 [Azure 資源移動器](../../resource-mover/overview.md)將資源移至不同的區域。 資源移動器提供：

- 跨區域移動資源的單一中樞。
- 縮短移動時間和複雜度。 您需要的所有專案都在單一位置。
- 移動不同 Azure 資源類型的簡單且一致體驗。
- 識別您想要移動之資源之間相依性的簡單方式。 這可協助您將相關的資源一起移動，如此一來，在移動之後，所有專案都能如預期般在目的地區域中運作。
- 如果您想要在移動之後將其刪除，請自動清除來源區域中的資源。
- 測試。 您可以嘗試移動，如果您不想要進行完整移動，請將它捨棄。

您可以使用幾種不同的方法，將資源移至另一個區域：

- **開始從資源群組移動資源**：使用此方法時，您會從資源群組內開始移動區域。 選取您要移動的資源之後，程式會繼續在資源移動器中樞內檢查資源相依性，並協調移動程式。 [深入了解](../../resource-mover/move-region-within-resource-group.md)。
- **開始直接從資源移動器中樞移動資源**：使用此方法時，您會直接在中樞開始區域移動程式。 [深入了解](../../resource-mover/tutorial-move-region-virtual-machines.md)。


## <a name="support-for-region-move"></a>支援區域移動

您目前可以使用資源移動器將這些資源移至另一個區域：

- Azure Vm 和相關聯的磁片
- NIC
- 可用性設定組
- Azure 虛擬網路
- 公用 IP 位址
- 網路安全性群組 (NSG)
- 內部和公用負載平衡器
- Azure SQL 資料庫和彈性集區

## <a name="region-move-process"></a>區域移動流程

跨區域移動資源的實際程式取決於您要移動的資源。 不過，有一些常見的主要步驟：

1. **確認必要條件**：必要條件包括確保您需要的資源可用於目的地區域、檢查您的配額是否足夠，以及確認您的訂用帳戶可以存取目的地區域。
2. **分析**相依性：您的資源可能相依于其他資源。 在移動之前，請先找出相依性，以便在移動之後，移動的資源仍能如預期般運作。
3. **準備移動**：這些是您在移動之前在主要區域中採取的步驟。 例如，您可能需要匯出 Azure Resource Manager 範本，或開始將資源從來源複寫到目標。
4. **移動資源**：移動資源的方式取決於它們的內容。 您可能需要在目的地區域中部署範本，或將資源容錯移轉至目標。
5. **捨棄目標資源**：移動資源之後，您可能會想要立即查看目的地區域中的資源，並決定是否有任何不需要的資源。
6. **認可移動**：確認目的地區域中的資源之後，部分資源可能需要最終的認可動作。 例如，在現在是主要區域的目的地區域中，您可能需要設定損毀修復至新的次要區域。 
7. **清除來源**：最後，在新區域中的所有專案都已啟動並執行之後，您就可以清除和解除委任您為移動而建立的資源，以及主要區域中的資源。



## <a name="next-steps"></a>接下來的步驟

[深入瞭解](../../resource-mover/about-move-process.md) 資源移動器中的移動程式。
