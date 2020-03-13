---
title: 免費試用語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用「語音服務」既容易又實惠。 有兩個免費選項可供您使用，因此您可以探索服務可執行檔動作，並決定其是否適合您的需求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221269"
---
# <a name="try-the-speech-service-for-free"></a>免費試用語音服務

在本文中，您會選擇免費輕鬆地測試語音服務的選項，讓您可以探索服務可執行檔動作，並決定是否適合您的需求。 根據您的情況和使用案例，選擇下列兩個選項的其中一個：

- [選項 1](#no-card)：立即取得**免費試用**API 金鑰，而不提供任何信用卡資訊（您必須擁有現有的 Azure 帳戶）。 **免費試用**會持續30天，且資料會在結束時刪除。 此選項最適合用來進行服務的快速實驗。
- [選項 2](#new-resource)：使用**免費訂**用帳戶在 Azure 中建立新的語音資源（需要信用卡資訊）。 **免費的訂**用帳戶主要具有比付費訂用帳戶更嚴格的速率限制。 如果您想要測試服務，但也計畫在未來升級至付費訂用帳戶，而不想遺失資料，最好使用此選項。

## <a id="no-card"></a>試用語音服務，而不需要信用卡資訊

請完成下列步驟，以啟用30天免費試用版並取得 API 金鑰。 當下列步驟完成後，您的試用期就會立即開始。

1. 前往[試用認知服務](https://azure.microsoft.com/try/cognitive-services/)。
1. 選取 [語音識別 API] 索引標籤。
1. 選擇 [**取得 API 金鑰**]。

您會看到帳單選擇。 選擇 [免費] 選項，然後閱讀並核准使用者合約。 您會看到可用來免費試用語音服務30天的金鑰。

## <a id="new-resource"></a>藉由建立 Azure 資源來試用語音服務

在下列步驟中，您需要 Microsoft 帳戶和 Azure 帳戶。 如果您沒有 Microsoft 帳戶，您可以在[Microsoft 帳戶入口網站](https://account.microsoft.com/account)上免費註冊一個。 選取 [**使用 Microsoft 帳戶登入**]，然後在系統要求您登入時，選取 [**建立 Microsoft 帳戶**]。 依照步驟來建立及驗證新的 Microsoft 帳戶。

一旦有了 Microsoft 帳戶，請移至[Azure 註冊頁面](https://azure.microsoft.com/free/ai/)，選取 [**開始免費**]，然後使用 Microsoft 帳戶建立新的 Azure 帳戶。

> [!NOTE]
> 語音服務有兩個服務層級：免費和訂用帳戶，其限制和權益各有不同。 當您註冊免費的 Azure 帳戶時，會隨附 $200 的服務信用額度，您可以將其套用到付費語音服務訂用帳戶，最多30天。
>
> 如果您使用免費的低容量語音服務層級，即使在免費試用或服務信用額度到期後，您仍可保留此免費訂用帳戶。
>
> 如需詳細資訊，請參閱[認知服務定價-語音服務](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="create-the-resource"></a>建立資源

若要將語音服務資源 (免費或付費層) 新增至您的 Azure 帳戶：

1. 使用您的 Microsoft 帳戶，登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取入口網站左上方的 [建立資源]。 如果您看不到 [**建立資源**]，您隨時都可以藉由選取左上方的折迭功能表來找到它：

   ![折迭的瀏覽按鈕](media/index/collapsed-nav.png)

1. 在**新**視窗中，于搜尋方塊中輸入 "speech"，然後按 enter。

1. 在搜尋結果中，選取 [Speech]。

   ![語音搜尋結果](media/index/speech-search.png)

1. 選取 [**建立**]，然後：

   - 為您的新資源提供唯一的名稱。 此名稱可協助您區分多個與相同服務系結的訂閱。
   - 選擇與新資源相關聯的 Azure 訂用帳戶來決定費用的計費方式。
   - 選擇將使用資源的[區域](regions.md)。
   - 請選擇免費（F0）或付費（S0）定價層。 如需每一層的定價和使用量配額完整資訊，請選取 [**查看完整定價詳細資料**]。
   - 為此語音訂用帳戶建立新的資源群組，或將該訂用帳戶指派給現有的資源群組。 資源群組可協助組織各種 Azure 訂用帳戶。
   - 選取 [建立]。 這會帶您前往部署總覽並顯示部署進度訊息。

> [!NOTE]
> 您可以在一或多個區域中建立無限數量的標準層訂用帳戶。 不過，您可以建立只有一個免費層的訂用帳戶。 免費層上保留7天未使用的模型部署將會自動解除委任。

部署新的語音資源需要幾分鐘的時間。 部署完成後，選取 [**移至資源**]，然後在左側流覽窗格中選取 [**金鑰**]，以顯示您的語音服務訂用帳戶金鑰。 每個訂用帳戶都有兩個金鑰，您可以在應用程式中使用任一個金鑰。 若要快速地將金鑰複製/貼到您的程式碼編輯器或其他位置，請選取每個索引鍵旁的 [複製] 按鈕，切換 windows 將剪貼簿內容貼到所需的位置。

> [!IMPORTANT]
> 這些訂用帳戶金鑰可用來存取您的認知服務 API。 請勿共用您的金鑰。 安全地加以儲存–例如，使用 Azure Key Vault。 我們也建議您定期重新產生這些金鑰。 進行 API 呼叫時，只需要一個金鑰。 重新產生第一個金鑰時，您可以使用第二個金鑰繼續存取服務。

## <a name="switch-to-a-new-subscription"></a>切換至新的訂用帳戶

若要從一個訂用帳戶切換至另一個，例如免費試用版到期時或發佈您的應用程式時，請將程式碼中的區域和訂用帳戶金鑰取代為新 Azure 資源的區域和訂用帳戶金鑰。

## <a name="about-regions"></a>關於區域

- 如果您的應用程式使用[語音 SDK](speech-sdk.md)，您會在建立語音設定時提供區域程式碼，例如 `westus`。
- 如果您的應用程式使用其中一個語音服務 [REST API](rest-apis.md)，則區域會是您提出要求時所用端點 URI 的一部份。
- 為某區域建立的金鑰，就只能在該區域中使用。 若嘗試在其他區域使用，將會導致驗證錯誤。

## <a name="next-steps"></a>後續步驟

完成個我們的任何一個 10 分鐘快速入門，或查看我們的 SDK 範例：

> [!div class="nextstepaction"]
> [快速入門：以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [語音 SDK 範例](speech-sdk.md#get-the-samples)
