---
title: 在 Azure Active Directory B2C 中啟用年齡管制 | Microsoft Docs
description: 了解如何使用應用程式識別未成年人。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386126"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用年齡管制

>[!IMPORTANT]
>這項功能處於公開預覽狀態。 請勿將功能用於生產應用程式。
>

Azure Active Directory B2C （Azure AD B2C）中的年齡管制可讓您識別想要使用應用程式的未成年人。 您可以選擇封鎖未成年人，使其無法登入應用程式。 使用者也可以返回應用程式，並確定其年齡群組和家長同意狀態。 Azure AD B2C 可以在不需要家長同意的情況下封鎖未成年人。 Azure AD B2C 也可以設定為允許應用程式決定如何處理未成年人。

在您的[使用者流程](user-flow-overview.md)中啟用年齡管制之後，系統會詢問使用者是否已出生，以及其居住所在的國家/地區。 如果使用者登入先前未輸入資訊，則他們需要在下次登入時輸入該資訊。 每次使用者登入時都會套用這些規則。

Azure AD B2C 會使用使用者輸入的資訊，來確定他們是否為未成年人。 然後，會更新其帳戶中的 **ageGroup** 欄位。 值可為 `null`、`Undefined`、`Minor`、`Adult` 和 `NotAdult`。  然後，**ageGroup** 和 **consentProvidedForMinor** 欄位用於計算 **legalAgeGroupClassification** 的值。

年齡管制牽涉到兩個年齡值：某人不再被視為未成年人的年齡，以及未成年人必須取得家長同意的年齡。 下表列出可用於定義未成年人和未成年人需要同意的年齡規則。

| 國家/地區 | 國家/地區名稱 | 未成年人同意年齡 | 未成年人年齡 |
| -------------- | ------------------- | ----------------- | --------- |
| 預設 | None | None | 18 |
| AE | 阿拉伯聯合大公國 | None | 21 |
| AT | 奧地利 | 14 | 18 |
| BE | 比利時 | 14 | 18 |
| BG | 保加利亞 | 16 | 18 |
| BH | 巴林 | None | 21 |
| CM | 喀麥隆 | None | 21 |
| CY | 賽浦路斯 | 16 | 18 |
| CZ | 捷克共和國 | 16 | 18 |
| DE | 德國 | 16 | 18 |
| DK | 丹麥 | 16 | 18 |
| EE | 愛沙尼亞 | 16 | 18 |
| EG | 埃及 | None | 21 |
| ES | 西班牙 | 13 | 18 |
| FR | 法國 | 16 | 18 |
| GB | United Kingdom | 13 | 18 |
| GR | 希臘 | 16 | 18 |
| HR | 克羅埃西亞 | 16 | 18 |
| HU | 匈牙利 | 16 | 18 |
| IE | 愛爾蘭 | 13 | 18 |
| IT | 義大利 | 16 | 18 |
| KR | 大韓民國 | 14 | 18 |
| LT | 立陶宛 | 16 | 18 |
| LU | 盧森堡 | 16 | 18 |
| LV | 拉脫維亞 | 16 | 18 |
| MT | 馬爾他 | 16 | 18 |
| NA | 納米比亞 | None | 21 |
| NL | 荷蘭 | 16 | 18 |
| PL | 波蘭 | 13 | 18 |
| PT | 葡萄牙 | 16 | 18 |
| RO | 羅馬尼亞 | 16 | 18 |
| SE | 瑞典 | 13 | 18 |
| SG | 新加坡 | None | 21 |
| SI | 斯洛維尼亞 | 16 | 18 |
| SK | 斯洛伐克 | 16 | 18 |
| TD | 查德 | None | 21 |
| 二四 | 泰國 | None | 20 |
| TW | 台灣 | None | 20 |
| US | 美國 | 13 | 18 |

## <a name="age-gating-options"></a>年齡管制選項

### <a name="allowing-minors-without-parental-consent"></a>允許未取得家長同意的未成年人

對於允許註冊和/或登入的使用者流量，您可以選擇允許未經同意的未成年人進入您的應用程式。 未取得家長同意的未成年人可以正常登入或註冊，Azure AD B2C 會以 **legalAgeGroupClassification** 宣告發出識別碼權杖。 此聲明定義了使用者擁有的體驗，例如收集家長同意及更新 **consentProvidedForMinor** 欄位。

### <a name="blocking-minors-without-parental-consent"></a>封鎖未取得家長同意的未成年人

對於允許註冊和/或登入的使用者流量，您可以選擇封鎖未經應用程式同意的未成年人。 下列選項可用於處理 Azure AD B2C 中被封鎖的使用者：

- 將 JSON 傳回至應用程式 - 此選項會將回應傳回至封鎖未成年人的應用程式。
- 顯示錯誤頁面 - 使用者會看見一個頁面，指出他們無法存取應用程式。

## <a name="set-up-your-tenant-for-age-gating"></a>設定您的租用戶以進行年齡管制

若要在使用者流程中使用年齡管制，您必須設定租用戶，使其具有其他屬性。

1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。 選取包含您租用戶的目錄。
2. 選取 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
3. 在左側功能表中為您的租用戶選取 [屬性]****。
2. 在 [年齡管制]**** 區段下，按一下 [設定]****。
3. 等候作業完成，您的租用戶將會進行年齡管制的設定。

## <a name="enable-age-gating-in-your-user-flow"></a>在使用者流程中啟用年齡管制

在您的租用戶設定為使用年齡管制後，您即可在啟用此功能的[使用者流量](user-flow-versions.md)中使用此功能。 您可以透過下列步驟啟用年齡管制：

1. 建立已啟用年齡管制的使用者流程。
2. 建立使用者流程之後，請選取功能表中的 [屬性]****。
3. 在 [年齡管制]**** 區段中，選取 [啟用]****。
4. 然後，您可以決定要如何管理識別為未成年人的使用者。 針對 [註冊或登入]****，選取 `Allow minors to access your application` 或 `Block minors from accessing your application`。 如果選取 [封鎖未成年人]，則選擇 `Send a JSON back to the application` 或 `Show an error message`。




