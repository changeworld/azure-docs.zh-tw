---
title: Azure AD 多重要素驗證資料存放區
description: 瞭解哪些個人和組織資料 Azure AD 多重要素驗證儲存在您和您的使用者，以及哪些資料保留在來源的國家/地區內。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208347"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD 多重要素驗證的資料存放區和客戶資料

當您訂閱 Microsoft 線上服務（例如 Microsoft 365 和 Azure）時，會根據您組織所提供的位址，在地理位置 Azure AD 儲存客戶資料。 如需客戶資料儲存位置的相關資訊，請參閱 Microsoft 信任中心的[資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

雲端式 Azure AD 多重要素驗證和 Azure AD 多重要素驗證服務器處理常式，並儲存某些數量的個人資料和組織資料。 本文概述儲存的資料內容與位置。

Azure AD 多重要素驗證服務有美國、歐洲和亞太地區的資料中心。 下列活動源自區域資料中心，但有注明：

* 使用電話的多重要素驗證是來自美國資料中心，由全域提供者路由傳送。
* 來自其他地區（例如歐洲或澳大利亞）的一般用途使用者驗證要求，目前是根據使用者的位置進行處理。
* 使用 Microsoft Authenticator 應用程式的推播通知，目前是根據使用者的位置在區域資料中心內處理。
    * 裝置廠商專屬的服務，例如 Apple Push 通知，可能在使用者的位置之外。

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重要素驗證儲存的個人資料

個人資料是與特定人員相關的使用者層級資訊。 下列資料存放區包含個人資訊：

* 封鎖的使用者
* 略過的使用者
* Microsoft Authenticator 裝置權杖變更要求
* 多重要素驗證活動報表
* Microsoft Authenticator 啟用

此資訊會保留 90 天。

Azure AD 多重要素驗證不會記錄個人資料，例如使用者名稱、電話號碼或 IP 位址，但有一個 *userobjectid 為* 可識別使用者的多重要素驗證嘗試。 此資料會儲存 30 天。

### <a name="azure-ad-multifactor-authentication"></a>Azure AD 多重要素驗證

Azure 公用雲端 (不含 Azure B2C 驗證、NPS 延伸模組與 Windows Server 2016 或 2019 AD FS 配接器) 會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重要素驗證記錄中     |
| 單向 SMS                          | 在多重要素驗證記錄中     |
| 語音通話                           | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

Microsoft Azure Government、Microsoft Azure 德國、21Vianet 所操作的 Microsoft Azure、Azure B2C 驗證、NPS 延伸模組，以及 Windows Server 2016 或 2019 AD FS 配接器，會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 單向 SMS                          | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 語音通話                           | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

### <a name="multifactor-authentication-server"></a>多重要素驗證服務器

如果您部署和執行 Azure AD 多重要素驗證服務器，則會儲存下列個人資料：

> [!IMPORTANT]
> 從2019年7月1日起，Microsoft 將不再提供適用于新部署的多重要素驗證服務器。 想要從使用者要求多重要素驗證的新客戶，應該使用雲端式 Azure AD 多重要素驗證。 在7月1日前啟用多重要素驗證服務器的現有客戶將能夠下載最新版本、未來的更新，並照常產生啟用認證。

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 單向 SMS                          | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 語音通話                           | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在多重要素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多重要素驗證儲存的組織資料

組織資料是租用戶層級的資訊，其可能會公開設定或環境設定。 下列 Azure 入口網站多重要素驗證頁面的租使用者設定，可能會儲存組織資料，例如連入電話驗證要求的鎖定閾值或呼叫端識別碼資訊：

* 帳戶鎖定
* 詐騙警示
* 通知
* 通話設定

針對 Azure AD 多重要素驗證服務器，下列 Azure 入口網站頁面可能包含組織資料：

* 伺服器設定
* 一次性略過
* 快取規則
* 多重要素驗證服務器狀態

## <a name="multifactor-authentication-logs-location"></a>多重要素驗證記錄位置

下表顯示公用雲端服務記錄檔的位置。

| 公用雲端| 登入記錄 | 多重要素驗證活動報表        | 多重要素驗證服務記錄       |
|-------------|--------------|----------------------------------------|------------------------|
| 美國          | 美國           | 美國                                     | 美國                     |
| 歐洲      | 歐洲       | 美國                                     | 歐洲 <sup>2</sup>    |
| 澳大利亞   | 澳大利亞    | US<sup>1</sup>                         | 澳大利亞 <sup>2</sup> |

<sup>1</sup>OATH 代碼記錄會儲存在澳大利亞

<sup>2</sup>語音通話多重要素驗證服務記錄會儲存在美國

下表顯示主權雲端服務記錄檔的位置。

| 主權雲端                      | 登入記錄                         | 多重要素驗證活動報表 (包含個人資料) | 多重要素驗證服務記錄 |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | 德國                              | 美國                            | 美國               |
| 由世紀經營的 Microsoft Azure | 中國                                | 美國                            | 美國               |
| Microsoft 政府雲端           | 美國                                   | 美國                            | 美國               |

多重要素驗證活動報告資料包含個人資料，例如使用者主體名稱 (UPN) 和完整的電話號碼。

多重要素驗證服務記錄用來操作服務。

## <a name="next-steps"></a>後續步驟

如需雲端式 Azure AD 多重要素驗證和 Azure AD 多重要素驗證服務器所收集的使用者資訊的詳細資訊，請參閱 [Azure AD 多重要素驗證使用者資料收集](howto-mfa-reporting-datacollection.md)。
