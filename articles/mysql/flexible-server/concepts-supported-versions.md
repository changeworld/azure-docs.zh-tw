---
title: 支援的版本-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解適用於 MySQL 的 Azure 資料庫彈性伺服器支援哪些版本的 MySQL 伺服器
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 11cefb1a29f7139f137221ab6fb924cc656da15d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547192"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>適用於 MySQL 的 Azure 資料庫彈性伺服器的支援版本


> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。


適用於 MySQL 的 Azure 資料庫具彈性的伺服器是由 MySQL InnoDB 引擎使用的 [MySQL 社區版本](https://www.mysql.com/products/community/)提供技術支援。

MySQL 會使用 X.Y.Z 命名配置。 X 是主要版本，Y 是次要版本，而 Z 是 Bug 修正版本。 如需此配置的詳細資訊，請參閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 若要判斷您的 MySQL 伺服器執行個體的版本，請在 MySQL 提示字元命令下使用 `SELECT VERSION();` 命令。

適用於 MySQL 的 Azure 資料庫目前支援下列版本：

## <a name="mysql-version-57"></a>MySQL 5.7 版

Bug 修正版本：5.7.29

此服務會執行基礎硬體、OS 和資料庫引擎的自動修補。 修補包含安全性和軟體更新。 針對 MySQL 引擎，次要版本升級會包含在計劃性維護版本中。 使用者可以將修補排程設定為系統管理或定義自訂排程。 在維護排程期間，系統會套用修補檔，而伺服器可能需要重新開機，修補程序才能完成更新。 使用自訂排程，使用者可以預測修補週期，並選擇對企業影響最小的維護視窗。 一般而言，服務會遵循持續整合和發行內的每月發行排程。

若要深入瞭解此版本中的增強功能和修正，請參閱 MySQL [版本](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) 資訊。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
服務會自動管理對於錯誤 (bug) 修正版本更新的修補。 例如，5.7.29 至5.7.30。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上建立 PHP 應用程式](../../app-service/tutorial-php-mysql-app.md)<br/>
>[使用 MySQL 在 Linux 上建立 PHP 應用程式](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[使用 MySQL 建立以 JAVA 為基礎的彈簧應用程式](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>