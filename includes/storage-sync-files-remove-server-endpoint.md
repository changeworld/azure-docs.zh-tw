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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "72391584"
---
否：移除伺服器端點不像重新開機伺服器一樣！ 移除和重新建立伺服器端點幾乎不是解決同步、雲端階層處理問題，或 Azure 檔案同步其他層面的適當解決方案。移除伺服器端點是破壞性作業。 如果階層式檔案存在於伺服器端點命名空間之外，可能會導致資料遺失。 如需詳細資訊，請參閱階層式檔案位於[伺服器端點命名空間外部的原因](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)。 或者，它可能會導致伺服器端點命名空間中存在的分層式檔案無法存取檔案。 重新建立伺服器端點時，不會解決這些問題。 分層的檔案可能會位於您的伺服器端點命名空間內部，即使您從未啟用過雲端分層。 因此，除非您想要停止使用此特定資料夾的 Azure 檔案同步，或已明確指示 Microsoft 工程師來執行這項操作，否則建議您不要移除伺服器端點。 如需關於移除伺服器端點的詳細資訊，請參閱[移除伺服器端點](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
