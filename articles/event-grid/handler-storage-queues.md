---
title: 以儲存體佇列作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 儲存體佇列作為「Azure 事件方格」事件的事件處理常式。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596267"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>以儲存體佇列作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 佇列儲存體**是其中之一。 

使用**佇列儲存體**來接收需要提取的事件。 若執行中的流程太過冗長導致回應時間過久，您可以使用佇列儲存體。 藉由傳送事件至佇列儲存體，應用程式即可按照自己的排程提取並處理事件。

## <a name="tutorials"></a>教學課程
如需使用佇列儲存體作為事件處理常式的範例，請參閱下列教學課程。 

|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 和事件方格將自訂事件路由至 Azure 佇列儲存體](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
