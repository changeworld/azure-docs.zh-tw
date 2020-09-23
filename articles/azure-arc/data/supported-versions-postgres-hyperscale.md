---
title: 支援的版本 Postgres 已啟用 Azure Arc 的于 postgresql 超大規模
description: 支援的版本 Postgres 已啟用 Azure Arc 的于 postgresql 超大規模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934350"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>支援的 Postgres 版本，並已啟用 Azure Arc 于 postgresql 超大規模

本文說明 Azure Arc enabled 于 postgresql 超大規模有哪些版本的 Postgres 可供使用。
支援的版本清單會隨著時間而演進。 目前支援的主要版本如下：
- Postgres 12 (預設) 
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>如何選擇版本？
建議您查看您的應用程式所設計的版本，以及每個版本的功能。 若要深入瞭解，請閱讀官方 Postgres 網站上每個版本的相關資訊：
- [Postgres 12 (預設) ](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>如何在已啟用 Azure Arc 的于 postgresql 超大規模中建立特定版本？
在建立期間，您可以藉由傳遞 _--engine-version_ 參數來指出要建立的版本。 如果您未指定版本資訊，預設會建立 Postgres 12 版的伺服器群組。

## <a name="how-do-be-notified-when-other-versions-are-available"></a>當有其他版本可供使用時，如何通知？
返回並閱讀這篇文章。 它會適當地更新。 您也可以在 Kubernetes 叢集中的 Arc 資料控制器 (.CRD) ，列出自訂資源定義的種類。
執行以下命令：
```console
kubectl get crds
```

它會傳回輸出，如下所示：
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

在此範例中，此輸出表示有兩種與 Postgres 相關的 .CRD：
- postgresql-12s.arcdata.microsoft.com 是 Postgres 12 的 .CRD
- postgresql-11s.arcdata.microsoft.com 是 Postgres 11 的 .CRD

這些都是 CRDs，而不是伺服器群組。 .CRD 是否存在，並不表示您有（或尚未建立）該版本的伺服器群組。
.CRD 表示可以建立的資源類型。

## <a name="next-steps"></a>後續步驟：
- [瞭解如何建立 Azure Arc 啟用的于 postgresql 超大規模](create-postgresql-hyperscale-server-group.md)
- [瞭解如何取得在您的 Arc 資料控制器中建立的 Azure Arc 于 postgresql 超大規模伺服器群組清單](list-server-groups-postgres-hyperscale.md)
