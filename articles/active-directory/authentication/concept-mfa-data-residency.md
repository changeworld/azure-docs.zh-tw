---
title: Azure 多因素驗證資料常駐
description: 瞭解 Azure 多因素驗證對您和您的使用者所儲存的個人和組織資料，以及哪些資料會保留在原始國家/地區。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309806"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>適用于 Azure 多因素驗證的資料常駐和客戶資料

客戶資料是根據貴組織在訂閱 Microsoft 線上服務（例如 Office 365 和 Azure）時所提供的位址，Azure AD 儲存在地理位置。 如需您的客戶資料儲存位置的資訊，您可以使用 Microsoft 信任中心的[資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

以雲端為基礎的 Azure 多因素驗證和 Azure 多因素驗證服務器處理，並儲存一些個人資料和組織資料。 本文概述資料的儲存和位置。

下列多重要素驗證活動目前源自于美國資料中心，但另有注明：

* 使用電話或 SMS 的雙因素驗證通常源自于美國資料中心，並由全域提供者路由傳送。
    * 來自其他地區（例如歐洲或澳大利亞）的一般用途使用者驗證要求目前由該區域中的資料中心處理。 使用 NPS 擴充功能或 AD FS 介面卡的其他事件（例如，自助式密碼重設、Azure B2C 事件或混合式案例）目前由美國資料中心處理。
* 使用 Microsoft Authenticator 應用程式的推播通知源自于美國資料中心。 此外，裝置廠商特定的服務也可能會從不同的區域中播放。
* OATH 代碼目前的驗證方式通常是在美國
    * 同樣地，源自其他地區（例如歐洲或澳大利亞）的一般用途使用者驗證事件，會由該區域中的資料中心處理。 美國資料中心目前正在處理其他事件。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure 多因素驗證所儲存的個人資料

個人資料是與特定人員相關聯的使用者層級資訊。 下列資料存放區包含個人資訊：

* 封鎖的使用者
* 略過的使用者
* Microsoft Authenticator 裝置權杖變更要求
* 多重要素驗證活動報告
* Microsoft Authenticator 啟用

此資訊會保留90天。

Azure 多因素驗證不會記錄個人資料（例如使用者名稱、電話號碼或 IP 位址），但會有一個*userobjectid 為*來識別對使用者的多重要素驗證嘗試。 記錄資料會儲存30天。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

針對 Azure 公用雲端（不包括 Azure B2C 驗證、NPS 擴充功能和 Windows Server 2016 或 2019 AD FS 介面卡），會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多因素驗證記錄中     |
| 單向 SMS                          | 在多因素驗證記錄中     |
| 語音通話                           | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙 |
| Microsoft Authenticator 通知 | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙<br />Microsoft Authenticator 裝置權杖變更時變更要求 |

> [!NOTE]
> 無論處理驗證要求的區域為何，多重要素驗證活動報告資料存放區都會儲存在美國供所有雲端之用。 Microsoft Azure 德國，由世紀營運的 Microsoft Azure，而 Microsoft 政府雲端有各自獨立的資料存放區，與公用雲端區域資料存放區分開，不過這項資料一律儲存在美國。

針對 Microsoft Azure Government、Microsoft Azure 德國、由世紀、Azure B2C 驗證、NPS 擴充功能、Windows Server 2016 或 2019 AD FS 介面卡操作的 Microsoft Azure，會儲存下列個人資料：

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 單向 SMS                          | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 語音通話                           | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙 |
| Microsoft Authenticator 通知 | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙<br />Microsoft Authenticator 裝置權杖變更時變更要求 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

如果您部署並執行 Azure 多因素驗證服務器，將會儲存下列個人資料：

> [!IMPORTANT]
> 自2019年7月1日起，Microsoft 將不再為新的部署提供多重要素驗證服務器。 新客戶若想要從他們的使用者要求多重要素驗證，應該使用雲端式 Azure 多重要素驗證。 在7月1日前啟用多重要素驗證服務器的現有客戶，將能夠下載最新版本、未來的更新，並如往常般產生啟用認證。

| 事件類型                           | 資料存放區類型 |
|--------------------------------------|-----------------|
| OATH 權杖                           | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 單向 SMS                          | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區 |
| 語音通話                           | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙 |
| Microsoft Authenticator 通知 | 在多因素驗證記錄中<br />多重要素驗證活動報表資料存放區<br />已封鎖的使用者是否已回報詐騙<br />Microsoft Authenticator 裝置權杖變更時變更要求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure 多因素驗證所儲存的組織資料

組織資料是租使用者層級的資訊，可能會公開設定或環境設定。 來自下列 Azure 入口網站多重要素驗證頁面的租使用者設定可能會儲存組織資料，例如鎖定閾值或傳入電話驗證要求的呼叫者識別碼資訊：

* 帳戶鎖定
* 詐騙警示
* 通知
* 通話設定

而對於 Azure 多因素驗證服務器，下列 Azure 入口網站頁面可能包含組織資料：

* 伺服器設定
* 一次性略過
* 快取規則
* 多重要素驗證服務器狀態

## <a name="log-data-location"></a>記錄資料位置

記錄資訊的儲存位置取決於要處理的區域。 大部分的地理位置都具有原生的 Azure 多因素驗證功能，因此記錄資料會儲存在處理多因素驗證要求的相同區域中。 在沒有原生 Azure 多因素驗證支援的地理位置中，它們是由美國或歐洲地理位置提供服務，而記錄資料則儲存在處理多因素驗證要求的相同區域中。

某些核心驗證記錄資料只會儲存在美國。 由世紀營運的 Microsoft Azure 德國和 Microsoft Azure 一律會儲存在其各自的雲端中。 Microsoft 政府雲端記錄資料一律儲存在美國。

## <a name="next-steps"></a>後續步驟

如需雲端式 Azure 多因素驗證和 Azure 多因素驗證服務器所收集的使用者資訊的詳細資訊，請參閱[Azure 多重要素驗證使用者資料收集](howto-mfa-reporting-datacollection.md)。
