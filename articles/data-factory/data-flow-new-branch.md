---
title: 對應資料流程的多個分支
description: 複製資料流程,映射具有多個分支的資料串流
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606400"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>建立新分支

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

添加新分支,以便針對同一數據流執行多組操作和轉換。 如果要將同一源用於多個接收器或將數據合併在一起,則添加新分支非常有用。

可以從轉換清單中添加新分支,類似於其他轉換。 只當嘗試分支轉換後存在現有轉換時,**新分支**才可作為操作提供。

![新增分支](media/data-flow/new-branch2.png "新增分支")

在下面的示例中,數據流正在讀取計程車行程數據。 需要按天和供應商聚合的輸出。 可以添加新分支,而不是創建從同一源讀取的兩個單獨的數據流。 這樣,兩個聚合都可以作為同一數據流的一部分執行。 

![新增分支](media/data-flow/new-branch.png "新增分支")
