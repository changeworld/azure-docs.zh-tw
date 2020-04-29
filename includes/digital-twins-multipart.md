---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77111245"
---
> [!NOTE]
> 多部分要求通常需要三個資訊片段：
> * **Content-Type** 標頭：
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **內容**配置：
>   * `form-data; name="metadata"`
> * 要上傳的檔案內容
>
> **Content-Type** 和 **Content-Disposition** 可能會依使用案例而有所不同。

可以透過程式設計的方式 (透過 C#)、透過 REST 用戶端或 [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request) 等工具進行多部分要求。 REST 用戶端工具可能對複雜的多部分要求具有不同程度的支援。 組態設定可能會隨著工具而略有不同。 確認哪一種工具最適合您的需求。

> [!IMPORTANT]
> 針對 Azure Digital Twins 管理 API 發出的多部分要求通常有兩個部分：
> * 由 **Content-Type** 和/或 **Content-Disposition** 宣告的 Blob 中繼資料 (例如相關聯的 MIME 類型)
> * Blob 內容，其中包含要上傳之檔案的非結構化內容
>
> 兩個部分都不需要 **PATCH** 要求。 針對 **POST** 或建立作業，兩者皆為必要。

[佔用量快速入門原始程式碼](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs)包含完整的 C# 範例，示範如何針對 Azure Digital Twins 管理 API 發出多部分要求。