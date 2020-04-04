---
title: 免費試用語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用語音服務既簡單又經濟實惠。 有兩個選項是免費的,因此您可以發現服務可以做什麼,並決定它是否適合您的需求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 4604bfe7c815f79733f99a1a3727e4c68527e7ec
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656135"
---
# <a name="try-the-speech-service-for-free"></a>免費試用語音服務'

在本文中,您可以選擇一個選項,以便輕鬆免費測試語音服務,以便瞭解該服務可以做什麼,並決定它是否適合您的需要。 根據您的情況與用例選擇以下兩個選項之一:

- [選項 1:](#no-card)立即取得**免費試用**API 金鑰,而無需提供任何信用卡資訊(您需要具有現有的 Azure 帳戶)。 **免費試用**將持續 30 天,數據在末尾被刪除。 此選項最適合快速試驗服務。
- [選項 2:](#new-resource)使用**免費訂閱**(需要信用卡資訊)在 Azure 中免費創建新的語音資源。 **免費訂閱**主要比付費訂閱具有更嚴格的費率限制。 如果要測試服務,但計劃將來升級到付費訂閱,並且不想丟失數據,則此選項是最佳選擇。

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>嘗試沒有信用卡資訊的語音服務

完成以下步驟以啟動 30 天的免費試用版並獲得 API 密鑰。 完成以下步驟后,試用期立即開始。

1. 跳到[試著認知服務](https://azure.microsoft.com/try/cognitive-services/)。
1. 選取 [語音識別 API]**** 索引標籤。
1. 選擇**API 金鑰**。

您將看到帳單選項。 選擇免費選項,然後讀取和批准使用者協定。 您將獲得可用於免費試用語音服務的密鑰 30 天。

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>透過建立 Azure 資源嘗試語音服務

對於以下步驟,您需要 Microsoft 帳戶和 Azure 帳戶。 如果您沒有 Microsoft 帳戶,則可以在[Microsoft 帳戶門戶](https://account.microsoft.com/account)免費註冊一個帳戶。 選擇 **"使用 Microsoft 登錄**",然後,當被要求登錄時,選擇"**創建 Microsoft 帳戶**"。 依照步驟來建立及驗證新的 Microsoft 帳戶。

擁有 Microsoft 帳戶後,轉到[Azure 註冊頁](https://azure.microsoft.com/free/ai/),選擇 **「免費開始」,** 並使用 Microsoft 帳戶創建新的 Azure 帳戶。

> [!NOTE]
> 語音服務有兩個服務層:免費和訂閱,它們具有不同的限制和好處。 當您註冊免費 Azure 帳戶時,它附帶 200 美元的服務積分,您可以申請付費語音服務訂閱,有效期最長為 30 天。
>
> 如果您使用免費、低容量的語音服務層,即使在免費試用或服務信用額度到期后,您也可以保留此免費訂閱。
>
> 有關詳細資訊,請參閱[認知服務定價 - 語音服務](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="create-the-resource"></a>建立資源

若要將語音服務資源 (免費或付費層) 新增至您的 Azure 帳戶：

1. 使用您的 Microsoft 帳戶，登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取入口網站左上方的 [建立資源]****。 如果未看到 **「創建資源」** 則始終可以通過選擇左上角的摺疊選單來找到它:

   ![折疊導覽按鈕](media/index/collapsed-nav.png)

1. 在 **"新建"** 視窗中,在搜尋框中鍵入"語音",然後按 ENTER。

1. 在搜尋結果中，選取 [Speech]****。

   ![語音搜尋結果](media/index/speech-search.png)

1. 選擇 **'建立**',然後:

   - 為新資源指定唯一名稱。 該名稱可説明您區分與同一服務關聯的多個訂閱。
   - 選擇與新資源相關聯的 Azure 訂用帳戶來決定費用的計費方式。
   - 選擇使用資源[的區域](regions.md)。
   - 選擇免費 (F0) 或付費 (S0) 定價層。 有關每個層的定價和使用配額的完整資訊,請選擇 **「查看完整的定價詳細資訊**」。。
   - 為此語音訂用帳戶建立新的資源群組，或將該訂用帳戶指派給現有的資源群組。 資源群組可協助組織各種 Azure 訂用帳戶。
   - 選取 [建立]  。 這將帶您到部署概述並顯示部署進度消息。

> [!NOTE]
> 您可以在一或多個區域中建立無限數量的標準層訂用帳戶。 不過，您可以建立只有一個免費層的訂用帳戶。 免費層上未使用 7 天的模型部署將自動停用。

部署新的語音資源需要一些時間。 部署完成後,選擇 **「轉到資源」** 在左側導航窗格中選擇 **「鍵**」以顯示語音服務訂閱金鑰。 每個訂用帳戶都有兩個金鑰，您可以在應用程式中使用任一個金鑰。 要快速將密鑰複製到代碼編輯器或其他位置,請選擇每個鍵旁邊的複製按鈕,切換視窗將剪貼簿內容粘貼到所需位置。

> [!IMPORTANT]
> 這些訂閱金鑰用於存取您的認知服務API。 不要共用您的金鑰。 安全儲存它們* 例如,使用 Azure 密鑰保管庫。 我們還建議定期重新生成這些密鑰。 進行 API 調用只需要一個密鑰。 重新生成第一個鍵時,可以使用第二個鍵繼續訪問服務。

## <a name="switch-to-a-new-subscription"></a>切換至新的訂用帳戶

若要從一個訂用帳戶切換至另一個，例如免費試用版到期時或發佈您的應用程式時，請將程式碼中的區域和訂用帳戶金鑰取代為新 Azure 資源的區域和訂用帳戶金鑰。

## <a name="about-regions"></a>關於地區

- 如果您的應用程式使用[語音 SDK](speech-sdk.md)，您會在建立語音設定時提供區域程式碼，例如 `westus`。
- 如果您的應用程式使用其中一個語音服務 [REST API](rest-apis.md)，則區域會是您提出要求時所用端點 URI 的一部份。
- 為某區域建立的金鑰，就只能在該區域中使用。 若嘗試在其他區域使用，將會導致驗證錯誤。

## <a name="next-steps"></a>後續步驟

完成個我們的任何一個 10 分鐘快速入門，或查看我們的 SDK 範例：

> [!div class="nextstepaction"]
> [快速入門:在 C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [語音 SDK 範例中辨識語音](speech-sdk.md#sample-source-code)
