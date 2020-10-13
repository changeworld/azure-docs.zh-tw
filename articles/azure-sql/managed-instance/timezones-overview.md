---
title: Azure SQL 受控執行個體時區
description: 瞭解 Azure SQL 受控執行個體的時區詳細資料
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: 7b95ddfdb75dd5e5951a9c95442798692582fe6a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978351"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Azure SQL 受控執行個體中的時區
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

國際標準時間 (UTC) 是雲端解決方案資料層的建議時區。 Azure SQL 受控執行個體也提供可選擇的時區，以符合儲存日期和時間值的現有應用程式需求，並以特定時區的隱含內容呼叫日期和時間函數。

[GETDATE ( # B1](/sql/t-sql/functions/getdate-transact-sql)或 CLR 程式碼之類的 t-sql 函數會觀察實例層級上設定的時區。 SQL Server Agent 作業也會根據實例的時區進行排程。

  > [!NOTE]
  > Azure SQL Database 不支援時區設定;一律遵循 UTC。 如果您需要解讀非 UTC 時區的日期和時間資訊，[請使用 SQL Database 中的時區。](/sql/t-sql/queries/at-time-zone-transact-sql)

## <a name="supported-time-zones"></a>支援的時區

受管理的實例的基礎作業系統會繼承一組支援的時區。 它會定期更新以取得新的時區定義，並反映現有的時區定義變更。

[日光節約時間/時區變更原則](https://aka.ms/time) 可保證從2010轉寄的歷史精確度。

系統會透過 [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) 系統檢視，公開具有支援時區名稱的清單。

## <a name="set-a-time-zone"></a>設定時區

受控實例的時區只能在建立實例期間設定。 預設時區是 UTC。

  >[!NOTE]
  > 無法變更現有受控實例的時區。

### <a name="set-the-time-zone-through-the-azure-portal"></a>透過 Azure 入口網站設定時區

當您為新的實例輸入參數時，請從支援的時區清單中選取時區。
  
![在實例建立期間設定時區](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

在 [Resource Manager 範本](https://aka.ms/sql-mi-create-arm-posh) 中指定 timezoneId 屬性，以在建立實例期間設定時區。

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

TimezoneId 屬性支援的值清單位於本文結尾。

如果未指定，時區會設定為 UTC。

## <a name="check-the-time-zone-of-an-instance"></a>檢查實例的時區

[CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql)函數會傳回實例的時區顯示名稱。

## <a name="cross-feature-considerations"></a>跨功能考慮

### <a name="restore-and-import"></a>還原和匯入

您可以從實例或具有不同時區設定的伺服器，還原備份檔案或將資料匯入至受控實例。 請務必小心。 分析應用程式行為和查詢和報告的結果，就像您在兩個 SQL Server 實例之間，使用不同時區設定傳送資料時一樣。

### <a name="point-in-time-restore"></a>時間點還原

當您執行時間點還原時，還原至的時間會轉譯為 UTC 時間。 如此一來，就能避免因日光節約時間而造成的任何歧義，以及其可能的變更。

### <a name="auto-failover-groups"></a>自動容錯移轉群組

在容錯移轉群組中的主要和次要實例上使用相同的時區並不會強制執行，但我們強烈建議您這樣做。

  >[!WARNING]
  > 強烈建議您針對容錯移轉群組中的主要和次要實例，使用相同的時區。 因為某些罕見的使用案例不會強制執行主要和次要實例之間的相同時區。 請務必瞭解，在手動或自動容錯移轉的情況下，次要實例將會保留其原始時區。

## <a name="limitations"></a>限制

- 無法變更現有受控實例的時區。 因應措施是，使用適當的時區建立新的受控實例，然後執行手動備份和還原，或建議您執行 [跨實例的時間點還原](https://docs.microsoft.com/azure/azure-sql/managed-instance/point-in-time-restore?tabs=azure-portal#restore-an-existing-database)。
- 從 SQL Server Agent 作業啟動的外部進程不會觀察實例的時區。

## <a name="list-of-supported-time-zones"></a>支援的時區清單

| **時區識別碼** | **時區顯示名稱** |
| --- | --- |
| 國際換日線標準時間 |  (UTC-12:00) 國際日期行西部 |
| UTC-11 |  (UTC-11:00) 國際標準時間-11 |
| 阿留申標準時間 |  (UTC-10:00) 阿留申群島 |
| 夏威夷標準時間 |  (UTC-10:00) 夏威夷 |
| 馬克沙斯標準時間 |  (UTC-09:30) 馬克沙斯群島 |
| 阿拉斯加標準時間 |  (UTC-09:00) 阿拉斯加 |
| UTC-09 |  (UTC-09:00) 國際標準時間-09 |
| 太平洋標準時間 (墨西哥)  |  (UTC-08:00) 中加州 |
| UTC-08 |  (UTC-08:00) 國際標準時間-08 |
| 太平洋標準時間 |  (UTC-08:00) 太平洋時間 (美國 & 加拿大)  |
| 美國山區標準時間 |  (UTC-07:00) 亞利桑那州 |
| 山區標準時間 (墨西哥) |  (UTC-07:00) 赤瓦瓦、La 巴斯、馬薩特蘭 |
| 美加山區標準時間 |  (UTC-07:00) 山區時間 (美國 & 加拿大)  |
| 中美洲標準時間 |  (UTC-06:00) 中美洲 |
| 美加中部標準時間 |  (UTC-06:00) 中部時間 (美國 & 加拿大)  |
| 復活節島標準時間 |  (UTC-06:00) 復活節島 |
| 中部標準時間 (墨西哥) |  (UTC-06:00) Guadalajara、墨西哥 City、蒙特雷 |
| 加拿大中部標準時間 |  (UTC-06:00) 薩斯 |
| 南美洲太平洋標準時間 |  (UTC-05:00) 波哥大，利馬，基多，Rio 約布蘭科 |
| 東部標準時間 (墨西哥)  |  (UTC-05:00) 切圖馬爾 |
| 美加東部標準時間 |  (UTC-05:00) 東部時間 (美國 & 加拿大)  |
| 海地標準時間 |  (UTC-05:00) 海地 |
| 古巴標準時間 |  (UTC-05:00) 哈瓦那 |
| 美東標準時間 |  (UTC-05:00) 印地安 (東部)  |
| 特克斯和凱科斯標準時間 |  (UTC-05:00) 特克斯和凱科斯 |
| 巴拉圭標準時間 |  (UTC-04:00) 亞松森 |
| 大西洋標準時間 |  (UTC-04:00) 大西洋時間 (加拿大)  |
| 委內瑞拉標準時間 |  (UTC-04:00) 卡拉卡斯 |
| 巴西中部標準時間 |  (UTC-04:00) 古雅巴 |
| 南美洲西部標準時間 |  (UTC-04:00) 喬治城、La 巴斯、瑪瑙斯、San Juan |
| 太平洋 SA 標準時間 |  (UTC-04:00) 聖地牙哥 |
| 紐芬蘭標準時間 |  (UTC-03:30) 紐芬蘭 |
| Tocantins 標準時間 |  (UTC-03:00) Araguaina |
| E. 南美洲標準時間 |  (UTC-03:00) 巴西利亞 |
| 南美洲東部標準時間 |  (UTC-03:00) 開雲、Fortaleza |
| 阿根廷標準時間 |  (UTC-03:00) 布宜諾斯艾利斯的城市 |
| 格陵蘭標準時間 |  (UTC-03:00) 格陵蘭 |
| 蒙特維多標準時間 |  (UTC-03:00) 蒙特維多 |
| 麥哲倫標準時間 |  (UTC-03:00) Punta Arenas |
| 聖聖皮爾標準時間 |  (UTC-03:00) 聖聖匹島 |
| 巴伊亞標準時間 |  (UTC-03:00)  |
| UTC-02 |  (UTC-02:00) 國際標準時間-02 |
| 大西洋中部標準時間 |  (UTC-02:00) Mid-Atlantic-舊 |
| 亞速爾標準時間 |  (UTC-01:00) 亞速爾 |
| 維德角標準時間 |  (UTC-01:00) 維德角。 |
| UTC | (UTC) 國際標準時間 |
| GMT 標準時間 |  (UTC + 00： 00) 都柏林，愛丁堡，里斯本，倫敦 |
| 格林威治標準時間 |  (UTC + 00： 00) 蒙羅維亞，雷克雅維克 |
| W. 歐洲標準時間 |  (UTC + 01： 00) 阿姆斯特丹，柏林，伯恩，羅馬，斯德哥爾摩，維也納 |
| 中歐標準時間 |  (UTC + 01： 00) 貝爾格勒，Bratislava，布達佩斯，盧布亞納，布拉格 |
| 羅馬標準時間 |  (UTC + 01： 00) 布魯塞爾，哥本哈根，馬德里，巴黎 |
| 摩洛哥標準時間 |  (UTC + 01： 00) Casablanca |
| 聖多美書籍標準時間 |  (UTC + 01： 00) 聖保羅書籍 |
| 歐洲中部標準時間 |  (UTC + 01： 00) 塞拉耶佛，斯高彼亞，華沙，札格雷布 |
| W. 中部非洲標準時間 |  (UTC + 01： 00) West 中部非洲 |
| 約旦標準時間 |  (UTC + 02： 00) 安曼 |
| GTB 標準時間 |  (UTC + 02： 00) 雅典，布加勒斯特 |
| 中東標準時間 |  (UTC + 02： 00) 貝魯特 |
| 埃及標準時間 |  (UTC + 02： 00) Cairo |
| E. 歐洲標準時間 |  (UTC + 02： 00) 奇西瑙 |
| 敘利亞標準時間 |  (UTC + 02： 00) 大馬士革 |
| 西 Bank 標準時間 |  (UTC + 02： 00) 加薩，希伯侖 |
| 南非標準時間 |  (UTC + 02： 00) 哈拉雷，普利托里亞 |
| FLE 標準時間 |  (UTC + 02： 00) 赫爾辛基、基輔、里加、索菲亞、塔林、維爾紐斯 |
| 以色列標準時間 |  (UTC + 02： 00) 耶路撒冷 |
| 卡裡的標準時間 |  (UTC + 02： 00) 卡裡 |
| 蘇丹標準時間 |  (UTC + 02： 00) 喀土穆 |
| 利比亞標準時間 |  (UTC + 02： 00) 的黎波里 |
| 納米比亞標準時間 |  (UTC + 02： 00) Windhoek |
| 伊拉克標準時間 |  (UTC + 03： 00) 巴格達 |
| 土耳其標準時間 |  (UTC + 03： 00) 伊斯坦布爾 |
| 沙烏地阿拉伯標準時間 |  (UTC + 03： 00) 科威特，利雅德 |
| 白俄羅斯標準時間 |  (UTC + 03： 00) 明斯克 |
| 俄羅斯標準時間 |  (UTC + 03： 00) 莫斯科，聖聖彼德堡 |
| E. 非洲標準時間 |  (UTC + 03： 00) 奈洛比 |
| 伊朗標準時間 |  (UTC + 03： 30) 德黑蘭 |
| 沙烏地阿拉伯標準時間 |  (UTC + 04： 00) 阿布達比阿布達比，馬斯喀特 |
| 阿斯特拉罕標準時間 |  (UTC + 04： 00) 阿斯特拉罕，Ulyanovsk |
| 亞塞拜然標準時間 |  (UTC + 04： 00) 巴庫 |
| 俄羅斯時間區域3 |  (UTC + 04： 00) Izhevsk，沙馬拉 |
| 模里西斯標準時間 |  (UTC + 04： 00) 埠港 |
| 薩拉托夫標準時間 |  (UTC + 04： 00) 薩拉托夫 |
| 喬治亞標準時間 |  (UTC + 04： 00) 第比利斯 |
| 伏爾加格勒標準時間 |  (UTC + 04： 00) 伏爾加格勒 |
| 高加索標準時間 |  (UTC + 04： 00) 葉里溫 |
| 阿富汗標準時間 |  (UTC + 04： 30) 喀布爾 |
| 西亞標準時間 |  (UTC + 05： 00) Ashgabat，塔什干 |
| 伊卡特林堡標準時間 |  (UTC + 05： 00) 的卡裡 |
| 巴基斯坦標準時間 |  (UTC + 05： 00) 伊斯蘭馬巴德，卡拉奇 |
| 印度標準時間 |  (UTC + 05： 30) 辰內，加爾各答，孟買，新德里 |
| 斯里蘭卡標準時間 |  (UTC + 05： 30) 斯里蘭卡 Jayawardenepura |
| 尼泊爾標準時間 |  (UTC + 05： 45) 加德滿都 |
| 中亞標準時間 |  (UTC + 06： 00) 阿斯坦納 |
| 孟加拉國標準時間 |  (UTC + 06： 00) 達卡 |
| 鄂木斯克標準時間 |  (UTC + 06： 00) 鄂木斯克 |
| 緬甸標準時間 |  (UTC + 06： 30) 仰光 (仰光)  |
| 東南亞標準時間 |  (UTC + 07： 00) 曼谷，河內，雅加達 |
| 阿勒泰標準時間 |  (UTC + 07： 00) 巴爾瑙爾，Gorno-Altaysk |
| W. 蒙古標準時間 |  (UTC + 07： 00) 哈霍德 |
| 北亞標準時間 |  (UTC + 07： 00) 的克拉 |
| N. 中亞標準時間 |  (UTC + 07： 00) 新西伯利亞 |
| 托木斯克標準時間 |  (UTC + 07： 00) 托木斯克 |
| 中國標準時間 |  (UTC + 08： 00) 北京，重慶，香港特別行政區，烏魯木齊 |
| 東北亞標準時間 |  (UTC + 08： 00) Irkutsk |
| 新加坡標準時間 |  (UTC + 08： 00) 吉隆坡，新加坡 |
| W. 澳大利亞標準時間 |  (UTC + 08： 00) 的 |
| 台北標準時間 |  (UTC + 08： 00) 臺北 |
| 庫倫標準時間 |  (UTC + 08： 00) 庫倫 |
| 澳大利亞中部 W. 標準時間 |  (UTC + 08： 45) 尤克拉 |
| 外貝加爾標準時間 |  (UTC + 09： 00) 赤塔 |
| 東京標準時間 |  (UTC + 09： 00) 大阪，札幌，東京 |
| 北韓國標準時間 |  (UTC + 09： 00) 平壤 |
| 韓國標準時間 |  (UTC + 09： 00) 首爾 |
| 亞庫茲克標準時間 |  (UTC + 09： 00) 雅茲克 |
| 中部 澳大利亞標準時間 |  (UTC + 09： 30) 阿阿德 |
| 澳洲中部標準時間 |  (UTC + 09： 30) Darwin |
| E. 澳大利亞標準時間 |  (UTC + 10： 00) 布里斯班 |
| 澳洲東部標準時間 |  (UTC + 10： 00) 坎培拉，墨爾本，悉尼 |
| 西太平洋標準時間 |  (UTC + 10： 00) 關島，埠港 |
| 塔斯馬尼亞標準時間 |  (UTC + 10： 00) 霍巴特 |
| 海參威標準時間 |  (UTC + 10： 00) 符拉迪沃斯托克 |
| 羅豪羅豪標準時間 |  (UTC + 10： 30) 羅豪羅豪島 |
| 布幹維爾標準時間 |  (UTC + 11： 00) 布幹維爾島 |
| 俄羅斯時區10 |  (UTC + 11： 00) Chokurdakh |
| 馬加丹標準時間 |  (UTC + 11： 00) 馬加丹 |
| 諾福克標準時間 |  (UTC + 11： 00) 諾福克島 |
| 庫頁島標準時間 |  (UTC + 11： 00) 庫頁島 |
| 太平洋中部標準時間 |  (UTC + 11： 00) 索羅門群島，新赫里多尼亞 |
| 俄羅斯時區11 |  (UTC + 12： 00) 阿納底，Petropavlovsk-Kamchatsky |
| 紐西蘭標準時間 |  (UTC + 12： 00) 奧克蘭，威靈頓 |
| UTC + 12 |  (UTC + 12： 00) 國際標準時間 + 12 |
| 斐濟標準時間 |  (UTC + 12： 00) 斐濟 |
| 勘察加標準時間 |  (UTC + 12： 00) Petropavlovsk-Kamchatsky-舊 |
| 查塔姆群島標準時間 |  (UTC + 12： 45) 查塔姆群島 |
| UTC + 13 |  (UTC + 13： 00) 國際標準時間 + 13 |
| 東加標準時間 |  (UTC + 13： 00) Nuku'alofa |
| 薩摩亞標準時間 |  (UTC + 13： 00) 薩摩亞 |
| 行群島標準時間 |  (UTC + 14： 00) Kiritimati 島 |

## <a name="see-also"></a>另請參閱 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-id-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-sql) ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
