---
title: 支援的版本 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 描述支援 Postgres 主版本和次要版本在 Azure 資料庫中的 PostgreSQL - 單伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792228"
---
# <a name="supported-postgresql-major-versions"></a>支援 PostgreSQL 主要版本
Microsoft 的目標是在 Azure 資料庫中支援 n-2 版本的 PostgreSQL 引擎，用於 PostgreSQL - 單伺服器。 版本會是 Azure 上目前的主要版本 (n) 和兩個先前的主要版本 (-2)。

用於 PostgreSQL 的 Azure 資料庫當前支援以下主要版本：

## <a name="postgresql-version-11"></a>後SQL版本 11
當前次要版本為 11.5。 請參閱[PostgreSQL 文檔](https://www.postgresql.org/docs/11/static/release-11-5.html)，瞭解有關此次要版本中的改進和修復的詳細資訊。

## <a name="postgresql-version-10"></a>後SQL版本 10
當前次要版本為 10.10。 請參閱[PostgreSQL 文檔](https://www.postgresql.org/docs/10/static/release-10-10.html)，瞭解有關此次要版本中的改進和修復的詳細資訊。

## <a name="postgresql-version-96"></a>後SQL版本 9.6
當前次要版本為 9.6.15。 請參閱[PostgreSQL 文檔](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html)，瞭解有關此次要版本中的改進和修復的詳細資訊。

## <a name="postgresql-version-95"></a>後SQL版本 9.5
當前次要版本為 9.5.19。 請參閱[PostgreSQL 文檔](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html)，瞭解此次要版本中的改進和修復。

## <a name="managing-upgrades"></a>管理升級
PostgreSQL 專案定期發佈次要版本，以修復報告的 Bug。 PostgreSQL 的 Azure 資料庫會在服務的每月部署期間自動修補具有次要版本的伺服器。 

不支援自動主要版本升級。 例如，不會從 PostgreSQL 9.5 自動升級為 PostgreSQL 9.6。 若要升級到下一個主要版本，請建立資料庫[傾印並還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本建立的伺服器。

### <a name="version-syntax"></a>版本語法
在 PostgreSQL 版本 10 之前[，PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)認為_主要版本_升級是第一_個或_第二個數字的增加。 例如，9.5 到 9.6 被視為_主要_版本升級。 從版本 10 起，只有第一個數位中的更改才被視為主要版本升級。 例如，10.0 到 10.1 是_次要_版本升級。 版本 10 到 11 是_主要_版本升級。

## <a name="next-steps"></a>後續步驟
有關支援的 PostgreSQL 擴展的資訊，請參閱[擴展文檔](concepts-extensions.md)。
