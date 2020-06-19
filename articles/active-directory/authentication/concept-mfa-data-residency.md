---
title: Azure Multi-Factor Authentication 資料落地
description: 了解 Azure Multi-Factor Authentication 會儲存哪些有關您與您使用者的個人與組織資料，以及哪些資料會保存在來源的國家/地區。
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
ms.openlocfilehash: 2a75e9d5c8501274553edef36b475b2bd21b7875
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738796"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 的資料落地與客戶資料

Azure AD 的客戶資料會儲存在您組織訂閱 Microsoft 線上服務 (例如 Office 365 與 Azure) 時所提供地址的地理位置。 如需客戶資料儲存位置的相關資訊，請參閱 Microsoft 信任中心的[資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

雲端式 Azure Multi-Factor Authentication 與 Azure Multi-Factor Authentication Server 會處理及儲存一些個人資料與組織資料。 本文概述儲存的資料內容與位置。

若未特別註明，下列 Multi-Factor Authentication 活動目前皆源自於美國資料中心：

* 使用通話或 SMS 的雙因素驗證通常源自於美國資料中心，並透過全域提供者路由。
    * 來自歐洲或澳大利亞等其他地區的一般用途使用者驗證要求，目前由該地區的資料中心負責處理。 其他事件，例如自助式密碼重設、Azure B2C 事件，或使用 NPS 延伸模組或 AD FS 配接器的混合式案例，目前全由美國資料中心處理。
* 使用 Microsoft Authenticator 應用程式的推播通知源自美國資料中心。 此外，不同的地區也可能出現裝置廠商特定服務。
* 美國目前常以 OATH 程式碼驗證。
    * 再次重申，來自歐洲或澳大利亞等其他地區的一般用途使用者驗證事件，由該地區的資料中心負責處理。 其他事件目前則由美國資料中心處理。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 儲存的個人資料

個人資料是與特定人員相關的使用者層級資訊。 下列資料存放區包含個人資訊：

* 封鎖的使用者
* 略過的使用者
* Microsoft Authenticator 裝置權杖變更要求
* Multi-Factor Authentication 活動報告
* Microsoft Authenticator 啟用

此資訊會保留 90 天。

Azure Multi-Factor Authentication 不會記錄個人資料 (例如使用者名稱、電話號碼或 IP 位址)，但會有用以識別使用者 Multi-Factor Authentication 嘗試的 *UserObjectId*。 此資料會儲存 30 天。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Azure 公用雲端 (不含 Azure B2C 驗證、NPS 延伸模組與 Windows Server 2016 或 2019 AD FS 配接器) 會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在 Multi-Factor Authentication 記錄中     |
| 單向 SMS                          | 在 Multi-Factor Authentication 記錄中     |
| 語音通話                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

> [!NOTE]
> 無論處理驗證要求的區域為何，所有雲端的 Multi-Factor Authentication 活動報告資料存放區都會儲存在美國。 Microsoft Azure 德國、21Vianet 所操作的 Microsoft Azure，以及 Microsoft Government Cloud 有各自獨立的資料存放區，與公用雲端區域資料存放區分開，而這些資料一律儲存在美國。

Microsoft Azure Government、Microsoft Azure 德國、21Vianet 所操作的 Microsoft Azure、Azure B2C 驗證、NPS 延伸模組，以及 Windows Server 2016 或 2019 AD FS 配接器，會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 單向 SMS                          | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 語音通話                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

若您部署並執行 Azure Multi-Factor Authentication Server，則會儲存下列個人資料：

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 不再為新的部署提供 Multi-Factor Authentication Server。 新客戶應依據雲端式 Azure Multi-Factor Authentication，要求其使用者使用多重要素驗證。 7 月 1 日前啟用 Multi-Factor Authentication Server 伺服器的現有客戶，將能夠下載最新版本及未來更新，並如常產生啟用認證。

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 單向 SMS                          | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 語音通話                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 儲存的組織資料

組織資料是租用戶層級的資訊，其可能會公開設定或環境設定。 下列 Azure 入口網站 Multi-Factor Authentication 各頁面中的租用戶設定，可能會儲存組織資料，例如來電驗證要求的鎖定閾值或來電顯示資訊：

* 帳戶鎖定
* 詐騙警示
* 通知
* 通話設定

而對於 Azure Multi-Factor Authentication Server，下列 Azure 入口網站頁面可能包含組織資料：

* 伺服器設定
* 一次性略過
* 快取規則
* Multi-Factor Authentication Server 狀態

## <a name="log-data-location"></a>記錄資料位置

記錄資訊的儲存位置取決於處理區域。 大部分地理位置都具有原生 Azure Multi-Factor Authentication 功能，因此記錄資料會儲存在處理 Multi-Factor Authentication 要求的相同區域中。 沒有原生 Azure Multi-Factor Authentication 支援的地理位置，會由美國或歐洲地理位置提供服務，而記錄資料會儲存在處理 Multi-Factor Authentication 要求的相同區域中。

某些核心驗證記錄資料只會儲存在美國。 Microsoft Azure 德國與 21Vianet 所操作的 Microsoft Azure 一律儲存各自的雲端中。 Microsoft Government Cloud 記錄資料一律儲存在美國。

## <a name="next-steps"></a>後續步驟

如需雲端式 Azure Multi-Factor Authentication 與 Azure Multi-Factor Authentication Server 所收集之使用者資訊的詳細資訊，請參閱 [Azure Multi-Factor Authentication 使用者資料收集](howto-mfa-reporting-datacollection.md)。
