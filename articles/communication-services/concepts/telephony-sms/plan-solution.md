---
title: 規劃您的 Azure 通訊服務電話語音和 SMS 解決方案
titleSuffix: An Azure Communication Services concept document
description: 了解如何有效規劃使用電話號碼和電話語音。
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943683"
---
# <a name="plan-your-telephony-and-sms-solution"></a>規劃您的電話語音和 SMS 解決方案

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

本文件說明 Azure 通訊服務必須提供的各種電話語音方案和號碼類型。 我們將逐步解說決策流程，協助您選取語音服務提供者、電話號碼類型、方案，以及透過通訊服務提供的功能。

## <a name="about-phone-numbers-in-azure-communications-services"></a>關於 Azure 通訊服務中的電話號碼

Azure 通訊服務可讓您使用電話號碼來撥打及接收電話語音通話和 SMS 訊息。 這些電話號碼可用來在您服務所撥打的外撥通話上設定呼叫者識別碼。
  
如果您沒有要匯入通訊服務解決方案的現有電話號碼，最簡單的開始方式，就是在幾分鐘內從 Azure 通訊服務取得新的電話號碼。

如果您有想要在解決方案中繼續使用的現有電話號碼 (例如，1-800–COMPANY)，您可以將來自現有提供者的電話號碼移植到通訊服務。

下圖可協助您瀏覽可用的選項：

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="此圖顯示如何針對您的電話號碼制定決策。":::

現在，讓我們來回顧透過通訊服務提供的電話號碼類型和功能。 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>電話號碼和功能的 Microsoft 直接供應項目

Azure 通訊服務為開發人員提供絕佳的彈性。 在大部分的電話號碼上，我們都可讓您設定一組「單點」的方案。 有些開發人員只需要輸入通話方案；有些可能會選擇進線通話和外撥 SMS 方案。 當您在通訊服務內租用及/或移植電話號碼時，可以選取這些方案。

可用的方案取決於您在其中操作的國家/地區和電話號碼類型。下圖代表決策流程：  可用的方案取決於您在其中操作的國家/地區和電話號碼類型。

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

在您選取電話號碼類型之前，讓我們先複習國際電話編號計劃。

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>選擇性閱讀。 國際公用電信編號計劃 (E.164)

> [!NOTE]
> 即使您已熟悉 E.164 電話編號計劃，也建議您查看這項資訊，以進一步了解 Azure 通訊服務直接供應項目所提供的類型和功能。

國際公用電信編號計劃是在國際電信聯盟 (ITU) 建議 E.164 中定義。 符合的數位限制為最多 15 個數字。

電話號碼包含

-   前置詞 “+”
-   國際撥號首碼或國家/地區代碼 (一、二或三個數字) 
-   (選擇性) 國家 (地區) 目的地代碼或編號計劃，通常稱為「區碼」。 此代碼的長度取決於國家/地區。 在美國，這是三個數字。 在澳洲和紐西蘭，這是一個數字。 德國、日本、墨西哥和某些其他國家/地區代碼的長度可能不同。 例如，在德國，區碼可為二到五個數字，而在日本，可為一到五個數字。
-   訂閱者號碼

> [!NOTE]
> 上述分類並不完全符合 ITU E.164 標準，並旨在提供簡化的描述。 例如，訂閱者號碼會在標準中細分。 如果您想要更深入了解國際編號計劃，[ITU E. 164 標準](https://www.itu.int/rec/T-REC-E.164)是很好的起點。  

以下一些範例可協助您更了解編號計劃：

美國區域電話號碼：

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="美國區域電話號碼的範例":::

加拿大區域電話號碼：

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="加拿大區域電話號碼的範例":::

北美洲區域免付費電話號碼：

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="北美洲免費電話號碼的範例":::

英國的行動電話號碼：

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="英國行動電話號碼的範例":::

接下來，讓我們來回顧 Azure 通訊服務中可用的特定電話號碼類型。

## <a name="phone-number-types-in-azure-communication-services"></a>Azure 通訊服務中的電話號碼類型

Microsoft 在適用的國家/地區中提供簡短代碼和行動電話號碼的區域和免付費方案。

下表摘要說明這些電話號碼類型： 

| 電話號碼類型 | 範例                              | 國家/地區可用性    | 常見使用案例                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 地區          | +1 (地理區碼) XXX XX XX  | 美國、加拿大、波多黎各 | 將電話號碼指派給應用程式中的使用者，或指派給互動式語音回應 (IVR) 系統/Bot |
| 免付費電話         | +1 (免付費區域「代碼」) XXX XX XX | 美國、加拿大、波多黎各 | 指派給互動式語音回應 (IVR) 系統/Bot、SMS 應用程式                                        |

## <a name="plans"></a>規劃 

讓我們看看您可以為您的電話號碼啟用的功能。 由於法規需求，這些功能會因國家/地區而異。 Azure 通訊服務提供下列功能：

- **單向外撥 SMS**，適用於通知和雙因素驗證案例。
- **雙向進線和外撥 SMS**，這是一項預先定義的套件，可讓 SMS 做為方案一部分進行傳送和接收。
- **PSTN 通話**，您可以選取進線通話，並使用呼叫者識別碼來撥打外撥通話。

## <a name="next-steps"></a>下一步

### <a name="quickstarts"></a>快速入門

- [取得手機號碼](../../quickstarts/telephony-sms/get-phone-number.md)
- [撥打電話](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [傳送 SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>概念文件

- [語音和視訊概念](../voice-video-calling/about-call-types.md)
- [呼叫流程和 SMS 流程](../call-flows.md)
- [定價](../pricing.md)
