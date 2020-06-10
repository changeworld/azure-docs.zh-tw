---
title: Azure 說話者辨識服務
titleSuffix: Azure Cognitive Services
description: Azure 認知服務說話者辨識依據其獨特的語音特性，提供驗證和識別喇叭的演算法。 說話者辨識是用來回答「誰在說話？」的問題。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: cc19844c6407a83233c70048e9eb258a742036fb
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635164"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>什麼是 Azure 說話者辨識服務？

說話者辨識服務提供以唯一語音特性驗證和識別喇叭的演算法。 說話者辨識是用來回答「誰在說話？」的問題。 您可以為單一說話者提供音訊訓練資料，這會根據說話者語音的唯一特性來建立註冊設定檔。 接著，您可以對照此設定檔交叉檢查音訊語音範例，以確認說話者為同一人（說話者驗證），或針對一*組*已註冊的說話者設定檔交叉檢查音訊語音範例，以查看其是否符合群組中的任何設定檔（說話者識別）。 相較之下，[說話者 Diarization](batch-transcription.md#speaker-separation-diarization)會在批次作業中，將音訊的區段分組為說話者。

## <a name="speaker-verification"></a>說話者驗證

說話者驗證使用複雜密碼或自由形式的語音輸入，簡化驗證已註冊說話者身分識別的程式。 它可以用來驗證個人在各種解決方案中的安全、順暢客戶參與，從撥接中心的客戶身分識別驗證，到不需要接觸的設備存取。

### <a name="how-does-speaker-verification-work"></a>說話者驗證如何運作？

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="說話者驗證流程圖。":::

說話者驗證可以是文字相依或與文字無關。 **文字相關**驗證表示說話者需要選擇相同的複雜密碼，才能在註冊和驗證階段中使用。 與**文字無關**的驗證表示說話者可以在註冊和驗證片語中以每天的語言來交談。

對於**文字相依**的驗證，說話者的聲音是藉由從一組預先定義的片語來表示複雜密碼來註冊。 語音功能會從音訊錄製中解壓縮以形成唯一的語音簽章，同時也會辨識選擇的複雜密碼。 語音簽名和複雜密碼會一起用來驗證說話者。 

與**文字無關**的驗證不會限制說話者在註冊期間或要驗證的音訊範例中所說的內容，因為它只會將語音功能解壓縮成分數相似之處。 

這些 Api 不是用來判斷音訊是來自 live person，或是已註冊說話者的仿造/錄製。 

## <a name="speaker-identification"></a>說話者識別

說話者識別是用來判斷一組已註冊說話者內的不明喇叭識別。 說話者識別可讓您將語音屬性設為個別的喇叭，並從有多個說話者的案例中解除鎖定值，例如：

* 遠端會議生產力的支援解決方案 
* 建立多使用者裝置個人化

### <a name="how-does-speaker-identification-work"></a>說話者辨識如何運作？

說話者識別的註冊與**文字無關**，這表示說話者在音訊中所說的不會有任何限制。 類似于說話者驗證，在註冊階段中，會記錄說話者的語音，並將語音功能解壓縮以形成唯一的語音簽章。 在識別階段，輸入語音範例會與指定的已註冊語音清單（每個要求中最多50個）進行比較。

## <a name="data-security-and-privacy"></a>資料安全性和隱私權

說話者註冊資料會儲存在安全的系統中，包括註冊的語音音訊和語音簽名功能。 只有在升級演算法時才會使用語音音訊進行註冊，而且需要重新解壓縮功能。 此服務不會保留語音錄製或在辨識階段期間傳送至服務的已解壓縮語音功能。 

您可以控制要保留資料的時間長度。 您可以透過 API 呼叫來建立、更新及刪除個別說話者的註冊資料。 刪除訂閱後，與該訂閱相關聯的所有說話者註冊資料也會一併刪除。 

如同所有的認知服務資源，使用說話者辨識服務的開發人員必須瞭解 Microsoft 對於客戶資料的原則。 您應該確定您已收到使用者的適當許可權，以進行說話者辨識。 如需詳細資訊，請參閱 Microsoft 信任中心上的 [認知服務頁面](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> * 完成說話者辨識[基本概念一文](speaker-recognition-basics.md)，以瞭解您可以在應用程式中使用的常見設計模式。
> * 請參閱[影片教學](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/)課程，以瞭解與文字無關的說話者驗證。
