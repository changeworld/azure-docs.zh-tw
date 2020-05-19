---
title: 認知服務控制流程
titleSuffix: Azure Cognitive Services
description: 瞭解如何申請以及早存取新的認知服務容器和 Api。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599595"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Azure 認知服務的管制流程

> [!NOTE]
> 服務供應專案完成閘道預覽之後，就會進入「ungated」公開預覽，而不需要應用程式來進行存取。 在預覽程式之後，它會發行為正式推出（GA）。

隨著新的 Azure 認知服務供應專案的推出，他們會通過閘道預覽，客戶必須透過應用程式要求存取權。 此管制程式有助於找出服務供應專案在廣泛推出之前的改進機會。 

本文將引導您完成閘道認知服務供應專案的應用程式流程。

## <a name="eligibility-and-approval-process"></a>資格與核准流程

管制程式已就緒，可協助衡量感興趣，並進一步瞭解客戶的需求。 Microsoft 小組接受來自 Microsoft 商業客戶的[應用程式](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)，並具備有效的 Azure 訂用帳戶和有效的商務案例。 客戶可能會在下列情況拒絕其應用程式：

 - 它們不會與任何組織相關聯
 - 他們沒有有效的 Azure 訂用帳戶
 - 應用程式已透過個人電子郵件提交（ @hotmail.com 、 @gmail.com 、 @yahoo.com ）
 - 未提供適當的理由或商務案例

根據不同客戶區段的需求，我們正嘗試加速核准程式。 不過，我們無法認可至時間軸。 做出決定之後，Microsoft 團隊會與您和您的帳戶管理小組聯繫，以進行後續步驟。 我們非常感謝您的瞭解和耐心。

如果應用程式已核准，Microsoft 團隊會傳送電子郵件，其中包含詳細資料、檔和指引。 [這裡](https://azure.microsoft.com/pricing/details/cognitive-services/)提供認知服務定價詳細資料。


目前，下列服務會透過管制程式來提供：

## <a name="gated-web-apis"></a>閘道 web Api

|服務  |
|---------|
|異常偵測器 v2     | 

## <a name="gated-online-containers"></a>閘道線上容器

| 服務                             | 個容器                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [電腦視覺][cv-containers]    | 讀取                                                                          |
| [臉部][fa-containers]               | 臉部                                                                          |
| [表單辨識器][fr-containers]    | 表單辨識器                                                               |
| [語音服務 API][sp-containers] | 語音轉換文字（自訂和標準）和文字轉換語音（自訂和標準） |
| [翻譯工具文字][tt-containers]    | 翻譯工具文字                                                               |

> [!IMPORTANT]
> 如果服務或容器化供應專案未列出，則不是閘道或無法使用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [註冊閘道服務](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
