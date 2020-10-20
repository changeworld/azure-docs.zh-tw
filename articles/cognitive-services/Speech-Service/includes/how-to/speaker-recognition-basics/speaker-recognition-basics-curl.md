---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 0fd13437ecd494eebf79fa80ed210a0663864104
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875414"
---
在本快速入門中，您將了解使用語音 SDK 進行說話者辨識的基本設計模式，包括：

* 與文字相關，以及與文字無關的驗證
* 說話者辨識，用以識別語音群組之間的語音樣本
* 刪除語音設定檔

如需有關語音辨識概念的高階探討，請參閱 [概觀](../../../speaker-recognition-overview.md) 文章。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 說話者辨識目前*僅*支援在 `westus` 區域中建立的 Azure 語音資源。

## <a name="text-dependent-verification"></a>文字相依驗證

說話者驗證是確認說話者符合已知或**已註冊**語音的動作。 第一個步驟是**註冊**一個語音設定檔，讓服務有一些內容可比較未來的語音樣本。 在此範例中，您會使用**與文字相關的**策略來註冊設定檔，這需要使用特定的傳遞片語來進行註冊和驗證。 如需支援的傳遞片語清單，請參閱 [參考文件](https://docs.microsoft.com/rest/api/speakerrecognition/)。

首先[建立語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createprofile)。 您必須將語音服務訂用帳戶金鑰和區域插入本文中的每個 curl 命令。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

請注意，語音設定檔有三種類型：

- 文字相依驗證
- 與文字無關的驗證
- 與文字無關的識別

在此情況下，您會建立與文字相關的驗證語音設定檔。 您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

接下來，[註冊語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createenrollment)。 針對 `--data-binary` 參數值，請在您的電腦上指定包含其中一個支援複雜密碼的音訊檔，例如 "my voice is my passport, verify me"。 您可以使用應用程式 (例如 [Windows 語音錄音機](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)) 來記錄這類音訊檔案，也可以使用[文字轉換語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech)加以產生。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

此回應會告訴您，您需要註冊兩個以上的音訊樣本。

在註冊總計三個音訊樣本之後，您應該會收到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

您現在已準備好[針對語音設定檔驗證音訊樣本](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/verifyprofile)。 此音訊樣本應該包含與您用來註冊語音設定檔的樣本相同的複雜密碼。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` 表示複雜密碼相符且驗證成功。 回應也包含範圍 0.0-1.0 的相似度分數。

若要完成，請[刪除語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

沒有回應。

## <a name="text-independent-verification"></a>與文字無關的驗證

相對於**與文字相關的**驗證，**與文字無關的**驗證是：

* 不需要說出特定的傳遞片語，可以說出任何內容
* 不需要三個音訊樣本，但是總計需要 20 秒的音訊

首先[建立與文字無關的驗證設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

接下來，[註冊語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createenrollment)。 同樣地，您需要提交包含總計 20 秒音訊的音訊樣本，而不是提交三個音訊樣本。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

提交足夠的音訊樣本後，您應該會收到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

您現在已準備好[針對語音設定檔驗證音訊樣本](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/verifyprofile)。 同樣地，此音訊樣本不需要包含複雜密碼。 其可包含任何語音，只要其中包含至少總計四秒的音訊即可。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` 表示驗證成功。 回應也包含範圍 0.0-1.0 的相似度分數。

若要完成，請[刪除語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

沒有回應。

## <a name="speaker-identification"></a>說話者識別

說話者辨識是用來判斷從指定的已註冊語音群組說話者的**身份**。 此流程類似於**與文字無關的驗證**，主要差異在於能夠一次針對多個語音設定檔進行驗證，而不是針對單一設定檔進行驗證。

首先[建立與文字無關的識別設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

接下來，[註冊語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createenrollment)。 同樣地，您需要提交包含總計 20 秒音訊的音訊樣本。 這些樣本不需要包含複雜密碼。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

提交足夠的音訊樣本後，您應該會收到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

您現在已準備好[使用語音設定檔來識別音訊樣本](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker)。 識別命令可接受以逗號分隔的可能語音設定檔識別碼清單。 在此情況下，您只要傳入先前建立的語音設定檔識別碼即可。 不過，如果您想要，可以傳入多個語音設定檔識別碼，其中每個語音設定檔都是以來自不同語音的音訊樣本註冊。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

您應該會得到以下回應。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

回應包含最符合您所提交音訊樣本的語音設定檔識別碼。 也包含候選語音設定檔清單，並依相似度順序排序。

若要完成，請[刪除語音設定檔](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

沒有回應。
