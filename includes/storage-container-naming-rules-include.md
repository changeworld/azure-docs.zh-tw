---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174594"
---
儲存體 Blob 中的每個 Blob 必須位於一個容器中。 此容器會組成 blob 名稱的一部分。 例如， `mycontainer` 是這些範例 blob URI 中容器的名稱：

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

容器名稱必須是有效的 DNS 名稱，且符合下列命名規則：

1. 容器名稱必須以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。
2. 每個虛線 (-) 字元前後必須立即接著字母或數字；容器名稱中不允許連續虛線。
3. 容器名稱的所有字母必須都是小寫。
4. 容器名稱必須介於 3 至 63 個字元長。

> [!IMPORTANT]
> 請注意容器的名稱一律必須小寫。 如果在容器名稱中含有大寫字母，或其他違反容器命名規則，您可能會收到「400 錯誤 (不正確的要求)」錯誤訊息。 
> 
> 

