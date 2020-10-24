---
title: 如何在 .NET SDK 中轉換會話權杖格式-Azure Cosmos DB
description: 瞭解如何轉換會話權杖格式，以確保不同 .NET SDK 版本之間的相容性
author: vinhms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: ff66c2cb355e2070cdf30444c52d1accacac8964
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490436"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>在 .NET SDK 中轉換會話權杖格式

本文說明如何在不同的會話權杖格式之間進行轉換，以確保 SDK 版本之間的相容性。

> [!NOTE]
> 根據預設，SDK 會自動追蹤會話權杖，並使用最新的會話權杖。  如需詳細資訊，請造訪 [使用會話權杖](how-to-manage-consistency.md#utilize-session-tokens)。 本文中的指示僅適用于下列條件：
> * 您的 Azure Cosmos DB 帳戶會使用會話一致性。
> * 您要以手動方式管理會話權杖。
> * 您同時使用多個版本的 SDK。

## <a name="session-token-formats"></a>會話權杖格式

有兩種會話權杖格式： **simple** 和 **vector**。  這兩種格式不是可互換的，因此在傳遞至具有不同版本的用戶端應用程式時，應該轉換格式。
- .NET SDK V1 ( # B0 umentDB-1.x 版) 使用 **簡單** 會話權杖格式
- .NET SDK V2 ( # B0 umentDB-2.x 版) 使用 **向量** 會話權杖格式

### <a name="simple-session-token"></a>簡單會話權杖

簡單會話權杖的格式如下： `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>向量會話權杖

向量會話權杖的格式如下： `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>轉換成簡單的會話權杖

若要使用 .NET SDK V1 將會話權杖傳遞給用戶端，請使用 **簡單** 的會話權杖格式。  例如，使用下列範例程式碼進行轉換。

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>轉換成向量會話權杖

若要使用 .NET SDK V2 將會話權杖傳遞給用戶端，請使用 **向量** 會話權杖格式。  例如，使用下列範例程式碼進行轉換。

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>後續步驟

請閱讀下列文章：

* [使用工作階段權杖在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [在 Azure Cosmos DB 中選擇正確的一致性層級](./consistency-levels.md)
* [Azure Cosmos DB 中的一致性、可用性和效能權衡取捨](./consistency-levels.md)
* [各種一致性層級的可用性和效能權衡取捨](./consistency-levels.md)