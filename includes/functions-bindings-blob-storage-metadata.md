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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "69642132"
---
Blob 觸發程序提供數個中繼資料屬性。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些值的語意與 [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) 類型相同。

|屬性  |類型  |描述  |
|---------|---------|---------|
|`BlobTrigger`|`string`|觸發 Blob 的路徑。|
|`Uri`|`System.Uri`|Blob 的主要位置 URI。|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob 的系統屬性。 |
|`Metadata` |`IDictionary<string,string>`|Blob 的使用者定義中繼資料。|

例如，下列 C# 指令碼和 JavaScript 範例會記錄觸發 Blob 的路徑，包括容器在內：

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
