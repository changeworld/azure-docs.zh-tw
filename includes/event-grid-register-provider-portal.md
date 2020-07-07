---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605771"
---
## <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者

如果您先前在 Azure 訂用帳戶中從未使用過事件方格，可能必須註冊事件方格資源提供者。

在 Azure 入口網站中：

1. 選取左側功能表上的 [訂用帳戶]  。
1. 選取您用於事件方格的訂用帳戶。
1. 在左側功能表的 [設定]  下，選取 [資源提供者]  。
1. 尋找 **Microsoft.EventGrid**。
1. 如果未註冊，請選取 [註冊]  。 

可能需要一點時間才能完成註冊。 選取 [重新整理]  來更新狀態。 當 [狀態]  是 [已註冊]  時，代表已準備好繼續進行。