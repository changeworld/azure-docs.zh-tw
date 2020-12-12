---
title: Azure AD Multi-Factor Authentication 資料存放區
description: 瞭解 Azure AD Multi-Factor Authentication 儲存的個人和組織資料，以及您的使用者，以及哪些資料會保留在來源的國家/地區內。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788512db242bf1a1c6f18ffc0ee773bd3372aa42
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355860"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 的資料存放區和客戶資料

當您訂閱 Microsoft 線上服務（例如 Microsoft 365 和 Azure）時，會根據您組織所提供的位址，在地理位置 Azure AD 儲存客戶資料。 如需客戶資料儲存位置的相關資訊，請參閱 Microsoft 信任中心的[資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

雲端式 Azure AD Multi-Factor Authentication 和 Azure Multi-Factor Authentication Server 處理常式，並儲存某些數量的個人資料和組織資料。 本文概述儲存的資料內容與位置。

Azure AD Multi-Factor Authentication 服務在美國、歐洲和亞太地區都有資料中心。 下列活動源自區域資料中心，但有注明：

* 使用電話的多重要素驗證是來自美國資料中心，由全域提供者路由傳送。
* 來自其他地區（例如歐洲或澳大利亞）的一般用途使用者驗證要求，目前是根據使用者的位置進行處理。
* 使用 Microsoft Authenticator 應用程式的推播通知，目前是根據使用者的位置在區域資料中心內處理。
    * 裝置廠商專屬的服務，例如 Apple Push 通知，可能在使用者的位置之外。

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 儲存的個人資料

個人資料是與特定人員相關的使用者層級資訊。 下列資料存放區包含個人資訊：

* 封鎖的使用者
* 略過的使用者
* Microsoft Authenticator 裝置權杖變更要求
* Multi-Factor Authentication 活動報告
* Microsoft Authenticator 啟用

此資訊會保留 90 天。

Azure AD Multi-Factor Authentication 不會記錄個人資料，例如使用者名稱、電話號碼或 IP 位址，但會有可識別使用者 Multi-Factor Authentication 嘗試的 *userobjectid 為* 。 此資料會儲存 30 天。

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD 多重要素驗證

Azure 公用雲端 (不含 Azure B2C 驗證、NPS 延伸模組與 Windows Server 2016 或 2019 AD FS 配接器) 會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在 Multi-Factor Authentication 記錄中     |
| 單向 SMS                          | 在 Multi-Factor Authentication 記錄中     |
| 語音通話                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

> [!NOTE]
> 無論處理驗證要求的區域為何，所有雲端的 Multi-Factor Authentication 活動報告資料存放區都會儲存在美國。 Microsoft Azure 德國、21Vianet 所操作的 Microsoft Azure，以及 Microsoft Government Cloud 有各自獨立的資料存放區，與公用雲端區域資料存放區分開，而這些資料一律儲存在美國。 這些資料存放區包含個人識別資訊 (PII) 例如使用者主體名稱 (UPN) 和完整的電話號碼。 

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
> 自 2019 年 7 月 1 日起，Microsoft 不再為新的部署提供 Multi-Factor Authentication Server。 想要從使用者要求多重要素驗證的新客戶，應該使用雲端式 Azure AD Multi-Factor Authentication。 7 月 1 日前啟用 Multi-Factor Authentication Server 伺服器的現有客戶，將能夠下載最新版本及未來更新，並如常產生啟用認證。

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 單向 SMS                          | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區 |
| 語音通話                           | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者 |
| Microsoft Authenticator 通知 | 在 Multi-Factor Authentication 記錄中<br />Multi-Factor Authentication 活動報告資料存放區<br />如果回報詐騙則封鎖使用者<br />當 Microsoft Authenticator 裝置權杖變更時，變更要求 |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 儲存的組織資料

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

記錄資訊的儲存位置取決於處理區域。 大部分的地理位置都有原生 Azure AD Multi-Factor Authentication 功能，因此記錄資料會儲存在處理 Multi-Factor Authentication 要求的相同區域中。 在沒有原生 Azure AD Multi-Factor Authentication 支援的地理位置中，它們是由美國或歐洲地理位置提供服務，而記錄資料則儲存在處理 Multi-Factor Authentication 要求的相同區域中。

某些核心驗證記錄資料只會儲存在美國。 Microsoft Azure 德國與 21Vianet 所操作的 Microsoft Azure 一律儲存各自的雲端中。 Microsoft Government Cloud 記錄資料一律儲存在美國。

## <a name="next-steps"></a>後續步驟

如需雲端式 Azure AD Multi-Factor Authentication 和 Azure Multi-Factor Authentication Server 收集哪些使用者資訊的詳細資訊，請參閱 [Azure AD Multi-Factor Authentication 使用者資料收集](howto-mfa-reporting-datacollection.md)。
