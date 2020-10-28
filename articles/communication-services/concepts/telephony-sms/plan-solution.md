---
title: 規劃您的 Azure 通訊服務電話語音和 SMS 解決方案
titleSuffix: An Azure Communication Services concept document
description: 了解如何有效規劃使用電話號碼和電話語音。
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 0420360b70485e49dc6cd06dbeb19400c0f73ef5
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070345"
---
# <a name="plan-your-telephony-and-sms-solution"></a>規劃您的電話語音和 SMS 解決方案

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Azure 通訊服務可讓您使用電話號碼撥打語音電話，並透過公用交換電話網路 (PSTN) 傳送 SMS 訊息。 在本文件中，我們將檢閱使用通訊服務規劃電話語音和 SMS 解決方案時所適用的電話號碼類型、設定選項和區域可用性。

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]


## <a name="phone-number-types-in-azure-communication-services"></a>Azure 通訊服務中的電話號碼類型
 
通訊服務提供兩種類型的電話號碼： **當地** 和 **免付費** 。 

### <a name="local-numbers"></a>當地電話號碼
當地 (地理) 電話號碼是由美國當地區碼組成的 10 位數電話號碼。 例如，`+1 (206) XXX-XXXX` 是區碼為 `206` 的當地電話號碼。 此區碼指派給西雅圖的城市。 這些電話號碼通常由個人和當地企業使用。 Azure 通訊服務提供美國地區的當地電話號碼。 這些號碼可以用來撥打電話，但不能用來傳送 SMS 訊息。 

### <a name="toll-free-numbers"></a>免付費電話號碼
免付費電話號碼是具有不同區碼的 10 位數電話號碼，您可以免費從任何電話號碼進行通話。 例如，`+1 (800) XXX-XXXX` 是北美洲區域的免付費電話號碼。 這些電話號碼通常用於服務客戶。 Azure 通訊服務提供美國地區的免付費電話號碼。 這些號碼可以用來撥打電話，以及傳送 SMS 訊息。 免付費電話號碼無法供人員使用，而且只能指派給應用程式。

#### <a name="choosing-a-phone-number-type"></a>選擇電話號碼類型

如果應用程式將使用您的電話號碼 (例如，代表您的服務進行通話或傳送訊息)，則您可以選取免付費或當地 (地理) 電話號碼。 如果您的應用程式將傳送 SMS 訊息及/或進行通話，則您可以選取免付費電話號碼。

如果您的電話號碼是由人員使用 (例如，通話應用程式的使用者)，則必須使用當地 (地理) 電話號碼。 

下表摘要說明這些電話號碼類型： 

| 電話號碼類型 | 範例                              | 國家/地區可用性    | 電話號碼功能 |常見使用案例                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| 當地 (地理)        | +1 (當地區碼) XXX XX XX  | 美國                      | 通話 (撥出) | 將電話號碼指派給應用程式中的使用者  |
| 免付費電話         | +1 (免付費區域「代碼」) XXX XX XX | 美國                      | 通話 (撥出)、SMS (撥入/撥出)| 將電話號碼指派給互動式語音回應 (IVR) 系統/Bot、SMS 應用程式                                        |


## <a name="phone-number-features-in-azure-communication-services"></a>Azure 通訊服務中的電話號碼功能 

針對大部分的電話號碼，我們都可讓您設定一組「單點」的功能。 當您在 Azure 通訊服務內租用電話號碼時，可以選取這些功能。

可供您使用的功能取決於您營運所在的國家/地區、您的使用案例，以及您所選取的電話號碼類型。 由於法規需求，這些功能會因國家/地區而異。 Azure 通訊服務提供下列電話號碼功能：

- **單向撥出 SMS** 此選項可讓您將 SMS 訊息傳送給您的使用者。 這在通知和雙因素驗證案例中很有用。 
- **雙向撥入和撥出 SMS** 此選項可讓您使用電話號碼將訊息傳送給使用者，以及從使用者接收訊息。 此選項在客戶服務案例中很有用。
- **單向撥出電話通話** 此選項可讓您對使用者進行通話，並為您服務所進行的撥出通話設定來電者識別碼。 此選項在客戶服務和語音通知案例中很有用。

## <a name="countryregion-availability"></a>國家/地區可用性

下表顯示您可以在何處取得不同類型的電話號碼，以及與這些電話號碼類型相關聯的撥入及撥出通話和簡訊功能。

|號碼類型| 取得號碼 | 進行通話                                        | 接收通話                                    |傳送訊息       | 接收訊息 |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| 當地 (地理)  | 美國                 | 美國、加拿大、英國、德國、法國、 +其他*| 美國、加拿大、英國、德國、法國、 +其他* |無法使用| 無法使用 |
| 免付費電話 | 美國                 | 美國                                                   | 美國                                                    |美國                | 美國 |

\* 如需通話目的地和定價的詳細資訊，請參閱[定價頁面](../pricing.md)。

## <a name="azure-subscriptions-eligibility"></a>Azure 訂用帳戶資格

若要取得電話號碼，您必須是付費的 Azure 訂用帳戶。 試用帳戶或 Azure 免費點數無法取得電話號碼。 

目前，電話號碼的可用性僅限於在美國有帳單位址的 Azure 訂用帳戶。

## <a name="next-steps"></a>下一步

### <a name="quickstarts"></a>快速入門

- [取得手機號碼](../../quickstarts/telephony-sms/get-phone-number.md)
- [撥打電話](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [傳送 SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>概念文件

- [語音和視訊概念](../voice-video-calling/about-call-types.md)
- [通話流程](../call-flows.md)
- [定價](../pricing.md)
