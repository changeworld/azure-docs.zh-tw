---
title: 說話者辨識總覽-語音服務
titleSuffix: Azure Cognitive Services
description: 說話者辨識提供的演算法，會使用語音 biometry 來驗證和識別說話者的獨特語音特性。 說話者辨識是用來回答「誰在說話？」的問題。 本文概述說話者辨識服務的優點和功能。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 說話者辨識，語音測量
ms.openlocfilehash: d07a9960c8a586fa137b4b717afbf91740c265d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015319"
---
# <a name="what-is-speaker-recognition-preview"></a>什麼是說話者辨識 (預覽) ？

說話者辨識服務提供的演算法，會使用語音 biometry 來驗證和識別喇叭的唯一語音特性。 說話者辨識是用來回答「誰在說話？」的問題。 您可以為單一喇叭提供音訊訓練資料，以根據說話者語音的獨特特性建立註冊設定檔。 接著，您可以針對此設定檔交叉檢查音訊語音範例，以確認說話者是同一人 (說話者驗證) ，或針對一 *組* 已註冊的說話者設定檔進行交叉檢查音訊語音範例，以查看是否符合群組 (說話者識別) 中的任何設定檔。 相反地， [說話者 Diarization](batch-transcription.md#speaker-separation-diarization) 會在批次作業中依說話者分組音訊區段。

## <a name="speaker-verification"></a>說話者驗證

說話者驗證可利用複雜密碼或自由格式的語音輸入，簡化驗證已註冊說話者身分識別的程式。 您可以用它來驗證個人在各種不同的解決方案中的安全、順暢的客戶合作，從電話中心內的客戶身分識別驗證，到無需接觸的設施存取。

### <a name="how-does-speaker-verification-work"></a>說話者驗證如何運作？

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="說話者驗證流程圖。":::

說話者驗證可以是文字相依或與文字無關。 **文字相依** 驗證表示說話者需要選擇在註冊和驗證階段期間所使用的相同複雜密碼。 與 **文字無關** 的驗證表示說話者可以在註冊和驗證片語中以日常語言說話。

針對 **文字相依** 驗證，說話者的語音會透過從一組預先定義的片語中說出複雜密碼來註冊。 聲音功能會從音訊錄製中解壓縮以形成唯一的語音簽章，同時也能辨識所選的複雜密碼。 語音簽名和複雜密碼會一起用來驗證說話者。 

與 **文字無關** 的驗證對於說話者在註冊期間或要驗證的音訊範例中所說的內容沒有任何限制，因為它只會將聲音特徵解壓縮以計分相似性。 

這些 Api 不是用來判斷音訊是來自 live person 或註冊的說話者的仿造/錄影。 

## <a name="speaker-identification"></a>說話者識別

說話者識別是用來在一組已註冊的喇叭內判斷不明的喇叭身分識別。 說話者辨識可讓您將語音屬性設為個別說話者，並從具有多個喇叭的案例中解除鎖定值，例如：

* 遠端會議生產力的支援解決方案 
* 建立多使用者裝置個人化

### <a name="how-does-speaker-identification-work"></a>說話者識別如何運作？

說話者識別的註冊與 **文字無關**，這表示說話者在音訊中所說的內容沒有任何限制。 類似于說話者驗證，在註冊階段會記錄說話者的語音，並將語音特徵解壓縮以形成唯一的語音簽章。 在識別階段中，輸入語音範例會與指定的已註冊語音清單進行比較 (在每個要求) 中，最高為50。

## <a name="data-security-and-privacy"></a>資料安全性和隱私權

說話者註冊資料儲存在安全的系統中，包括用於註冊的語音音訊和語音簽名功能。 註冊的語音音訊只會在演算法升級時使用，而且需要再次解壓縮這些功能。 服務不會保留語音錄製或在辨識階段傳送給服務的解壓縮語音功能。 

您可以控制應保留資料的時間長度。 您可以透過 API 呼叫來建立、更新及刪除個別說話者的註冊資料。 刪除訂閱後，與該訂閱相關聯的所有說話者註冊資料也會一併刪除。 

如同所有認知服務資源，使用說話者辨識服務的開發人員必須留意 Microsoft 對於客戶資料的政策。 您應確定您已收到使用者的適當許可權，以進行說話者辨識。 如需詳細資訊，請參閱 Microsoft 信任中心的 [認知服務頁面](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   。 

## <a name="common-questions-and-solutions"></a>常見問題和解決方案

| 問題 | 解決方法 |
|---------|----------|
| 說話者辨識可用於哪些案例？ | 撥接中心客戶驗證、以語音為基礎的患者簽入、會議轉譯、多使用者裝置個人化|
| 識別與驗證之間有何差異？ | 識別是一種偵測來自喇叭群組的成員是說的過程。 驗證是確認說話者符合已知或已 **註冊** 的語音時的行為。|
| 文字相依和與文字無關的驗證有何不同？ | 文字相依驗證要求註冊和辨識都需要特定的傳遞片語。 與文字無關的驗證需要較長的語音範例進行註冊，但可以說出任何內容，包括在辨識期間。|
| 支援哪些語言？ | 英文、法文、西班牙文、中文、德文、義大利文、日文和葡萄牙文 |
| 支援哪些 Azure 區域？ | 說話者辨識是預覽服務，目前僅適用于美國西部區域。|
| 支援哪種音訊格式？ | Mono 16 位，16kHz PCM 編碼的 WAV |
| **接受** 和 **拒絕** 回應不正確，您要如何調整臨界值？ | 由於最佳閾值會隨著案例而有所不同，因此 API 會根據預設閾值0.5 來決定是否「接受」或「拒絕」。 建議 Advanced users 覆寫預設的決策，並根據您自己的案例微調結果。 |
| 您可以多次註冊一個說話者嗎？ | 是，針對文字相依驗證，您可以註冊最多50次的喇叭。 針對文字獨立驗證或說話者辨識，您最多可以註冊300秒的音訊。 |
| 哪些資料會儲存在 Azure 中？ | 註冊音訊會儲存在服務中，直到 [刪除](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments)語音設定檔為止。 不會保留或儲存辨識音訊範例。 |

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> * 完成說話者辨識 [基本文章](./get-started-speaker-recognition.md) ，以瞭解您可以在應用程式中使用的常見設計模式。
> * 請參閱 [影片教學](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) 課程，以瞭解與文字無關的說話者驗證。