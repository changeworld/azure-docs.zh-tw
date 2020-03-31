---
title: 事件中心 - 使用 Azure 門戶捕獲流式處理事件
description: 本文說明如何才能使用 Azure 入口網站透過 Azure 事件中樞擷取事件串流。
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187444"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>允許透過 Azure 事件中樞擷取事件串流

Azure[事件中心捕獲][capture-overview]使您能夠自動將事件中心中的流式處理資料傳遞到您選擇的[Azure Blob 存儲](https://azure.microsoft.com/services/storage/blobs/)或 Azure[資料存儲第 1 代或第 2 代](https://azure.microsoft.com/services/data-lake-store/)帳戶。

您可以使用 [Azure 入口網站](https://portal.azure.com)，在建立事件中樞時設定擷取功能。 您可以將資料捕獲到 Azure Blob[存儲](https://azure.microsoft.com/services/storage/blobs/)容器，也可以捕獲到[Azure 資料湖存儲第 1 代或第 2 代](https://azure.microsoft.com/services/data-lake-store/)帳戶。

如需詳細資訊，請參閱[事件中樞擷取概觀][capture-overview]。

## <a name="capture-data-to-azure-storage"></a>將資料捕獲到 Azure 存儲

當您建立事件中樞時，按一下 [建立事件中樞]**** 入口網站畫面中的 [開啟]**** 按鈕，即可啟用擷取功能。 然後按一下 [擷取提供者]**** 方塊中的 [Azure 儲存體]****，以指定儲存體帳戶和容器。 事件中樞擷取會對儲存體使用服務對服務驗證，因此您不需要指定儲存體連接字串。 資源選擇器會自動選取儲存體帳戶的資源 URI。 如果您使用 Azure Resource Manager，您必須以字串形式明確提供此 URI。

預設時間範圍為 5 分鐘。 最小值是 1，最大值是 15。 **大小** 範圍為 10-500 MB。

![擷取的時間範圍][1]

> [!NOTE]
> 您可以啟用或停用在擷取期間未發生任何事件時發出空白檔案。 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>將資料捕獲到 Azure 資料存儲第 2 代 

1. 按照["創建存儲帳戶](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account)"一文創建 Azure 存儲帳戶。 在 **"高級**"選項卡上將**階層命名空間**設置為 **"已啟用**"，使其成為 Azure 資料湖存儲第 2 代帳戶。
2. 創建事件中心時，執行以下步驟： 

    1. 選擇 **"打開**以進行**捕獲**"。 
    2. 選擇**Azure 存儲**作為捕獲提供程式。 對於**捕獲提供程式**，您看到的**Azure 資料湖存儲**選項適用于 Azure 資料湖存儲的第 1 代。 要使用 Azure 資料湖存儲的 Gen 2，請選擇**Azure 存儲**。
    2. 選擇 **"選擇容器**"按鈕。 

        ![啟用資料湖存儲第 2 代的捕獲](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. 從清單中選擇**Azure 資料存儲湖存儲第 2 代**帳戶。 

    ![選擇資料存儲第 2 代](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. 選擇**容器**（資料湖存儲第 2 代中的檔案系統）。

    ![在存儲中選擇檔案系統](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. 在 **"創建事件中心"** 頁上，選擇 **"創建**"。 

    ![選擇"創建"按鈕](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > 使用此使用者介面 （UI） 在 Azure 資料湖存儲第 2 代中創建的容器顯示在**存儲資源管理器**中的**檔案系統**下。 同樣，您在資料存儲庫第 2 代帳戶中創建的檔案系統在此 UI 中顯示為容器。 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>將資料捕獲到 Azure 資料存儲第 1 代 

要將資料捕獲到 Azure 資料存儲第 1 代，請創建資料存儲庫第 1 代帳戶和事件中心：

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>創建 Azure 資料存儲庫存儲第 1 代帳戶和資料夾

1. 創建資料湖存儲帳戶，按照 Azure 資料存儲第[1 代使用 Azure 門戶開始](../data-lake-store/data-lake-store-get-started-portal.md)的說明創建資料湖存儲帳戶。
2. 按照"[向事件中心分配許可權](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)"部分中的說明，在"資料存儲庫 1 代"帳戶中創建一個資料夾，在其中要從事件中心捕獲資料，並將許可權分配給事件中心，以便它可以將資料寫入資料存儲第 1 代帳戶。  


### <a name="create-an-event-hub"></a>建立事件中樞

1. 事件中心必須與您創建的 Azure 資料存儲第 1 代帳戶處於相同的 Azure 訂閱中。 按一下 [建立事件中樞]**** 入口網站頁面中 [擷取]**** 之下的 [開啟]**** 按鈕，以建立事件中樞。 
2. 在 [建立事件中樞]**** 入口網站頁面中，從 [擷取提供者]**** 方塊選取 [Azure Data Lake Store]****。
3. 在 **"資料湖存儲**"下拉清單旁邊的 **"選擇存儲**"中，指定以前創建的資料湖存儲第 1 代帳戶，並在 **"資料湖路徑"** 欄位中輸入您創建的資料檔案夾的路徑。

    ![選取 Data Lake Storage 帳戶][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>在現有的事件中樞上新增或設定擷取功能

您可以在位於事件中樞命名空間的現有事件中樞上設定擷取功能。 若要在現有事件中樞上啟用擷取功能，或變更您的擷取設定，請按一下命名空間以載入概觀畫面，然後按一下您要啟用或變更擷取設定的事件中樞。 最後，按一下開啟之頁面左側的 [擷取]**** 選項，然後編輯設定，如下圖所示：

### <a name="azure-blob-storage"></a>Azure Blob 儲存體

![設定 Azure Blob 儲存體][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![配置 Azure 資料存儲第 2 代](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure 資料存儲第 1 代 

![設定 Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>後續步驟

- 閱讀[事件中樞擷取概觀][capture-overview]，深入了解事件中樞擷取功能。
- 您也可以透過 Azure Resource Manager 範本來設定事件中樞擷取。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本啟用擷取功能](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。
- [了解如何以事件中樞命名空間作為來源來建立 Azure 事件方格訂用帳戶](store-captured-data-data-warehouse.md)
- [使用 Azure 門戶開始使用 Azure 資料湖存儲](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
