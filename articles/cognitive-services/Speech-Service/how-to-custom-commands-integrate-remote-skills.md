---
title: 如何：將自訂命令應用程式匯出為遠端技能-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何將自訂命令應用程式匯出為技能
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 6ab3e1e063409832de839da26eba80efd8b3f4d5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344795"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>將自訂命令應用程式匯出為遠端技能

在本文中，您將瞭解如何將自訂命令應用程式匯出為遠端技能。

## <a name="prerequisites"></a>先決條件
> [!div class="checklist"]
> * [瞭解 Bot Framework 技能](https://aka.ms/speech/cc-skill-overview)
> * [瞭解技能資訊清單](https://aka.ms/speech/cc-skill-manifest)
> * [如何從 Bot Framework Bot 叫用技能](https://aka.ms/speech/cc-skill-consumer)
> * 現有的自訂命令應用程式。 如果您沒有任何自訂命令應用程式，請嘗試使用- [快速入門：使用自訂命令建立語音助理](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>作為遠端技能的自訂命令
* Bot Framework 技能是可重複使用的交談式技能建築物，其中涵蓋交談式使用案例，可讓您在幾分鐘內將廣泛的功能新增至 Bot。 若要閱讀詳細資訊，請移至 [Bot Framework 技能](https://microsoft.github.io/botframework-solutions/overview/skills/)。
* 自訂命令應用程式可以匯出為技能。 然後可以透過 Bot Framework Bot 的遠端技能通訊協定來叫用這種技能。

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>設定要公開為遠端技能的應用程式

### <a name="application-level-settings"></a>應用層級設定
1. 在左面板中，選取 [**設定**  >  **遠端技巧**]。
1. 將 **遠端技能啟用** 切換為開啟。

### <a name="authentication-to-skills"></a>技術驗證
1. 如果您想要啟用驗證，請將您要設定之 Bot Framework Bot 的 Microsoft 應用程式識別碼新增至呼叫自訂命令應用程式。
      > [!div class="mx-imgBorder"]
      > ![將 MSA 識別碼新增至技能](media/custom-commands/skill-add-msa-id.png)

1. 如果您至少已將一個專案新增至清單，則會在應用程式上啟用驗證，而且只有允許的 bot 能夠呼叫應用程式。
> [!TIP]
>  若要停用驗證，請從允許的清單中刪除所有的 Microsoft 應用程式識別碼。 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>啟用/停用要公開為技能的命令

您可以選擇要透過遠端技能匯出哪些命令。

1. 若要透過技能公開命令，請選取 [**啟用命令以取得技能**] 下的 [**啟用新命令**]。
1. 從下拉式清單中，選取您要新增的命令。
1. 選取 [儲存]。

### <a name="configure-triggering-utterances-for-commands"></a>設定觸發命令的語句
自訂命令會使用針對命令設定的範例句子，以產生觸發語句的技能。 這些 **觸發語句** 將用來產生 **發送器** 區段 [**技能資訊清單**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)。

身為作者，您可能會想要控制要使用哪一個 **範例句子** 來產生技能的觸發語句。
1. 根據預設，命令中的所有 **觸發範例** 都會包含在資訊清單檔中。
1. 如果您想要明確消除任何一個範例，請從 [技能] 區段的 [**啟用的命令**] 區段中，選取 [**編輯**] 圖示。
    > [!div class="mx-imgBorder"]
    > ![編輯技能的已啟用命令](media/custom-commands/skill-edit-enabled-command.png)

1. 接下來，在您要省略的範例句子上，以**滑鼠右鍵按一下**[  >  **停用範例句子**]。
    > [!div class="mx-imgBorder"]
    > ![停用範例](media/custom-commands/skill-disable-example-sentences.png)

1. 選取 [儲存]。
1. 您將會注意到，您無法在這個視窗中加入新的範例。 如果需要這樣做，請移至 [設定] 區段的 [結束]，然後從 [ **命令** 可折疊] 選取相關的命令。 此時，您可以在 [ **範例句子** ] 區段中加入新專案。 這項變更將會自動反映在命令的 [遠端技能設定] 值中。

> [!IMPORTANT]
> 如果您現有的範例句子有 **字串 > 目錄** 資料類型的參考，則會自動從 [觸發語句] 清單中省略這些句子。 

## <a name="download-skill-manifest"></a>下載技能資訊清單
1. 在之後，您已 **發佈** 應用程式，您可以下載技能資訊清單檔。
1. 使用技能資訊清單來設定您的 Bot Framework 取用者 Bot，以呼叫自訂命令技能。
> [!IMPORTANT]
> 您必須 **發佈** 自訂命令應用程式，才能下載技能資訊清單。 </br>
> 此外，如果您對應用程式進行 **任何變更** ，則需要再次發佈應用程式，才能在資訊清單檔案中反映最新的變更。

> [!NOTE]
> 如果您在發行應用程式時遇到任何問題，並將錯誤導向至觸發語句的技能，請重新檢查 **已啟用命令**的設定以瞭解技能。 每個公開的命令都必須至少有一個有效的觸發語句。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為自訂命令應用程式啟用 CI/CD 程序](./how-to-custom-commands-deploy-cicd.md)