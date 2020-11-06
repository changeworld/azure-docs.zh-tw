---
title: '版本控制原則-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器和彈性伺服器 (預覽) '
description: 描述在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中 Postgres 主要和次要版本的原則。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f92864cea4332157b0bf8b171a9d88b34f79a5ac
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422105"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>適用於 PostgreSQL 的 Azure 資料庫版本控制原則

本頁面描述適用於 PostgreSQL 的 Azure 資料庫版本控制原則，適用于適用於 PostgreSQL 的 Azure 資料庫單一伺服器和適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器 (Preview) 部署模式。

## <a name="supported--postgresql-versions"></a>支援的于 postgresql 版本

適用於 PostgreSQL 的 Azure 資料庫支援下列資料庫版本。

| 版本 | 單一伺服器 | 彈性伺服器 (預覽) |
| ----- | :------: | :----: |
| 于 postgresql 12 |  | X  | 
| 于 postgresql 11 | X | X |
| 于 postgresql 10 | X |  |
| 于 postgresql 9。6 | X |  |
| 于 postgresql 9。5 | X |  |

## <a name="major-version-support"></a>主要版本支援
于 postgresql 會支援每個主要版本的適用於 PostgreSQL 的 Azure 資料庫從 Azure 開始支援版本的日期開始，直到于 postgresql 社區淘汰版本為止，如 [于 postgresql 社區版本控制原則](https://www.postgresql.org/support/versioning/)中所述。

## <a name="minor-version-support"></a>次要版本支援
適用於 PostgreSQL 的 Azure 資料庫會在定期維護過程中，自動執行 Azure 慣用於 postgresql 版本的次要版本升級。 

## <a name="major-version-retirement-policy"></a>主要版本淘汰原則
下表提供于 postgresql 主要版本的淘汰詳細資料。 日期會遵循 [于 postgresql 社區版本控制原則](https://www.postgresql.org/support/versioning/)。

| 版本 | 新功能 | Azure 支援開始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| 于 postgresql 9。5| [功能](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018 年 4 月 18 日    | 2021年2月11日
| [于 postgresql 9。6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [功能](https://wiki.postgresql.org/wiki/NewIn96) | 2018 年 4 月 18 日  | 2021年11月11日
| [于 postgresql 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [功能](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018 年 6 月 4 日  | 2022年11月10日
| [于 postgresql 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [功能](https://www.postgresql.org/docs/11/release-11.html) | 2019 年 7 月 24 日  | 2023年11月9日
| [于 postgresql 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [功能](https://www.postgresql.org/docs/12/release-12.html) | 2020年9月22日  | 2024年11月14日

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中不支援已淘汰的于 postgresql 引擎版本

在每個于 postgresql 資料庫版本的停用日期之後，如果您繼續執行已淘汰的版本，請注意下列限制：
- 因為此社區不會釋出任何進一步的 bug 修正或安全性修正，所以 Azure for 于 postgresql 不會針對任何 bug 或安全性問題修補已淘汰的資料庫引擎，或針對已淘汰的資料庫引擎採取安全性措施。 因此，您可能會遇到安全性弱點或其他問題。 不過，Azure 會繼續定期針對主機、作業系統、容器和任何其他服務相關元件執行維護和修補。
- 如果您可能遇到與于 postgresql 資料庫相關的任何支援問題，我們可能無法提供您支援。 在這種情況下，您必須升級您的資料庫，才能讓我們提供任何支援。
- 您將無法為已淘汰的版本建立新的資料庫伺服器。 不過，您將能夠執行時間點復原，並為現有的伺服器建立讀取複本。
- 適用於 PostgreSQL 的 Azure 資料庫所開發的新服務功能，僅適用于支援的資料庫伺服器版本。
- 執行時間 Sla 僅適用于適用於 PostgreSQL 的 Azure 資料庫服務相關的問題，而不是與 Database engine 相關的 bug 所造成的任何停機時間。  
- 如果在已淘汰的資料庫版本中識別出的于 postgresql 資料庫引擎弱點所造成的服務發生嚴重威脅，則 Azure 可能會選擇停止資料庫伺服器的計算節點來保護服務。 在這種情況下，您可能會在伺服器上線之前，收到升級伺服器的通知。

## <a name="postgresql-version-syntax"></a>于 postgresql 版本語法
在於 postgresql 第10版之前， [于 postgresql 版本設定原則](https://www.postgresql.org/support/versioning/) 會將 _主要版本_ 升級視為第一個 _或_ 第二個數字的增加。 例如，9.5 至9.6 被視為 _主要_ 版本升級。 從版本10開始，只有第一個數位的變更會被視為主要版本升級。 例如，10.0 至10.1 是 _次要_ 版本升級。 10到11版是 _主要_ 版本升級。

## <a name="next-steps"></a>後續步驟
- 請參閱適用於 PostgreSQL 的 Azure 資料庫-單一伺服器 [支援的版本](./concepts-supported-versions.md)
- 請參閱適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器 (預覽版) [支援的版本](flexible-server/concepts-supported-versions.md)
- 如需有關如何執行主要版本升級的詳細資訊，請參閱 [主要版本升級](how-to-upgrade-using-dump-and-restore.md) 檔。
- 如需支援的于 postgresql 延伸模組的詳細資訊，請參閱 [擴充功能檔](concepts-extensions.md)。
