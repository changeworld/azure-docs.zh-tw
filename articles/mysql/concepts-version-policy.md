---
title: '版本控制原則-適用於 MySQL 的 Azure 資料庫-單一伺服器和彈性伺服器 (預覽) '
description: 描述適用於 MySQL 的 Azure 資料庫中的 MySQL 主要和次要版本的原則
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 4903f1e48eb2f33c68d62c635201474b841ed146
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591507"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>適用於 MySQL 的 Azure 資料庫版本控制原則

本頁面描述適用於 MySQL 的 Azure 資料庫版本控制原則，適用于適用於 MySQL 的 Azure 資料庫單一伺服器和適用於 MySQL 的 Azure 資料庫彈性的伺服器 (Preview) 部署模式。

## <a name="supported--mysql-versions"></a>支援的 MySQL 版本

適用於 MySQL 的 Azure 資料庫支援下列資料庫版本。

| 版本 | 單一伺服器 | 彈性伺服器 (預覽) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5。7 | X | X |
| MySQL 5。6| X |  |


## <a name="major-version-support"></a>主要版本支援
從 Azure 開始支援版本的日期起，適用於 MySQL 的 Azure 資料庫會支援 MySQL 的每個主要版本，直到 MySQL 社區淘汰版本為止（如 [版本設定原則](https://www.mysql.com/support/eol-notice.html)中所提供）。

## <a name="minor-version-support"></a>次要版本支援
適用於 MySQL 的 Azure 資料庫會在定期維護過程中，自動執行 Azure 慣用 MySQL 版本的次要版本升級。 

## <a name="major-version-retirement-policy"></a>主要版本淘汰原則
下表提供 MySQL 主要版本的淘汰詳細資料。 日期會遵循 [MySQL 版本控制原則](https://www.mysql.com/support/eol-notice.html)。

| 版本 | 新功能 | Azure 支援開始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| [MySQL 5。6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [功能](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018 年 3 月 20 日 | 2021年2月
| [MySQL 5。7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018 年 3 月 20 日 | 2023年10月
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html))  | 2019 年 12 月 11 日 | 2026年4月


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中不支援已淘汰的 MySQL 引擎版本

在每個 MySQL 資料庫版本的停用日期之後，如果您繼續執行已淘汰的版本，請注意下列限制：
- 由於該社區將不會釋出任何進一步的 bug 修正或安全性修正程式，適用於 MySQL 的 Azure 資料庫不會修補已淘汰的資料庫引擎是否有任何 bug 或安全性問題，或是針對已淘汰的資料庫引擎採取安全性措施。 不過，Azure 會繼續定期針對主機、作業系統、容器和任何其他服務相關元件執行維護和修補。
- 如果您可能遇到與 MySQL 資料庫相關的任何支援問題，我們可能無法提供您支援。 在這種情況下，您必須升級您的資料庫，才能讓我們提供任何支援。
- 您將無法為已淘汰的版本建立新的資料庫伺服器。 不過，您將能夠執行時間點復原，並為現有的伺服器建立讀取複本。
- 適用於 MySQL 的 Azure 資料庫所開發的新服務功能，僅適用于支援的資料庫伺服器版本。
- 執行時間 Sla 僅適用于適用於 MySQL 的 Azure 資料庫服務相關的問題，而不是與 Database engine 相關的 bug 所造成的任何停機時間。  
- 如果在淘汰的資料庫版本中識別出 MySQL 資料庫引擎弱點所造成的服務發生嚴重威脅，則 Azure 可能會選擇停止資料庫伺服器的計算節點，以先保護服務。 在讓伺服器上線之前，系統會要求您升級伺服器。 在升級過程中，您的資料一律會使用在服務上執行的自動備份來保護，視需要可用來還原回較舊版本。 



## <a name="next-steps"></a>後續步驟
- 請參閱適用於 MySQL 的 Azure 資料庫-單一伺服器 [支援的版本](./concepts-supported-versions.md)
- 請參閱適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽版) [支援的版本](flexible-server/concepts-supported-versions.md)
- 請參閱 MySQL 傾印 [和還原](./concepts-migrate-dump-restore.md) 以執行升級。
