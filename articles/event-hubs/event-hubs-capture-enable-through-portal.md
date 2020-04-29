---
title: 事件中樞-使用 Azure 入口網站來捕捉串流事件
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77187444"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>允許透過 Azure 事件中樞擷取事件串流

Azure[事件中樞 Capture][capture-overview]可讓您將事件中樞中的串流資料自動傳遞給您選擇的[azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)或[Azure Data Lake Storage Gen1 或 Gen 2](https://azure.microsoft.com/services/data-lake-store/)帳戶。

您可以使用 [Azure 入口網站](https://portal.azure.com)，在建立事件中樞時設定擷取功能。 您可以將資料捕獲到 Azure [Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)容器或[Azure Data Lake Storage Gen 1 或 gen 2](https://azure.microsoft.com/services/data-lake-store/)帳戶。

如需詳細資訊，請參閱[事件中樞擷取概觀][capture-overview]。

## <a name="capture-data-to-azure-storage"></a>將資料捕獲到 Azure 儲存體

當您建立事件中樞時，按一下 [建立事件中樞]**** 入口網站畫面中的 [開啟]**** 按鈕，即可啟用擷取功能。 然後按一下 [擷取提供者]**** 方塊中的 [Azure 儲存體]****，以指定儲存體帳戶和容器。 事件中樞擷取會對儲存體使用服務對服務驗證，因此您不需要指定儲存體連接字串。 資源選擇器會自動選取儲存體帳戶的資源 URI。 如果您使用 Azure Resource Manager，您必須以字串形式明確提供此 URI。

預設時間範圍為 5 分鐘。 最小值是 1，最大值是 15。 **大小** 範圍為 10-500 MB。

![擷取的時間範圍][1]

> [!NOTE]
> 您可以啟用或停用在擷取期間未發生任何事件時發出空白檔案。 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>將資料捕獲到 Azure Data Lake Storage Gen 2 

1. 遵循[建立儲存體帳戶一](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account)文，以建立 Azure 儲存體帳戶。 將 [ **Advanced** ] 索引標籤上的 [**階層命名空間**] 設定為 [**啟用**]，使其成為 Azure Data Lake Storage Gen 2 帳戶
2. 建立事件中樞時，請執行下列步驟： 

    1. 針對 [ **Capture**] 選取 [ **On** ]。 
    2. 選取 [ **Azure 儲存體**] 作為 [capture 提供者]。 您針對**Capture 提供者**所看到的**Azure Data Lake 存放區**選項，適用于 Azure Data Lake Storage 的 Gen 1。 若要使用 Azure Data Lake Storage 的 Gen 2，請選取 [ **Azure 儲存體**]。
    2. 選取 [**選取容器**] 按鈕。 

        ![啟用 capture to Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. 從清單中選取 [ **Azure Data Lake Storage Gen 2** ] 帳戶。 

    ![選取 Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. 選取**容器**（Data Lake Storage Gen 2 中的檔案系統）。

    ![選取儲存體中的檔案系統](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. 在 [**建立事件中樞**] 頁面上，選取 [**建立**]。 

    ![選取 [建立] 按鈕](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > 您在使用此使用者介面（UI）的 Azure Data Lake Storage Gen 2 中建立的容器，會顯示在**儲存體總管**的 [**檔案系統**] 之下。 同樣地，您在 Data Lake Storage Gen 2 帳戶中建立的檔案系統會顯示為此 UI 中的容器。 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>將資料捕獲到 Azure Data Lake Storage Gen 1 

若要將資料捕獲到 Azure Data Lake Storage Gen 1，請建立 Data Lake Storage Gen 1 帳戶和事件中樞：

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>建立 Azure Data Lake Storage Gen 1 帳戶和資料夾

1. [依照開始使用 Azure 入口網站的 Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-get-started-portal.md)中的指示，建立 Data Lake Storage 帳戶。
2. 依照 [[將許可權指派給事件中樞](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)] 區段中的指示，在您想要從事件中樞中捕獲資料的 Data Lake Storage Gen 1 帳戶內建立資料夾，並將許可權指派給事件中樞，讓它可以將資料寫入您的 Data Lake Storage Gen 1 帳戶。  


### <a name="create-an-event-hub"></a>建立事件中樞

1. 事件中樞必須與您建立的 Azure Data Lake Storage Gen 1 帳戶位於相同的 Azure 訂用帳戶中。 按一下 [建立事件中樞]**** 入口網站頁面中 [擷取]**** 之下的 [開啟]**** 按鈕，以建立事件中樞。 
2. 在 [建立事件中樞]**** 入口網站頁面中，從 [擷取提供者]**** 方塊選取 [Azure Data Lake Store]****。
3. 在 [ **Data Lake Store** ] 下拉式清單旁邊的 [**選取存放區**] 中，指定您先前建立的 Data Lake Storage Gen 1 帳戶，然後在 [ **Data Lake 路徑**] 欄位中，輸入您所建立之資料檔案夾的路徑。

    ![選取 Data Lake Storage 帳戶][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>在現有的事件中樞上新增或設定擷取功能

您可以在位於事件中樞命名空間的現有事件中樞上設定擷取功能。 若要在現有事件中樞上啟用擷取功能，或變更您的擷取設定，請按一下命名空間以載入概觀畫面，然後按一下您要啟用或變更擷取設定的事件中樞。 最後，按一下開啟之頁面左側的 [擷取]**** 選項，然後編輯設定，如下圖所示：

### <a name="azure-blob-storage"></a>Azure Blob 儲存體

![設定 Azure Blob 儲存體][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![設定 Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![設定 Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>後續步驟

- 閱讀[事件中樞擷取概觀][capture-overview]，深入了解事件中樞擷取功能。
- 您也可以透過 Azure Resource Manager 範本來設定事件中樞擷取。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本啟用擷取功能](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。
- [了解如何以事件中樞命名空間作為來源來建立 Azure 事件方格訂用帳戶](store-captured-data-data-warehouse.md)
- [使用 Azure 入口網站開始使用 Azure Data Lake 存放區](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
