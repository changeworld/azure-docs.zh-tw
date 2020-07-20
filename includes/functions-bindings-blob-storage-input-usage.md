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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202121"
---
您可以針對 blob 輸入系結使用下列參數類型：

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> 在 *function.json* 或 `FileAccess.ReadWrite` C# 類別庫中需要 "inout" 繫結 `direction`。

如果您嘗試繫結至其中一個儲存體 SDK 類型，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)。

由於會將整個 blob 內容載入記憶體中，因此只有在 Blob 大小很小時才建議繫結至 `string` 或 `Byte[]`。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。 如需詳細資訊，請參閱本文稍早的[並行存取和記憶體使用量](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage)。
