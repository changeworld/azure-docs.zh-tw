---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391584"
---
否：刪除伺服器終結點與重新開機伺服器不類似！ 刪除和重新創建伺服器終結點幾乎絕不是修復 Azure 檔同步的同步、雲分層或其他方面問題的適當解決方案。刪除伺服器終結點是一種破壞性操作。 如果分層檔存在於伺服器終結點命名空間之外，則可能導致資料丟失。 有關詳細資訊[，請參閱為什麼分層檔存在於伺服器終結點命名空間之外](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)。 或者，它可能導致伺服器終結點命名空間中存在的分層檔的無法訪問檔。 重新創建伺服器終結點時，這些問題無法解決。 分層的檔案可能會位於您的伺服器端點命名空間內部，即使您從未啟用過雲端分層。 因此，建議不要刪除伺服器終結點，除非您希望停止使用此特定資料夾的 Azure 檔同步，或者 Microsoft 工程師已明確指示這樣做。 如需關於移除伺服器端點的詳細資訊，請參閱[移除伺服器端點](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
