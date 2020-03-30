---
title: Azure 函數的 Azure 事件網格綁定
description: 了解如何在 Azure Functions 中處理 Event Grid 事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461074"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure 函數的 Azure 事件網格綁定

此參考說明如何在 Azure 函數中處理[事件網格](../event-grid/overview.md)事件。 有關如何在 HTTP 終結點中處理事件網格消息的詳細資訊，請參閱[將事件接收到 HTTP 終結點](../event-grid/receive-events.md)。

Event Grid 是一項 Azure 服務，會傳送 HTTP 要求通知您「發行者」** 中發生的事件。 發行者是產生事件的服務或資源。 例如，Azure Blob 儲存體帳戶即為發行者，而 [Blob 的上傳或刪除是事件](../storage/blobs/storage-blob-event-overview.md)。 部分 [Azure 服務內建有將事件發佈至 Event Grid 的支援](../event-grid/overview.md#event-sources)。

事件*處理常式*會接收及處理事件。 Azure Functions 是數個[有內建 Event Grid 事件處理支援的 Azure 服務](../event-grid/overview.md#event-handlers)之一。 在此引用中，您將學習使用事件網格觸發器在從事件網格接收事件時調用函數，並使用輸出綁定將事件發送到[事件網格自訂主題](../event-grid/post-to-custom-topic.md)。

如果您願意，可以使用 HTTP 觸發器來處理事件網格事件;因此，您可以使用 HTTP 觸發器來處理事件網格事件。請參閱[將事件接收到 HTTP 終結點](../event-grid/receive-events.md)。 目前，當事件在[雲事件架構](../event-grid/cloudevents-schema.md#azure-functions)中傳遞時，不能為 Azure 函數應用使用事件網格觸發器。 相反地，請使用 HTTP 觸發程序。

| 動作 | 類型 |
|---------|---------|
| 調度事件網格事件時運行函數 | [觸發](./functions-bindings-event-grid-trigger.md) |
| 發送事件網格事件 |[輸出綁定](./functions-bindings-event-grid-output.md) |

此引用中的代碼預設為 .NET Core 語法，用於函數版本 2.x 和更高版本。 如需 1.x 語法的資訊，請參閱 [1.x 函式範本](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)。

## <a name="add-to-your-functions-app"></a>添加到功能應用

### <a name="functions-2x-and-higher"></a>功能 2.x 及以上

使用觸發器和綁定需要引用相應的包。 NuGet 包用於 .NET 類庫，而擴展包用於所有其他應用程式類型。

| 語言                                        | 添加...                                   | 備註 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安裝[NuGet 套裝軟體]，版本 3.x | |
| C# 腳本、JAVA、JavaScript、Python、PowerShell | 註冊[擴展包]          | 建議使用[Azure 工具擴展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)與視覺化工作室代碼一起使用。 |
| C# 腳本（僅在 Azure 門戶中連線）         | 添加綁定                            | 要更新現有綁定擴展，而無需重新發佈函數應用，請參閱[更新擴展]。 |

[core tools]: ./functions-run-local.md
[擴展包]: ./functions-bindings-register.md#extension-bundles
[Nuget 套件]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[更新擴展]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

函數 1.x 應用會自動具有[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包（版本 2.x）的引用。

## <a name="next-steps"></a>後續步驟
* [調度事件網格事件時運行函數](./functions-bindings-event-grid-trigger.md)
* [調度事件網格事件](./functions-bindings-event-grid-trigger.md)
