---
title: 託管實例時區
description: 瞭解 Azure SQL 資料庫託管實例的時區特定
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256090"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL 資料庫託管實例中的時區

協調通用時間 （UTC） 是雲解決方案資料層的建議時區。 Azure SQL 資料庫託管實例還提供時區選擇，以滿足現有應用程式的需求，這些應用程式使用特定時區的隱式上下文存儲日期和時間值以及調用日期和時間函數。

T-SQL 函數（如[GETDATE（） 或](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)CLR 代碼可觀察實例級別上設置的時區。 SQL Server 代理作業還會根據實例的時區遵循計畫。

  >[!NOTE]
  > 託管實例是 Azure SQL 資料庫唯一支援時區設置的部署選項。 其他部署選項始終遵循 UTC。
如果需要解釋非 UTC 時區中的日期和時間資訊，請使用單個和池 SQL 資料庫中的["時間時區](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)"。

## <a name="supported-time-zones"></a>支援的時區

一組受支援的時區是從託管實例的基礎作業系統繼承的。 它定期更新以獲取新的時區定義，並反映對現有時區定義的更改。

[夏令時/時區更改策略](https://aka.ms/time)保證從 2010 年提前到歷史準確性。

包含受支援時區名稱的清單通過[sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)系統檢視公開。

## <a name="set-a-time-zone"></a>設置時區

只能在實例創建期間設置託管實例的時區。 預設時區為 UTC。

  >[!NOTE]
  > 無法更改現有託管實例的時區。

### <a name="set-the-time-zone-through-the-azure-portal"></a>通過 Azure 門戶設置時區

輸入新實例的參數時，請從受支援的時區清單中選擇時區。
  
![在實例創建期間設置時區](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

在[資源管理器範本](https://aka.ms/sql-mi-create-arm-posh)中指定時區 Id 屬性，以在實例創建期間設置時區。

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

本文末尾列出了 timezoneId 屬性的支援值清單。

如果未指定，時區將設置為 UTC。

## <a name="check-the-time-zone-of-an-instance"></a>檢查實例的時區

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)函數返回實例時區的顯示名稱。

## <a name="cross-feature-considerations"></a>交叉功能注意事項

### <a name="restore-and-import"></a>還原和導入

可以從具有不同時區設置的實例或伺服器還原備份檔案或將資料導入託管實例。 請務必謹慎這樣做。 分析應用程式行為以及查詢和報告的結果，就像在具有不同時區設置的兩個 SQL Server 實例之間傳輸資料一樣。

### <a name="point-in-time-restore"></a>時間點還原

執行時間點還原時，還原的時間將解釋為 UTC 時間。 這樣可以避免由於夏令時及其潛在變化而導致的任何模糊性。

### <a name="auto-failover-groups"></a>自動容錯移轉群組

不會強制執行在容錯移轉組中的主實例和輔助實例上使用相同的時區，但我們強烈建議這樣做。

  >[!WARNING]
  > 我們強烈建議您在容錯移轉組中對主實例和輔助實例使用相同的時區。 由於某些罕見的用例，因此不會強制執行在主實例和輔助實例之間保持相同的時區。 請務必瞭解，在手動或自動容錯移轉的情況下，輔助實例將保留其原始時區。

## <a name="limitations"></a>限制

- 無法更改現有託管實例的時區。
- 從 SQL Server 代理作業啟動的外部進程不觀察到實例的時區。

## <a name="list-of-supported-time-zones"></a>受支援的時區清單

| **時區 ID** | **時區顯示名稱** |
| --- | --- |
| 國際換日線標準時間 | （UTC-12：00）國際日期線西 |
| UTC-11 | （UTC-11：00）協調通用時間-11 |
| 阿留申標準時間 | （UTC-10：00）阿留申群島 |
| 夏威夷標準時間 | （UTC-10：00）夏威夷 |
| 馬奎薩標準時間 | （UTC-09：30）瑪律克薩群島 |
| 阿拉斯加標準時間 | （UTC-09：00）阿拉斯加 |
| UTC-09 | （UTC-09：00）協調通用時間-09 |
| 太平洋標準時間（墨西哥） | （UTC-08：00）下加利福尼亞州 |
| UTC-08 | （UTC-08：00）協調通用時間-08 |
| 太平洋標準時間 | （UTC-08：00）太平洋時間（美國&加拿大） |
| 美國山區標準時間 | （UTC-07：00）亞利桑那州 |
| 山區標準時間 (墨西哥) | （UTC-07：00）奇瓦瓦、拉巴斯、馬紮特蘭 |
| 美加山區標準時間 | （UTC-07：00）山地時間（美國&加拿大） |
| 中美洲標準時間 | （UTC-06：00）中美洲 |
| 美加中部標準時間 | （UTC-06：00）中部時間（美國&加拿大） |
| 復活節島標準時間 | （UTC-06：00）復活節島 |
| 中部標準時間 (墨西哥) | （UTC-06：00）瓜達拉哈拉， 墨西哥城， 蒙特雷 |
| 加拿大中部標準時間 | （UTC-06：00）薩斯喀徹爾省 |
| 南美洲太平洋標準時間 | （UTC-05：00）波哥大、 利馬、 基多、 裡奧布蘭科 |
| 東部標準時間（墨西哥） | （UTC-05：00）切圖馬爾 |
| 美加東部標準時間 | （UTC-05：00）東部時間（美國&加拿大） |
| 海地標準時間 | （UTC-05：00）海地 |
| 古巴標準時間 | （UTC-05：00）哈瓦 那 |
| 美東標準時間 | （UTC-05：00）印第安那州（東部） |
| 特克斯和凱科斯標準時間 | （UTC-05：00）土克斯及開科斯群島 |
| 巴拉圭標準時間 | （UTC-04：00）亞松森 |
| 大西洋標準時間 | （UTC-04：00）大西洋時間（加拿大） |
| 委內瑞拉標準時間 | （UTC-04：00）卡拉卡斯 |
| 巴西中部標準時間 | （UTC-04：00）庫亞巴 |
| 南美洲西部標準時間 | （UTC-04：00）喬治城， 拉巴斯， 馬納斯， 聖胡安 |
| 太平洋 SA 標準時間 | （UTC-04：00）聖地牙哥 |
| 紐芬蘭標準時間 | （UTC-03：30）紐芬蘭 |
| 托坎丁標準時間 | （UTC-03：00）阿拉瓜伊納 |
| E. 南美洲標準時間 | （UTC-03：00）巴西利亞 |
| 南美洲東部標準時間 | （UTC-03：00）卡耶納， 福塔雷薩 |
| 阿根廷標準時間 | （UTC-03：00）布宜諾斯艾利斯市 |
| 格陵蘭標準時間 | （UTC-03：00）格陵蘭 |
| 蒙特維多標準時間 | （UTC-03：00）蒙特維多 |
| 馬加蘭內斯標準時間 | （UTC-03：00）蓬塔阿雷納斯 |
| 聖皮爾標準時間 | （UTC-03：00）聖皮爾和密克隆 |
| 巴伊亞標準時間 | （UTC-03：00）薩爾瓦多 |
| UTC-02 | （UTC-02：00）協調通用時間-02 |
| 大西洋中部標準時間 | （UTC-02：00）大西洋中部 - 舊 |
| 亞速爾標準時間 | （UTC-01：00）亞速爾群島 |
| 維德角標準時間 | （UTC-01：00）維德角是. |
| UTC | (UTC) 國際標準時間 |
| GMT 標準時間 | （UTC=00：00）都柏林， 愛丁堡， 里斯本， 倫敦 |
| 格林威治標準時間 | （UTC=00：00）蒙羅維亞， 雷克雅維克 |
| W. 歐洲標準時間 | （UTC=01：00）阿姆斯特丹、 柏林、 伯恩、 羅馬、 斯德哥爾摩、 維也納 |
| 中歐標準時間 | （UTC=01：00）貝爾格勒、 布拉提斯拉瓦、 布達佩斯、 盧布亞納、 布拉格 |
| 羅馬標準時間 | （UTC=01：00）布魯塞爾、哥本哈根、馬德里、巴黎 |
| 摩洛哥標準時間 | （UTC=01：00）卡薩布蘭卡 |
| 聖多美標準時間 | （UTC=01：00）聖多美 |
| 歐洲中部標準時間 | （UTC=01：00）塞拉耶佛、 斯高彼亞、 華沙、 札格雷布 |
| W. 中非標準時間 | （UTC=01：00）西非 |
| 約旦標準時間 | （UTC=02：00）安曼 |
| GTB 標準時間 | （UTC=02：00）雅典， 布加勒斯特 |
| 中東標準時間 | （UTC=02：00）貝魯特 |
| 埃及標準時間 | （UTC=02：00）開羅 |
| E. 歐洲標準時間 | （UTC=02：00）基希訥烏 |
| 敘利亞標準時間 | （UTC=02：00）大馬士革 |
| 西岸標準時間 | （UTC=02：00）加沙，希伯倫 |
| 南非標準時間 | （UTC=02：00）哈拉雷， 普利托里亞 |
| FLE 標準時間 | （UTC=02：00）赫爾辛基、 基輔、 里加、 索菲亞、 塔林 、 維爾紐斯 |
| 以色列標準時間 | （UTC=02：00）耶路撒冷 |
| 加里甯格勒標準時間 | （UTC=02：00）加里甯格勒 |
| 蘇丹標準時間 | （UTC=02：00）喀土穆 |
| 利比亞標準時間 | （UTC=02：00）的黎波里 |
| 納米比亞標準時間 | （UTC=02：00）溫吐克 |
| 伊拉克標準時間 | （UTC=03：00）巴格達 |
| 土耳其標準時間 | （UTC=03：00）伊斯坦布爾 |
| 沙烏地阿拉伯標準時間 | （UTC=03：00）科威特、 利雅德 |
| 白俄羅斯標準時間 | （UTC=03：00）明斯克 |
| 俄羅斯標準時間 | （UTC=03：00）莫斯科， 聖彼德堡 |
| E. 非洲標準時間 | （UTC=03：00）奈洛比 |
| 伊朗標準時間 | （UTC+03：30）德黑蘭 |
| 沙烏地阿拉伯標準時間 | （UTC=04：00）阿布達比， 馬斯喀特 |
| 阿斯特拉罕標準時間 | （UTC=04：00）阿斯特拉罕， 烏裡揚諾夫斯克 |
| 亞塞拜然標準時間 | （UTC=04：00）巴庫 |
| 俄羅斯時區 3 | （UTC=04：00）伊薩馬拉伊熱夫斯克 |
| 模里西斯標準時間 | （UTC=04：00）路易士港 |
| 薩拉托夫標準時間 | （UTC=04：00）薩拉托夫 |
| 喬治亞標準時間 | （UTC=04：00）第比利斯 |
| 伏爾加格勒標準時間 | （UTC=04：00）伏爾加格勒 |
| 高加索標準時間 | （UTC=04：00）葉里溫 |
| 阿富汗標準時間 | （UTC+04：30）喀布爾 |
| 西亞標準時間 | （UTC=05：00）阿什哈巴德， 塔什干 |
| 伊卡特林堡標準時間 | （UTC=05：00）葉卡捷琳堡 |
| 巴基斯坦標準時間 | （UTC=05：00）伊斯蘭馬巴德， 卡拉奇 |
| 印度標準時間 | （UTC+05：30）欽奈、加爾各答、孟買、新德里 |
| 斯里蘭卡標準時間 | （UTC+05：30）斯裡·賈亞瓦德內普拉 |
| 尼泊爾標準時間 | （UTC+05：45）加德滿都 |
| 中亞標準時間 | （UTC+06：00）阿斯坦納 |
| 孟加拉國標準時間 | （UTC+06：00）達卡 |
| 鄂木斯克標準時間 | （UTC+06：00）鄂木斯克 |
| 緬甸標準時間 | （UTC+06：30）仰光（仰光） |
| 東南亞標準時間 | （UTC+07：00）曼谷、 河內 、 雅加達 |
| 阿勒泰標準時間 | （UTC+07：00）巴爾瑙爾，戈爾諾-阿勒泰斯克 |
| W. 蒙古標準時間 | （UTC+07：00）霍夫德 |
| 北亞標準時間 | （UTC+07：00）克拉斯諾亞爾斯克 |
| N. 中亞標準時間 | （UTC+07：00）新西伯利亞 |
| 托木斯克標準時間 | （UTC+07：00）托木斯克 |
| 中國標準時間 | （UTC=08：00）北京、重慶、香港、烏魯木齊 |
| 東北亞標準時間 | （UTC=08：00）伊爾庫茨克 |
| 新加坡標準時間 | （UTC=08：00）吉隆坡， 新加坡 |
| W. 澳大利亞標準時間 | （UTC=08：00）珀 斯 |
| 台北標準時間 | （UTC=08：00）臺北 |
| 烏蘭巴托標準時間 | （UTC=08：00）烏蘭巴托 |
| Aus 中環 W. 標準時間 | （UTC+08：45）歐克拉 |
| 跨貝加爾標準時間 | （UTC=09：00）奇塔 |
| 東京標準時間 | （UTC=09：00）大阪， 札幌， 東京 |
| 朝鮮標準時間 | （UTC=09：00）平壤 |
| 韓國標準時間 | （UTC=09：00）首爾 |
| 亞庫茲克標準時間 | （UTC=09：00）亞庫次克 |
| 中部 澳大利亞標準時間 | （UTC+09：30）阿德萊德 |
| 澳洲中部標準時間 | （UTC+09：30）達爾文 |
| E. 澳大利亞標準時間 | （UTC=10：00）布里斯班 |
| 澳洲東部標準時間 | （UTC=10：00）坎培拉、墨爾本、悉尼 |
| 西太平洋標準時間 | （UTC=10：00）關島，莫爾斯比港 |
| 塔斯馬尼亞標準時間 | （UTC=10：00）霍巴特 |
| 海參威標準時間 | （UTC=10：00）符拉迪沃斯托克 |
| 豪勳爵標準時間 | （UTC+10：30）豪勳爵島 |
| 布幹維爾標準時間 | （UTC=11：00）布幹維爾島 |
| 俄羅斯時區 10 | （UTC=11：00）喬庫爾達赫 |
| 馬加丹標準時間 | （UTC=11：00）馬加丹 |
| 諾福克標準時間 | （UTC=11：00）諾福克島 |
| 薩哈林標準時間 | （UTC=11：00）庫頁島 |
| 太平洋中部標準時間 | （UTC=11：00）新赫里多尼亞所羅門 |
| 俄羅斯時區 11 | （UTC=12：00）阿納迪爾， 彼得羅巴甫洛夫斯克-堪察茨基 |
| 紐西蘭標準時間 | （UTC=12：00）奧克蘭， 威靈頓 |
| UTC+12 | （UTC=12：00）協調通用時間+12 |
| 斐濟標準時間 | （UTC=12：00）斐濟 |
| 堪察加標準時間 | （UTC=12：00）彼得羅巴甫洛夫斯克-堪察茨基 - 老 |
| 查塔姆群島標準時間 | （UTC+12：45）查塔姆群島 |
| UTC+13 | （UTC=13：00）協調通用時間+13 |
| 東加標準時間 | （UTC=13：00）努庫阿盧瓦 |
| 薩摩亞標準時間 | （UTC=13：00）薩摩亞 |
| 線島標準時間 | （UTC=14：00）基裡蒂馬蒂島 |

## <a name="see-also"></a>另請參閱 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [系統time_zone_info（轉算-SQL）](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
