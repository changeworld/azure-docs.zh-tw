---
title: 映射資料流程的多個分支
description: 複製資料流程，映射具有多個分支的資料流程
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834526"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在映射資料流程中創建新分支

添加新分支，以便針對同一資料流程執行多組操作和轉換。 如果要將同一源用於多個接收器或將資料合併在一起，則添加新分支非常有用。

可以從轉換清單中添加新分支，類似于其他轉換。 僅當嘗試分支轉換後存在現有轉換時，**新分支**才可作為操作提供。

![添加新分支](media/data-flow/new-branch2.png "添加新分支")

在下面的示例中，資料流程正在讀取出租車行程資料。 需要按天和供應商聚合的輸出。 可以添加新分支，而不是創建從同一源讀取的兩個單獨的資料流程。 這樣，兩個聚合都可以作為同一資料流程的一部分執行。 

![添加新分支](media/data-flow/new-branch.png "添加新分支")
