---
title: 支援的版本-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫單一伺服器中支援的 Postgres 主要和次要版本。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658514"
---
# <a name="supported-postgresql-major-versions"></a>支援的于 postgresql 主要版本

如需支援原則的詳細資料，請參閱 [適用於 PostgreSQL 的 Azure 資料庫版本控制原則](concepts-version-policy.md) 。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列主要版本：

## <a name="postgresql-version-11"></a>于 postgresql 第11版
目前的次要版本為11.6。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/11/static/release-11-6.html) ，以深入瞭解此次要版本中的改進功能與修正。

## <a name="postgresql-version-10"></a>于 postgresql 第10版
目前的次要版本為10.11。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/10/static/release-10-11.html) ，以深入瞭解此次要版本中的改進功能與修正。

## <a name="postgresql-version-96"></a>于 postgresql 版本9。6
目前的次要版本是9.6.16。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) ，以深入瞭解此次要版本中的改進功能與修正。

## <a name="postgresql-version-95"></a>于 postgresql 版本9。5
目前的次要版本是9.5.20。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) ，以瞭解此次要版本中的改進功能與修正。

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫將于2021年2月11日淘汰 Postgres 9.5 版，以與 Postgres 社區 [版本控制原則](https://www.postgresql.org/support/versioning/)一致。 如需詳細資訊和限制，請參閱 [適用於 PostgreSQL 的 Azure 資料庫版本控制原則](concepts-version-policy.md) 。

## <a name="managing-upgrades"></a>管理升級
于 postgresql 專案會定期發出次要版本來修正回報的錯誤。 適用於 PostgreSQL 的 Azure 資料庫在服務的每月部署期間自動修補具有次要版本的伺服器。 

不支援主要版本的自動就地升級。 若要升級至下一個主要版本，您可以 
   * 查看使用傾印[和還原執行主要版本升級](./how-to-upgrade-using-dump-and-restore.md)的各種方法
   * 使用 [pg_dump 和 pg_restore](./howto-migrate-using-dump-and-restore.md) 將資料庫移至使用新引擎版本建立的伺服器
   * 或者，您可以使用[Azure 資料庫移轉服務](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)，從于 postgresql 10 升級為11

### <a name="version-syntax"></a>版本語法
在於 postgresql 第10版之前， [于 postgresql 版本設定原則](https://www.postgresql.org/support/versioning/) 會將 _主要版本_ 升級視為第一個 _或_ 第二個數字的增加。 例如，9.5 至9.6 被視為 _主要_ 版本升級。 從版本10開始，只有第一個數位的變更會被視為主要版本升級。 例如，10.0 至10.1 是 _次要_ 版本升級。 10到11版是 _主要_ 版本升級。

## <a name="next-steps"></a>後續步驟
如需支援的于 postgresql 延伸模組的詳細資訊，請參閱 [擴充功能檔](concepts-extensions.md)。
