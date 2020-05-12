---
title: 適用於中國 Android 的 Microsoft Authenticator 可用性和限制 | Microsoft Docs
description: 了解如何取得 Microsoft Authenticator 應用程式 - 在中國的可用性
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/06/2020
ms.author: curtand
ms.openlocfilehash: 3ba1b41859a543ab5e0393c5715dc49d452ea73c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892234"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>適用於中國公用雲端中 Android 的 Microsoft Authenticator

適用於 Android 的 Microsoft Authenticator 應用程式可以在中國下載。 Google Play 商店在中國無法使用，因此應用程式必須從其他中國應用程式市集下載。 適用於 Android 的 Microsoft Authenticator 應用程式目前可以在中國的下列市集取得：

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

應用程式的最新組建在 Google Play 商店中，但是我們會儘快在所有其他應用程式市集上更新應用程式。 因為沒有任何自訂 Android 應用程式套件 (APK) 部署到任何應用程式市集，所以可以從下列其中一個位置順暢地更新應用程式：

- 下載來源的市集
- 使用者跨越區域時的 Google Play 商店

## <a name="limitations"></a>限制

適用於 Android 的 Microsoft Authenticator 應用程式會使用 Google 的 Firebase 雲端通訊系統和 Google Play Services 來接收推播通知。 因為這些服務在中國都無法使用，所以應用程式的功能有一些限制：

- 使用推播通知，將 Authenticator 應用程式註冊為多重要素驗證 (MFA) 方法並不適用。

- 無法設定[手機登入](../authentication/howto-authentication-sms-signin.md)。 使用者必須使用推播通知將應用程式設定為 MFA 方法，目前無法使用。

如果使用者先前已使用應用程式來設定手機登入或多重要素驗證，他們可以手動檢查應用程式中的通知要求，並將其用於身分識別驗證。

## <a name="multi-factor-authentication-workaround"></a>多重要素驗證因應措施

使用者不需要使用推播通知來進行多重要素驗證，而是可以設定其 MFA 在其裝置上接收驗證碼，以用來驗證其身分識別。 這些驗證碼的有效時間為 30 秒，而且若要使用，系統管理員必須讓其租用戶使用限時單次密碼 (TOTP) 驗證碼來執行驗證。

## <a name="availability"></a>可用性

Microsoft Authenticator 功能 | 中國的可用性
------------------------------- | ---------------------
使用推播通知的 MFA 註冊 | 否
預先存在的 MFA 帳戶使用推播通知來驗證身分識別 | 否
預先存在的 MFA 帳戶執行手動檢查通知 | 是
僅使用 TOTP/驗證碼進行 MFA 註冊/驗證 | 是
手機登入註冊 | 否
使用推播通知的現有手機登入 | 否
藉由執行驗證要求的手動檢查來進行現有的手機登入驗證 | 是

## <a name="next-steps"></a>後續步驟

- [下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)
