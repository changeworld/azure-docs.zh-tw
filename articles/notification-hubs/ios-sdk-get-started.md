---
title: 使用 Azure 通知中樞和 iOS SDK 將推播通知傳送至 iOS
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞和 Apple Push Notification Service 將推播通知傳送至 iOS 裝置。
author: sethmanheim
ms.author: sethm
ms.date: 08/10/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 3ec96ff0fdebc0ac862af00c699ec489567a7144
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426768"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>教學課程：使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式

本教學課程會示範如何設定 Azure 通知中樞，以及如何透過 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) 設定認證，以將通知推送至 iOS 裝置。 

執行本教學課程是後續 Objective C 和 Swift iOS 教學課程的必要條件，並且涵蓋下列步驟：

- 產生憑證簽署要求檔案。
- 為您的應用程式要求推播通知。
- 建立應用程式的佈建設定檔。
- 建立通知中樞。
- 使用 APNS 資訊設定您的通知中樞。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/free/)。

您也需要下列項目：

- 作用中的 [Apple 開發人員](https://developer.apple.com/)帳戶。
- 執行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及安裝在您 Keychain 中的有效開發人員憑證。
- 執行 iOS 10 或更新版本的 iPhone 或 iPad。
- 在 [Apple 入口網站](https://developer.apple.com/)中註冊且與您憑證相關聯的實體裝置。

如果您不熟悉此服務，請務必閱讀 [Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)。

> [!NOTE]
> 通知中樞將設定為只使用沙箱驗證模式。 您不應該將此驗證模式用於生產工作負載。

## <a name="generate-the-certificate-signing-request-file"></a>產生憑證簽署要求檔案

Apple Push Notification Service (APNS) 使用憑證來驗證您的通知服務。 遵循下列指示來建立必要的推播憑證，以便傳送和接收通知。 如需這些概念的詳細資訊，請參閱 [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 官方文件。

產生憑證簽署要求 (CSR) 檔案，Apple 會用此檔案來產生簽署的推播憑證：

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 此工具可從啟動控制板上的 [公用程式]  資料夾或 [其他]  資料夾開啟。

2. 選取 [金鑰鏈存取]  ，並展開 [憑證助理]  ，然後選取 [從憑證授權單位要求憑證]  。

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="螢幕擷取畫面：醒目提示 [從憑證授權單位要求憑證] 功能表選項。":::

   > [!NOTE]
   > 依預設，Keychain 存取會選取清單中的第一個項目。 如果您位於 [憑證] 類別中，而 **Apple Worldwide Developer Relations 憑證授權單位** 不是清單中的第一個項目，則可能因此而產生問題。 在產生 CSR (憑證簽署要求) 之前，請確定您擁有非金鑰項目，或已選取 **Apple Worldwide Developer Relations 憑證授權單位** 金鑰。

3. 選取您的 **使用者電子郵件地址** ，輸入 **一般名稱** 值，並確定您已指定 [儲存至磁碟]  ，然後選取 [繼續]  。 請將 [CA 電子郵件地址]  留空，因為這不是必要資訊。

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="必要的憑證資訊":::

4. 在 [另存新檔]  中輸入 CSR 檔案的名稱，在 [位置]  中選取位置，然後選取 [儲存]  。

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="選擇檔案名稱":::

   此動作會在選取的位置儲存 CSR 檔案。 預設位置是 **桌面** 。 請記住為檔案選擇的位置。

接著，向 Apple 註冊您的應用程式，並啟用推播通知，然後上傳這個匯出的 CSR 建立推播憑證。

## <a name="register-your-app-for-push-notifications"></a>針對推播通知註冊應用程式

若要將推播通知傳送至 iOS 應用程式，請向 Apple 註冊您的應用程式，並註冊進行推播通知。

1. 如果您尚未註冊應用程式，請瀏覽至 Apple Developer Center 上的 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 使用您的 Apple ID 登入入口網站，然後選取 [識別碼]  。 然後選取 [+]  以註冊新的應用程式。

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="應用程式識別碼頁面":::

2. 在 [註冊新識別碼]  畫面上，選取 [應用程式識別碼]  選項按鈕。 然後選取 [繼續]。

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="註冊新的識別碼頁面":::

3. 將新應用程式的下列三個值予以更新，然後選取 [繼續]  ：

   - **描述** ：輸入您應用程式的描述性名稱。
   - **套件組合識別碼** ：以 [應用程式分發指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) \(英文\) 中所提的 **Organization Identifier.Product Name** 形式輸入套件組合識別碼。 [組織識別碼]  和 [產品名稱]  值必須符合當您建立 Xcode 專案時使用的組織識別碼和產品名稱。 在下列螢幕擷取畫面中， **NotificationHubs** 值會作為組織識別碼， **GetStarted** 值會作為產品名稱。 請確定 **套件組合識別碼** 值符合您 Xcode 專案中的值，好讓 Xcode 使用正確的發行設定檔。

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="註冊應用程式識別碼":::

   - **推播通知** ：勾選 [功能]  區段中的 [推播通知]  選項。

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="註冊新的應用程式識別碼":::

      此動作會產生您的應用程式識別碼，並要求您確定此資訊。 選取 [繼續]  ，然後選取 [註冊]  以確認新的應用程式識別碼。

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="確認新的應用程式識別碼":::

      選取 **註冊** 之後，您會在 [憑證、識別碼與設定檔] 頁面中看到作為明細項目的新應用程式識別碼。

4. 在 [憑證、識別碼與設定檔]  頁面的 [識別碼]  底下，找出您剛才建立的應用程式識別碼明細項目，然後選取其資料列以顯示 [編輯您的應用程式識別碼設定]  畫面。

## <a name="create-a-certificate-for-notification-hubs"></a>建立通知中樞的憑證

> [!NOTE]
> 隨著 iOS 13 的發行，您只能使用權杖型驗證來接收無訊息通知。 如果您要針對 APNS 認證使用憑證型驗證，則必須切換為使用權杖型驗證。

需要憑證才能搭配使用通知中樞與 **APNS** 。 完成此步驟的方式有兩種：

- 建立可直接上傳至通知中樞的 **.p12** 檔案。

- 建立 **.p8** 檔案，其可用於[權杖型驗證](notification-hubs-push-notification-http2-token-authentication.md) (較新的方法)。

第二個選項具有許多優點 (相較於使用憑證)，如 [APNS 權杖型 (HTTP/2) 驗證](notification-hubs-push-notification-http2-token-authentication.md)中所述。 不過，這兩種方法的步驟皆有提供。

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>選項 1：建立可直接上傳至通知中樞的 .p12 推送憑證

1. 向下瀏覽至已核取的 [推播通知]  選項，然後選取 [設定]  來建立憑證。

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="應用程式識別碼":::

2. [Apple 推播通知服務 SSL 憑證]  視窗隨即出現。 在 [開發 SSL 憑證] 區段中選取 [建立憑證] 按鈕。

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="建立憑證":::

   [建立新憑證]  畫面會隨即顯示。

   > [!NOTE]
   > 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 確定在傳送通知時使用相同的憑證類型。

3. 選取 [選擇檔案]，瀏覽到您在第一個工作中儲存 CSR 檔案的位置，然後按兩下憑證名稱來將其下載。 然後選取 [繼續]。

4. 在入口網站建立憑證之後，請選取 [下載]  按鈕。 儲存憑證，並記住其儲存位置。

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="下載憑證":::

   憑證會下載並儲存在您的 [下載] 資料夾中。

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="尋找憑證檔案":::

   依預設，下載的開發憑證會名為 **aps_development.cer** 。

5. 按兩下下載的推播憑證 **aps\_development.cer** 。 此動作會將新的憑證安裝在金鑰鏈中，如下圖所示：

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="金鑰鏈存取":::

   雖然憑證中的名稱可能會不同，不過名稱會加上 **Apple Development iOS Push Services** 前置詞。

6. 在 Keychain Access 中，以滑鼠右鍵按一下您在 [憑證]  類別中建立的新推播憑證。 選取 [匯出]  、為檔案命名、選取 [.p12]  格式，然後選取 [儲存]  。

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="匯出憑證":::

   您可以選擇使用密碼來保護憑證，但這是選擇性的。 如果您想要略過密碼建立，請按一下 [確定]  。 記下匯出的 .p12 憑證的檔案名稱和位置。 其可用來啟用 APNS 驗證功能。

   > [!NOTE]
   > 您的 .p12 檔案名稱和位置可能與本教學課程中所顯示的不同。

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>選項 2：建立可用於權杖型驗證的 .p8 憑證

1. 記下下列詳細資料：

   - **應用程式識別碼首碼** (這是 **小組識別碼** )
   - **套件組合識別碼**

2. 回到 [憑證、識別碼與設定檔]  中，按一下 [金鑰]  。 如果您已為 **APNS** 設定金鑰，則可重複使用在建立之後所下載的 .p8 憑證。 若是如此，您可以忽略 3 到 5 的步驟。

3. 按一下 [ **+** ] 按鈕 (或 [建立金鑰]  按鈕) 以建立新的金鑰。

4. 提供適當的 **金鑰名稱** 值，勾選 [Apple Push Notifications Service (APNS)] 選項，接著在下一個畫面上，依序按一下 [繼續]、[註冊]。

5. 按一下 [下載]  然後將 **.p8** 檔案 (前置為 AuthKey_`AuthKey_` ) 移至安全的本機目錄，然後按一下 [完成]  。

   > [!IMPORTANT]
   > 請務必將您的 .p8 檔案保留在安全的位置 (並儲存備份檔案)。 下載完您的金鑰之後，就無法再重新下載，因為伺服器複本已移除。

6. 在 [金鑰]  上，按一下剛才建立的金鑰 (如果選擇改用現有的金鑰，則按一下現有金鑰)。

7. 請記下 **金鑰識別碼** 的值。

8. 在所選擇的適當應用程式中開啟您的 .p8 憑證，例如 [Visual Studio Code](https://code.visualstudio.com/)，然後記下金鑰值。 這是 **-----BEGIN PRIVATE KEY-----** 和 **-----END PRIVATE KEY-----** 之間的值。

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   這是稍後將用來設定通知中樞的權杖值。

在這些步驟的最後，您應該具有下列資訊，以便稍後在[使用 APNS 資訊設定通知中樞](#configure-the-notification-hub-with-apns-information)中使用：

- **小組識別碼** (請參閱步驟 1)
- **套件組合識別碼** (請參閱步驟 1)
- **金鑰識別碼** (請參閱步驟 7)
- **權杖值** (.p8 金鑰值，請參閱步驟 8)

## <a name="create-a-provisioning-profile"></a>建立佈建設定檔

1. 返回 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)，選取 [憑證、識別碼與設定檔]  ，從左側功能表中選取 [設定檔]  ，然後選取 [+]  以建立新的設定檔。 [註冊新佈建設定檔]  的畫面會隨即出現。

2. 在 [開發]  下，選取 [iOS 應用程式開發]  作為佈建設定檔類型，然後選取 [繼續]  。

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="佈建設定檔清單":::

3. 接著，從 [應用程式識別碼] 下拉式清單選取您建立的應用程式識別碼，然後選取 [繼續]。

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="選取應用程式識別碼":::

4. 在 [選取憑證]  視窗中，選取用於程式碼簽署的開發憑證，然後選取 [繼續]  。 此憑證不是您建立的推播憑證。 如果沒有憑證存在，您必須加以建立。 如果憑證存在，請跳到下一個步驟。 若要建立開發憑證 (憑證不存在的話)：

   1. 如果您看到 [沒有可用憑證]  ，請選取 [建立憑證]  。
   2. 在 [軟體]  區段中，選取 [Apple 開發]  。 然後選取 [繼續]  。
   3. 在 [建立新憑證]  畫面中，選取 [選擇檔案]  。
   4. 瀏覽至您稍早建立的 **憑證簽署要求** 憑證並加以選取，然後選取 [開啟]  。
   5. 選取 [繼續]  。
   6. 下載開發憑證，並記住其儲存位置。

5. 返回 [憑證、識別碼與設定檔]  頁面，從左側功能表中選取 [設定檔]  ，然後選取 [+]  以建立新的設定檔。 [註冊新佈建設定檔]  的畫面會隨即出現。

6. 在 [選取憑證]  視窗中，選取您剛才建立的開發憑證。 然後選取 [繼續]  。

7. 接下來，選取要用來測試的裝置，然後選取 [繼續]  。

8. 最後，在 [佈建設定檔名稱] 中為設定檔選擇名稱，然後選取 [產生]。

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="選擇佈建設定檔名稱":::

9. 建立新的佈建設定檔後，請選取 [下載]  。 記住其儲存位置。

10. 瀏覽至佈建設定檔的位置，然後按兩下來將其安裝在您的 Xcode 開發機器上。

## <a name="create-a-notification-hub"></a>建立通知中樞

在本節中，您會建立一個通知中樞，並透過 .p12 推播憑證或權杖型驗證，使用 APNS 來設定驗證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取左功能表上的 [所有服務]，然後選取 [行動] 區段中的 [通知中樞]。 選取服務名稱旁邊的星號圖示，將服務加到左功能表上的 [我的最愛] 區段。 將 [通知中樞] 新增至 [我的最愛] 之後，請加以選取。

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure 入口網站":::

3. 在 [通知中樞] 頁面上，選取工具列上的 [新增]。

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="新增工具列按鈕":::

4. 在 [通知中樞] 頁面上，執行下列步驟：

   1. 在 [通知中樞] 中輸入名稱。
   2. 在 [建立新的命名空間] 中輸入名稱。 命名空間包含一或多個通知中樞。
   3. 從 [位置] 下拉式清單中選取值。 此值會指定您要在其中建立通知中樞的位置。
   4. 在 [資源群組] 中選取現有的資源群組，或建立新的資源群組。
   5. 選取 [建立]。

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="設定屬性":::

5. 選取 [通知] \(鈴鐺圖示)，然後選取 [前往資源]。 您也可以重新整理 [通知中樞] 頁面中的清單，然後選取您的中樞。

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="入口網站通知":::

6. 從清單中選取 [存取原則]。 請記下您可使用的兩個連接字串。 您稍後需要用到這些連接字串來處理推播通知。

   > [!IMPORTANT]
   > 請勿在應用程式中使用 **DefaultFullSharedAccessSignature** 原則。 這只能在後端使用。

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="連接字串":::

## <a name="configure-the-notification-hub-with-apns-information"></a>使用 APNS 資訊設定您的通知中樞

在 [Notification Services] 下，選取 [Apple (APNS)]，然後遵循先前在＜[建立通知中樞的憑證](#create-a-certificate-for-notification-hubs)＞一節中選擇的方法進行適當的步驟。

> [!NOTE]
> 只有在您想傳送推播通知給從市集購買應用程式的使用者時，才可使用 [生產] 的 [應用程式模式]。

### <a name="option-1-use-a-p12-push-certificate"></a>選項 1：使用 .p12 推送憑證

1. 選取 [憑證] 。

2. 選取 [檔案] 圖示。

3. 選取您先前匯出的 .p12 檔案，然後選取 [開啟]  。

4. 若有需要，請指定正確的密碼。

5. 選取 [沙箱]  模式。

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="設定":::

6. 選取 [儲存]  。

### <a name="option-2-use-token-based-authentication"></a>選項 2：使用權杖式驗證

1. 選取 **權杖** 。

2. 輸入您稍早取得的下列值：

   - **金鑰識別碼**
   - **套件組合識別碼**
   - **小組識別碼**
   - **權杖**

3. 選擇 [沙箱] 

4. 選取 [儲存]  。

您現在已使用 APNS 來設定通知中樞。 您也擁有註冊應用程式和傳送推播通知所需的連接字串。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure 中建立並設定通知中樞，並將其設定為允許透過 Apple Push Notification Service (APNS) 將通知傳送至您的應用程式。 接下來，我們將建立 iOS 應用程式範例，並整合 Azure 通知中樞 SDK，讓其可以接收透過 Azure 入口網站傳送的推播通知。 根據您選擇的語言，前往下列教學課程：

- [教學課程：使用 Swift 與 iOS 應用程式整合]()
