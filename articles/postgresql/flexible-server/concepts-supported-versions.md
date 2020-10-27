---
title: 支援的版本-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中支援的于 postgresql 主要和次要版本。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542075"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中支援的于 postgresql 主要版本

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器目前支援下列主要版本：

## <a name="postgresql-version-12"></a>于 postgresql 第12版

目前的次要版本為12.4。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/12/static/release-12-4.html) ，以深入瞭解此次要版本中的改進功能與修正。

## <a name="postgresql-version-11"></a>于 postgresql 第11版

目前的次要版本為11.9。 請參閱 [于 postgresql 檔](https://www.postgresql.org/docs/11/static/release-11-9.html) ，以深入瞭解此次要版本中的改進功能與修正。

## <a name="postgresql-version-10-and-older"></a>于 postgresql 第10版和更舊版本

針對適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器，我們不支援于 postgresql 第10版和更舊版本。 如果您需要較舊的版本，請使用 [單一伺服器](../concepts-supported-versions.md) 部署選項。

## <a name="managing-upgrades"></a>管理升級

于 postgresql 專案會定期發出次要版本來修正回報的錯誤。 適用於 PostgreSQL 的 Azure 資料庫在服務的每月部署期間自動修補具有次要版本的伺服器。

尚未支援主要版本升級的自動化。 例如，目前沒有從于 postgresql 11 到于 postgresql 12 的自動升級。<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->