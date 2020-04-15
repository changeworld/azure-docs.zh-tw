---
title: Azure 多重身份驗證資料駐留
description: 瞭解 Azure 多重身份驗證存儲哪些有關您和您的使用者的個人和組織數據,以及源國家 /地區內仍保留哪些數據。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309806"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure 多重身份驗證的資料駐留和客戶資料

客戶數據由 Azure AD 儲存在地理位置中,具體取決於組織在訂閱 Microsoft 線上服務(如 Office 365 和 Azure)時提供的位址。 有關客戶資料儲存位置的資訊,您可以使用 Microsoft 信任中心的「[資料位於何處」](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

基於雲端的 Azure 多重身份驗證和 Azure 多重身份驗證伺服器進程並儲存一定數量的個人數據和組織數據。 本文概述了數據的存儲位置和位置。

以下多重身份驗證活動目前源自美國資料中心,除非另有說明:

* 使用電話或 SMS 的雙因素身份驗證通常源自美國資料中心,並由全球供應商路由。
    * 來自歐洲或澳大利亞等其他地區的通用使用者身份驗證請求目前由該區域的數據中心處理。 其他事件(如自助服務密碼重置、Azure B2C 事件或使用 NPS 擴展或 AD FS 適配器的混合方案)目前均由美國資料中心處理。
* 使用 Microsoft 身份驗證器應用的推送通知源自美國資料中心。 此外,設備供應商特定的服務也可能從不同區域發揮作用。
* OATH 代碼通常在美國進行驗證。
    * 同樣,源自其他區域(如歐洲或澳大利亞)的通用使用者身份驗證事件由該區域的數據中心處理。 其他事件目前由美國數據中心處理。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份驗證儲存的個人資料

個人數據是與特定人員關聯的使用者級資訊。 以下資料儲存包含個人資訊:

* 被封鎖的使用者
* 繞過的使用者
* 微軟驗證器裝置的權杖變更要求
* 多重身份驗證活動報告
* 微軟驗證器

此資訊將保留 90 天。

Azure 多重身份驗證不會記錄個人資料(如使用者名、電話號碼或 IP 位址),但有一個*UserObjectId*標識了對使用者的多重身份驗證嘗試。 日誌數據存儲 30 天。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

對於 Azure 公共雲(不包括 Azure B2C 身份驗證、NPS 擴展和 Windows 伺服器 2016 或 2019 AD FS 配接器),儲存以下個人資料:

| 事件類型                           | 資料儲存類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重身份驗證紀錄中     |
| 單向短信                          | 在多重身份驗證紀錄中     |
| 語音通話                           | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者 |
| 微軟驗證器通知 | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者<br />變更 Microsoft 身份驗證器裝置權杖變更時的要求 |

> [!NOTE]
> 多因素身份驗證活動報告數據存儲存儲在美國的所有雲,而不考慮處理身份驗證請求的區域。 微軟 Azure 德國、由 21Vianet 運營的 Microsoft Azure 和 Microsoft 政府雲都有獨立於公共雲區域數據存儲的獨立數據存儲,但這些數據始終存儲在美國。

對於 Microsoft Azure 政府、微軟 Azure 德國版、由 21Vianet 操作的 Microsoft Azure、Azure B2C 身份驗證、NPS 擴展和 Windows 伺服器 2016 或 2019 AD FS 適配器,儲存以下個人數據:

| 事件類型                           | 資料儲存類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存 |
| 單向短信                          | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存 |
| 語音通話                           | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者 |
| 微軟驗證器通知 | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者<br />變更 Microsoft 身份驗證器裝置權杖變更時的要求 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

若部署並執行 Azure 多重身份驗證伺服器,將儲存以下個人資料:

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起,Microsoft 將不再為新部署提供多重身份驗證伺服器。 希望用戶進行多重身份驗證的新客戶應使用基於雲的 Azure 多重身份驗證。 在 7 月 1 日之前啟動多重身份驗證伺服器的現有客戶將能夠像往常一樣下載最新版本、將來的更新並生成激活認證。

| 事件類型                           | 資料儲存類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存 |
| 單向短信                          | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存 |
| 語音通話                           | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者 |
| 微軟驗證器通知 | 在多重身份驗證紀錄中<br />多重身份驗證活動報告資料儲存<br />如果舉報了欺詐,則被阻止的使用者<br />變更 Microsoft 身份驗證器裝置權杖變更時的要求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份驗證儲存的組織資料

組織數據是租戶級資訊,可能會公開配置或環境設置。 以下 Azure 門戶多重身份驗證頁的租戶設置可能會儲存組織數據,如鎖定閾值或來電身份驗證請求的來電方 ID 資訊:

* 帳戶鎖定
* 詐騙警示
* 通知
* 通話設定

對於 Azure 多重身份驗證伺服器,以下 Azure 門戶頁面可能包含組織資料:

* 伺服器設定
* 一次性略過
* 快取規則
* 多重身份驗證伺服器狀態

## <a name="log-data-location"></a>紀錄資料位置

日誌資訊的存儲位置取決於它們在哪個區域的處理。 大多數地理位置具有本機 Azure 多重身份驗證功能,因此日誌數據存儲在處理多重身份驗證請求的同一區域中。 在沒有本機 Azure 多重身份驗證支援的地理位置中,它們由美國和歐洲地理位置提供服務,日誌數據存儲在處理多重身份驗證請求的同一區域中。

某些核心身份驗證日誌數據僅存儲在美國。 微軟 Azure 德國和由 21Vianet 操作的 Microsoft Azure 始終存儲在各自的雲中。 Microsoft 政府雲日誌數據始終存儲在美國。

## <a name="next-steps"></a>後續步驟

有關基於雲端的 Azure 多重身份驗證和 Azure 多重身份驗證伺服器收集的使用者資訊的詳細資訊,請參閱[Azure 多重身份驗證使用者資料收集](howto-mfa-reporting-datacollection.md)。
