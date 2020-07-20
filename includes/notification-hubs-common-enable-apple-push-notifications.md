---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095189"
---
### <a name="register-your-ios-app-for-push-notifications"></a>針對推播通知註冊 iOS 應用程式

若要將推播通知傳送至 iOS 應用程式，請向 Apple 註冊您的應用程式，並註冊進行推播通知。  

1. 如果您尚未註冊應用程式，請瀏覽至 Apple Developer Center 上的 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 使用您的 Apple ID 登入入口網站，瀏覽至 [憑證、識別碼與設定檔]，然後選取 [識別碼]。 按一下 [+] 來註冊新的應用程式。

    ![iOS 佈建入口網站應用程式識別碼頁面](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. 在 [註冊新識別碼] 畫面上，選取 [應用程式識別碼] 選項按鈕。 然後選取 [繼續]。

    ![iOS 佈建入口網站的 [註冊新識別碼] 頁面](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. 將新應用程式的下列三個值予以更新，然後選取 [繼續]：

   * **描述**：輸入您應用程式的描述性名稱。

   * **套件組合識別碼**：以[應用程式分發指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)中所提的 **com.<organization_identifier>.<product_name>** 形式輸入套件組合識別碼。 在下列螢幕擷取畫面中，`mobcat` 值會作為組織識別碼，而 **PushDemo** 值會作為產品名稱。

      ![iOS 佈建入口網站的 [註冊應用程式識別碼] 頁面](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **推播通知**：勾選 [功能] 區段中的 [推播通知] 選項。

      ![註冊新應用程式識別碼的表單](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      此動作會產生您的應用程式識別碼，並要求您確定此資訊。 選取 [繼續]，然後選取 [註冊] 以確認新的應用程式識別碼。

      ![確認新的應用程式識別碼](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      選取**註冊**之後，您會在 [憑證、識別碼與設定檔] 頁面中看到作為明細項目的新應用程式識別碼。

1. 在 [憑證、識別碼與設定檔] 頁面的 [識別碼] 底下，找出您所建立的應用程式識別碼行明細項目。 然後選取其資料列，以顯示 [編輯您的應用程式識別碼設定] 畫面。

#### <a name="creating-a-certificate-for-notification-hubs"></a>建立通知中樞的憑證

需要憑證才能讓通知中樞與 **Apple Push Notification Services (APNS)** 搭配使用，其可透過下列兩種方式的其中一種來提供：

1. [建立可直接上傳至通知中樞的 p12 推播憑證](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (原始方法)

1. [建立可用於權杖型驗證的 p8 憑證](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (較新且建議的方法)

較新的方法具有許多優點，如 [APNS 權杖型 (HTTP/2) 驗證](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)中所述。 需要的步驟較少，但也會針對特定案例強制執行。 不過，這兩種方法的步驟皆會提供，因為這兩者都適用於本教學課程的目的。

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>選項 1：建立可直接上傳至通知中樞的 p12 推播憑證

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 此工具可從啟動控制板上的 [公用程式] 資料夾或 [其他] 資料夾開啟。

1. 選取 [金鑰鏈存取]，並展開 [憑證助理]，然後選取 [從憑證授權單位要求憑證]。

    ![使用金鑰鏈存取要求新憑證](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 依預設，Keychain 存取會選取清單中的第一個項目。 如果您位於 [憑證] 類別中，而 **Apple Worldwide Developer Relations 憑證授權單位**不是清單中的第一個項目，則可能因此而產生問題。 在產生 CSR (憑證簽署要求) 之前，請確定您擁有非金鑰項目，或已選取 **Apple Worldwide Developer Relations 憑證授權單位**金鑰。

1. 選取您的**使用者電子郵件地址**，輸入**一般名稱**值，並確定您已指定 [儲存至磁碟]，然後選取 [繼續]。 請將 [CA 電子郵件地址] 留空，因為這不是必要資訊。

    ![預期的憑證資訊](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. 在 [另存新檔] 中輸入**憑證簽署要求 (CSR) 檔案**的名稱，並且在 [位置] 中選取位置，然後選取 [儲存]。

    ![選擇憑證的檔案名稱](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    此動作會在選取的位置儲存 **CSR 檔案**。 預設位置是**桌面**。 請記住為檔案選擇的位置。

1. 回到 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)的 [憑證、識別碼與設定檔] 頁面上，向下瀏覽至已核取的 [推播通知] 選項，然後選取 [設定] 來建立憑證。

    ![編輯應用程式識別碼頁面](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. [Apple Push Notification Service TLS/SSL 憑證] 視窗隨即出現。 在 [開發 TLS/SSL 憑證] 區段下方，選取 [建立憑證] 按鈕。

    ![為應用程式識別碼建立憑證的按鈕](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    [建立新憑證] 畫面會隨即顯示。

    > [!NOTE]
    > 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 只要確定在傳送通知時使用相同的憑證類型即可。

1. 選取 [選擇檔案]，瀏覽到您儲存 **CSR 檔案**的位置，然後按兩下憑證名稱來將其下載。 然後選取 [繼續]。

1. 在入口網站建立憑證之後，請選取 [下載] 按鈕。 儲存憑證，並記住其儲存位置。

    ![產生的憑證 CSR 下載頁面](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    這會下載憑證並將其儲存到您電腦中的 [下載] 資料夾。

    ![在 [下載] 資料夾中找出憑證檔案](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 依預設，下載的開發憑證會名為 **aps_development.cer**。

1. 按兩下下載的推播憑證 **aps_development.cer**。 此動作會將新的憑證安裝在金鑰鏈中，如下圖所示：

    ![顯示新憑證的金鑰鏈存取憑證清單](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 雖然憑證中的名稱可能會不同，不過名稱會加上 **Apple Development iOS Push Services** 前置詞，並且會與適當的套件組合識別碼相關聯。

1. 在 Keychain Access 中，**Control** + **按一下**您在 [憑證] 類別中建立的新推播憑證。 選取 [匯出]、為檔案命名、選取 [p12] 格式，然後選取 [儲存]。

    ![以 p12 格式匯出憑證](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    您可以選擇使用密碼來保護憑證，但密碼是選擇性的。 如果您想要略過密碼建立，請按一下 [確定]。 記下匯出的 p12 憑證的檔案名稱和位置。 其可用來啟用 APNS 驗證功能。

    > [!NOTE]
    > 您的 p12 檔案名稱和位置可能與本教學課程中所顯示的不同。

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>選項 2：建立可用於權杖型驗證的 p8 憑證

1. 記下下列詳細資料：

    * **應用程式識別碼前置詞** (**小組識別碼**)
    * **套件組合識別碼**

1. 回到 [憑證、識別碼與設定檔] 中，按一下 [金鑰]。

   > [!NOTE]
   > 如果您已為 **APNS** 設定金鑰，則可重複使用在建立之後所下載的 p8 憑證。 若是如此，您可以忽略 **3** 到 **5** 的步驟。

1. 按一下 [ **+** ] 按鈕 (或 [建立金鑰] 按鈕) 以建立新的金鑰。
1. 提供適當的**金鑰名稱**值，然後勾選 [Apple Push Notifications Service (APNS)] 選項，接著在下一個畫面上，依序按一下 [繼續]、[註冊]。
1. 按一下 [下載] 然後將 **p8** 檔案 (前置為 *AuthKey_* ) 移至安全的本機目錄，然後按一下 [完成]。

   > [!NOTE]
   > 請務必將您的 p8 檔案保留在安全的位置 (並儲存備份檔案)。 下載完您的金鑰之後，就無法再重新下載，因為伺服器複本已移除。
  
1. 在 [金鑰] 上，按一下建立的金鑰 (如果選擇改用現有的金鑰，則按一下現有金鑰)。
1. 請記下**金鑰識別碼**的值。
1. 在所選擇的適當應用程式中開啟您的 p8 憑證，例如 [**Visual Studio Code**](https://code.visualstudio.com)。 記下 **-----BEGIN PRIVATE KEY-----** 和 **-----END PRIVATE KEY-----** 之間的金鑰值。

    -----BEGIN PRIVATE KEY-----  
    <key_value>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > 這是稍後將用來設定**通知中樞**的**權杖值**。

在這些步驟的最後，您應該具有下列資訊，以便稍後在[使用 APNS 資訊設定通知中樞](#configure-your-notification-hub-with-apns-information)中使用：

* **小組識別碼** (請參閱步驟 1)
* **套件組合識別碼** (請參閱步驟 1)
* **金鑰識別碼** (請參閱步驟 7)
* **權杖值** (在步驟 8 中取得的 p8 金鑰值)

### <a name="create-a-provisioning-profile-for-the-app"></a>建立應用程式的佈建設定檔

1. 返回 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)，選取 [憑證、識別碼與設定檔]，從左側功能表中選取 [設定檔]，然後選取 [+] 以建立新的設定檔。 [註冊新佈建設定檔] 的畫面會隨即出現。

1. 在 [開發] 下，選取 [iOS 應用程式開發] 作為佈建設定檔類型，然後選取 [繼續]。

    ![佈建設定檔清單](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 接著，從 [應用程式識別碼] 下拉式清單選取您建立的應用程式識別碼，然後選取 [繼續]。

    ![選取應用程式識別碼](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. 在 [選取憑證] 視窗中，選取用於程式碼簽署的開發憑證，然後選取 [繼續]。

    > [!NOTE]
    > 此憑證不是您在[上一個步驟中](#creating-a-certificate-for-notification-hubs)建立的推播憑證。 這是您的開發憑證。 如果此憑證不存在，您必須加以建立，因為這是本教學課程的[必要條件](#prerequisites)。 開發人員憑證可以透過 [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) 或 [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)，在 [Apple 開發人員入口網站](https://developer.apple.com)中建立。

1. 返回 [憑證、識別碼與設定檔] 頁面，從左側功能表中選取 [設定檔]，然後選取 [+] 以建立新的設定檔。 [註冊新佈建設定檔] 的畫面會隨即出現。

1. 在 [選取憑證] 視窗中，選取您建立的開發憑證。 然後選取 [繼續]。

1. 接下來，選取要用來測試的裝置，然後選取 [繼續]。

1. 最後，在 [佈建設定檔名稱] 中為設定檔選擇名稱，然後選取 [產生]。

    ![選擇佈建設定檔名稱](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 建立新的佈建設定檔後，請選取 [下載]。 記住其儲存位置。

1. 瀏覽至佈建設定檔的位置，然後按兩下來將其安裝在您的開發機器上。
