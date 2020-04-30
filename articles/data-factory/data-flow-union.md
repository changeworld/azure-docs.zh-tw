---
title: 對應資料流程聯集轉換
description: Azure Data Factory 對應的資料流程新增分支轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 1874e3536a3dda123c7fff9726c02e5d92d88804
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204333"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory 對應的資料流程聯集轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集合聯集會將多個資料流結合在一起，並使那些資料流的 SQL 集合聯集成為來自集合聯集轉換的新輸出。 每個輸入資料流程中的所有架構都會合並在您的資料流程內，而不需要有聯結索引鍵。

您可以在 [設定] 資料表中結合 n 個數據流，方法是選取每個已設定資料列旁的 [+] 圖示，包括來源資料，以及資料流程中現有轉換的資料流程。

以下是 ADF 的對應資料流程中，「聯集」轉換的簡短影片逐步解說：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![集合聯集轉換](media/data-flow/union.png "Union")

在此情況下，您可以結合多個來源的不同中繼資料（在此範例中，三個不同的來源檔案），並將它們結合成單一資料流程：

![聯集轉換總覽](media/data-flow/union111.png "聯集1")

若要達成此目的，請加入您想要加入的所有來源，以在聯集設定中加入其他資料列。 不需要常見的查閱或聯結索引鍵：

![聯集轉換設定](media/data-flow/unionsettings.png "聯集設定")

如果您在聯集後設定選取轉換，您將能夠重新命名不是從 headerless 來源命名的重迭欄位或欄位。 按一下 [檢查] 以查看此範例中三個不同來源的 [結合中繼資料與132總計] 資料行：

![聯集轉換最終](media/data-flow/union333.png "聯集3")

## <a name="name-and-position"></a>名稱和位置

當您選擇 [依名稱聯集] 時，每個資料行值都會從每個來源的對應資料行中，加上新的串連中繼資料架構。

如果您選擇「依位置的聯集」，每個資料行值都會從每個對應來源的原始位置中卸載，因而產生新的結合資料串流，其中每個來源的資料會加入至相同的資料流程：

![聯集輸出](media/data-flow/unionoutput.png "聯集輸出")

## <a name="next-steps"></a>後續步驟

探索類似的轉換，包括[Join](data-flow-join.md)和[Exists](data-flow-exists.md)。
