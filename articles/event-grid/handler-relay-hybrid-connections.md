---
title: 以轉送混合式連線作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 轉送作為「Azure 事件方格」事件的事件處理常式。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596107"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>以轉送混合式連線作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 轉送**是其中之一。 

使用 **Azure 轉送**的混合式連線將事件傳送至應用程式，這些應用程式都位於企業網路中，而且沒有可公開存取的端點。

## <a name="tutorials"></a>教學課程
如需使用 Azure 轉送混合式連線作為事件處理常式的範例，請參閱下列教學課程。 

|Title  |描述  |
|---------|---------|
| [教學課程：將事件傳送至混合式連線](custom-event-to-hybrid-connection.md) | 將自訂事件傳送至現有的混合式連線，以透過接聽應用程式進行處理。 |

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 