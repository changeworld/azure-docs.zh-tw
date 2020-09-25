---
title: 建立 Azure 事件中樞架構登錄
description: 本文說明如何在 Azure 事件中樞命名空間中建立架構登錄。
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 691279ea4fbadf5a905de4bab0304a1ad526146d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342734"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>建立 Azure 事件中樞 schema registry (preview) 
本文說明如何在 Azure 事件中樞所裝載的架構登錄中建立具有架構的架構群組。 如需 Azure 事件中樞架構登錄功能的總覽，請參閱 [事件中樞的 Azure 架構登錄](schema-registry-overview.md)。

> [!NOTE]
> - **架構**登錄功能目前為**預覽**狀態，且僅適用于**標準**和**專用**層，而非**基本**層。
> - 此預覽版目前僅適用于 **美國中西部** 區域。 

## <a name="prerequisites"></a>Prerequisites
[建立事件中樞命名空間](event-hubs-create.md#create-an-event-hubs-namespace)。 您也可以使用現有的命名空間。 

## <a name="create-a-schema-group"></a>建立架構群組
1. 流覽至 [ **事件中樞命名空間** ] 頁面。 
1. 在左側功能表上選取 [ **架構** 登錄]。 若要建立架構群組，請選取工具列上的 [ **+ 架構群組** ]。 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="架構登錄頁面":::
1. 在 [ **建立架構群組** ] 頁面上，執行下列步驟：
    1. 輸入架構群組的 **名稱** 。
    1. 針對 **序列化類型**，請挑選適用于架構群組中所有架構的序列化格式。 目前， **avro** 是唯一支援的類型，因此請選取 **avro**。 
    1. 選取群組中所有架構的 **相容性模式** 。 針對 **Avro**，支援正向和回溯相容性模式。 
    1. 然後，選取 [ **建立** ] 以建立架構群組。 
1. 在架構群組清單中，選取 **架構群組** 的名稱。

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="在清單中選取您的架構群組":::    
1. 您會看到群組的 [ **架構群組** ] 頁面。

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="架構群組頁面":::
    

## <a name="add-a-schema-to-the-schema-group"></a>將架構新增至架構群組
在本節中，您會使用 Azure 入口網站將架構新增至架構群組。 

1. 在 [ **架構群組** ] 頁面上，選取工具列上的 [ **+ 架構** ]。 
1. 在 [ **建立架構** ] 頁面上，執行下列步驟：
    1. 輸入架構的 **名稱** 。
    1. 在文字方塊中輸入下列 **架構** 。 您也可以選取具有架構的檔案。
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. 選取 [建立]。 
1. 從架構清單中選取 **架構** 。 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="選取結構描述":::
1. 您會看到架構的 **架構總覽** 頁面。 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="架構總覽頁面":::    
1. 如果有多個版本的架構，您會在 [ **版本** ] 下拉式清單中看到它們。 選取要切換至該版本架構的版本。 

## <a name="create-a-new-version-of-schema"></a>建立新版本的架構

1. 更新文字方塊中的架構，然後選取 [ **驗證**]。 在下列範例中，已將新欄位 `id` 加入至架構。 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="更新架構":::    
    
1. 檢查驗證狀態和變更，然後選取 [ **儲存**]。 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="檢查驗證狀態、變更和儲存":::     
1. 您會看到在 `2` [**架構總覽**] 頁面上已選取 [**版本**]。 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="新版本的架構":::    
1. 選取 `1` 以查看架構的第1版。 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="選取版本":::    


## <a name="next-steps"></a>後續步驟
如需架構登錄的詳細資訊，請參閱「 [事件中樞」中的 Azure Schema registry](schema-registry-overview.md)。

