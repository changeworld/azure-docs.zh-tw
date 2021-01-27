---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: ac83f455d2af82c3f6970077fccb17f07e53cccc
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900827"
---
## <a name="creating-and-running-a-scan"></a>建立和執行掃描

> [!Note] 
> 以下所示的步驟和螢幕擷取畫面說明跨不同資料來源類型管理掃描的一般程序。 視您使用的資料來源類型而定，您的選項可能稍有不同。

若要建立並執行新的掃描，請執行下列動作：

1. 瀏覽到 [來源]

1. 選取您註冊的資料來源。

1. 選取 [+ 新增掃描]

1. 選取要連線到資料來源的認證。 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="設定掃描":::

1. 您可以藉由檢查清單中的適當項目，將掃描範圍限定為資料來源的特定部分，例如資料夾、集合或結構描述。

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="為您的掃描設定範圍":::

1. 為您的掃描選取掃描規則集。 您可以在系統預設值、現有自訂值之間做選擇，或線上建立新的值。

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="掃描規則集":::

1. 選擇您的掃描觸發程序。 您可以設定排程或執行一次掃描。

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="trigger":::

1. 檢閱您的掃描，然後選取 [儲存並執行]。

## <a name="viewing-your-scans-and-scan-runs"></a>檢視掃描和掃描執行

若要檢視現有的掃描，請執行下列動作：

1. 瀏覽至管理中心。 在 [來源和掃描] 區段底下選取 [資料來源]。 

2. 選取所需的資料來源。 您會看到該資料來源上的現有掃描清單。

3. 選取您有興趣檢視其結果的掃描。

4. 此頁面會顯示所有先前的掃描執行，以及每個掃描執行的計量和狀態。 也會顯示您的掃描是已排程或手動、有多少資產已套用分類、總共探索到多少資產、掃描的開始和結束時間，以及掃描持續時間總計。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>管理您的掃描 - 編輯、刪除或取消

若要管理或刪除掃描，請執行下列動作：

1. 瀏覽至管理中心。 選取 [來源和掃描] 區段底下的 [資料來源]，然後選取所需的資料來源。

2. 選取您想要管理的掃描。 您可以選取 [編輯] 來編輯掃描。

3. 您可以選取 [刪除] 來刪除掃描。 
