---
title: Azure PowerShell 指令碼範例 - 建立自訂主題 | Microsoft Docs
description: 本文提供範例 Azure PowerShell 指令碼，示範如何建立事件方格自訂主題。
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 915b8e13adaa440063f5db62b72fa39e1035d576
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460774"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>使用 PowerShell 建立 Event Grid 自訂主題

此指令碼可建立 Event Grid 自訂主題。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立自訂主題。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | 建立 Event Grid 自訂主題。 |

## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](../overview.md)。
* 如需 PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/get-started-azureps)。
