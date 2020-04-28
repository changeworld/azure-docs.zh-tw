---
title: 支援的版本-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 說明適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中支援的 Postgres 主要和次要版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792228"
---
# <a name="supported-postgresql-major-versions"></a>支援的于 postgresql 主要版本
Microsoft 的目標是在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中支援 n-2 版的于 postgresql 引擎。 版本會是 Azure 上目前的主要版本 (n) 和兩個先前的主要版本 (-2)。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列主要版本：

## <a name="postgresql-version-11"></a>于 postgresql 版本11
目前的次要版本為11.5。 請參閱[于 postgresql 檔](https://www.postgresql.org/docs/11/static/release-11-5.html)，以深入瞭解此次要版本中的改進功能和修正。

## <a name="postgresql-version-10"></a>于 postgresql 第10版
目前的次要版本為10.10。 請參閱[于 postgresql 檔](https://www.postgresql.org/docs/10/static/release-10-10.html)，以深入瞭解此次要版本中的改進功能和修正。

## <a name="postgresql-version-96"></a>于 postgresql 版本9。6
目前的次要版本是9.6.15。 請參閱[于 postgresql 檔](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html)，以深入瞭解此次要版本中的改進功能和修正。

## <a name="postgresql-version-95"></a>于 postgresql 版本9。5
目前的次要版本是9.5.19。 請參閱[于 postgresql 檔](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html)，以瞭解此次要版本中的改進功能和修正。

## <a name="managing-upgrades"></a>管理升級
于 postgresql 專案會定期發出次要版本來修正回報的錯誤。 適用於 PostgreSQL 的 Azure 資料庫在服務的每月部署期間，自動修補具有次要版本的伺服器。 

不支援自動主要版本升級。 例如，不會從 PostgreSQL 9.5 自動升級為 PostgreSQL 9.6。 若要升級到下一個主要版本，請建立資料庫[傾印並還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本建立的伺服器。

### <a name="version-syntax"></a>版本語法
在於 postgresql 第10版之前，[于 postgresql 版本設定原則](https://www.postgresql.org/support/versioning/)會將_主要版本_升級視為第一個_或_第二個數字的增加。 例如，9.5 到9.6 被視為_主要_版本升級。 從第10版開始，只有第一個數位的變更會被視為主要版本升級。 例如，10.0 到10.1 是_次要_版本升級。 版本10到11是_主要_版本升級。

## <a name="next-steps"></a>後續步驟
如需支援的于 postgresql 擴充功能的詳細資訊，請參閱[擴充功能檔](concepts-extensions.md)。
