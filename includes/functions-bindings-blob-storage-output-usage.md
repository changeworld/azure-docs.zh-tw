---
title: 包含檔案
description: 包含檔案
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 09e56395122f3be897d8bfb34b1d51cee034b505
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356222"
---
您可以系結至下列類型來寫入 blob：

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>二級</sup>
* `CloudBlockBlob`<sup>二級</sup>
* `CloudPageBlob`<sup>二級</sup>
* `CloudAppendBlob`<sup>二級</sup>

<sup>1</sup> 在 *function.json* 或 `FileAccess.Read` C# 類別庫中需要 "in" 繫結 `direction`。 不過，您可以使用執行階段提供的容器物件來執行寫入作業，例如將 blob 上傳至容器。

<sup>2</sup> 在 *function.json* 或 `FileAccess.ReadWrite` C# 類別庫中需要 "inout" 繫結 `direction`。

如果您嘗試繫結至其中一個儲存體 SDK 類型，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)。

由於會將整個 blob 內容載入記憶體中，因此只有在 Blob 大小很小時才建議繫結至 `string` 或 `Byte[]`。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。 如需詳細資訊，請參閱本文稍早的[並行存取和記憶體使用量](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage)。
