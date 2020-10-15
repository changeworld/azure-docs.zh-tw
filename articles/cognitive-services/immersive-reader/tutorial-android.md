---
title: 教學課程：使用 Android 程式碼範例啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將設定及執行可啟動沈浸式閱讀程式的範例 Android 應用程式。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516430"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>教學課程：使用 Android Java 程式碼範例啟動沈浸式閱讀程式

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 此教學課程說明如何建立可啟動沈浸式閱讀程式的 Android 應用程式。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用範例專案設定及執行 Android 應用程式。
> * 取得存取權杖。
> * 使用範例內容啟動沈浸式閱讀程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./how-to-create-immersive-reader.md)來設定。 設定環境屬性時，您需要用到在這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。
* [Git](https://git-scm.com/)。
* [沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>設定驗證認證

1. 啟動 Android Studio，然後從 **immersive-reader-sdk/js/samples/quickstart-java-android** 目錄 (Java) 或 **immersive-reader-sdk/js/samples/quickstart-kotlin** 目錄 (Kotlin) 中開啟專案。

1. 在 [/assets] 資料夾內建立名為 **env** 的檔案。 新增下列名稱和值，並提供適當的值。 請勿將此檔案認可到原始程式碼控制，因為其中包含不應公開的機密資料。
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

從 AVD 管理員中選擇裝置模擬器，然後執行專案。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)。
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上的程式碼範例。