---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "68002669"
---
您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立資料庫和容器。 

1. 按一下 [資料總管] > [新增容器]。 
    
    [新增容器] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

    ![Azure 入口網站資料總管，[新增容器] 刀鋒視窗](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. 在 [新增容器] 頁面上，輸入新容器的設定。

    |設定|建議值|描述
    |---|---|---|
    |**資料庫識別碼**|工作|輸入 *Tasks* 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 /、\\、#、? 或尾端空格。 核取 [佈建資料庫輸送量] 選項，它可讓您在資料庫中的所有容器內共用佈建到資料庫的輸送量。 此選項也有助於節省成本。 |
    |**輸送量**|400|讓輸送量保持在每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。| 
    |**容器識別碼**|項目|輸入 *Items* 作為新容器的名稱。 容器識別碼與資料庫名稱具有相同的字元需求。|
    |**分割區索引鍵**| /類別| 本文中所述的範例使用 */category* 作為分割區索引鍵。 設定資料分割索引鍵可讓 Azure Cosmos DB 調整集合的大小，以符合您應用程式的儲存體和輸送量需求。 一般情況下，所選擇的資料分割索引鍵最好是有各種不同值的索引鍵，這樣的選擇會導致系統平均地對工作負載分配儲存體和要求數量。 [深入了解資料分割。](../articles/cosmos-db/partitioning-overview.md)|
    
    除了上述的設定，您可以選擇性地為容器新增 [唯一索引鍵]。 在此範例中，讓我們將欄位保留空白。 唯一索引鍵可讓開發人員在資料庫中新增一層資料完整性。 您可在建立容器時建立唯一索引鍵原則，以確保每個資料分割索引鍵一或多個值的唯一性。 若要深入了解，請參閱 [Azure Cosmos DB 中的唯一索引鍵](../articles/cosmos-db/unique-keys.md)一文。
    
    選取 [確定]。 [資料總管] 會顯示新的資料庫和容器。

