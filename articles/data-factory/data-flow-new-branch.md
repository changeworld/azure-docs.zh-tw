---
title: 在對應資料流程中的多個分支
description: 使用多個分支複寫對應資料流程中的資料流程
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a1dd4baecd0e1f817c93652fbc0766069ccf5583
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040140"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在對應資料流程中建立新的分支

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

加入新的分支，以針對相同的資料流程進行多組作業和轉換。 當您想要將相同的來源用於多個接收或將自我聯結的資料一起使用時，新增分支會很有用。

您可以從轉換清單中加入新的分支，類似于其他轉換。 當您嘗試分支的轉換之後有現有的轉換時，才會有 **新的分支** 可作為動作。

![螢幕擷取畫面：顯示 [多個輸入/輸出] 功能表中的 [新增分支] 選項。](media/data-flow/new-branch2.png "新增分支")

在下列範例中，資料流程會讀取出租車行程資料。 日期和廠商都必須匯總輸出。 您可以加入新的分支，而不是建立兩個從相同來源讀取的不同資料流程。 如此一來，就可以在相同的資料流程中執行這兩個匯總。 

![螢幕擷取畫面顯示來自來源的兩個分支的資料流程。](media/data-flow/new-branch.png "新增分支")
