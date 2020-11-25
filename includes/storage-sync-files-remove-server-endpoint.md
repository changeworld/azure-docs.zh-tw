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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005302"
---
否：移除伺服器端點不像將伺服器重新開機！ 移除並重新建立伺服器端點幾乎不是修正同步、雲端階層處理或 Azure 檔案同步其他方面問題的適當解決方案。移除伺服器端點是破壞性作業。 當階層式檔案存在於伺服器端點命名空間之外時，可能會導致資料遺失。 如需詳細資訊，請參閱為何階層式檔案位於 [伺服器端點命名空間外部](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) 。 或者，它可能會導致伺服器端點命名空間記憶體在的階層式檔案無法存取檔案。 重新建立伺服器端點時，不會解決這些問題。 分層的檔案可能會位於您的伺服器端點命名空間內部，即使您從未啟用過雲端分層。 這就是為什麼建議您不要移除伺服器端點，除非您想要停止使用 Azure 檔案同步搭配這個特定的資料夾，或由 Microsoft 工程師明確指示。 如需關於移除伺服器端點的詳細資訊，請參閱[移除伺服器端點](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
