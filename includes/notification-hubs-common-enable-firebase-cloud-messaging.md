---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095192"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>建立適用於 Android 的 Firebase 專案並啟用 Firebase 雲端通訊

1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案，並輸入 **PushDemo** 作為**專案名稱**。

    > [!NOTE]
    > 系統會為您產生唯一的名稱。 根據預設，這會由您所提供名稱的小寫變體加上產生的數字 (以破折號分隔) 來組成。 您可以變更此名稱，但必須仍然是全域唯一的。

1. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]。

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. 在 [將 Firebase 新增至 Android 應用程式] 頁面上，採取下列步驟。
    1. 針對 [Android 套件名稱]，請輸入套件的名稱。 例如： `com.<organization_identifier>.<package_name>` 。

        ![指定套件名稱](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. 選取 [註冊應用程式]。  
    1. 選取 [下載 google-services.json]。 然後將檔案儲存到本機資料夾，以供稍後使用，然後選取 [下一步]。  

        ![下載 google-services.json](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. 選取 [下一步] 。
    1. 選取 [繼續前往主控台]

        > [!NOTE]
        > 如果 [繼續前往主控台] 按鈕因為 [驗證安裝] 檢查而未啟用，請選擇 [略過此步驟]。

1. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]。

    ![選取專案設定](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > 如果尚未下載 **google-services.json** 檔案，您可以在此頁面下載。

1. 切換到頂端的 [雲端通訊] 索引標籤。 複製並儲存**伺服器金鑰**以供稍後使用。 您可使用此值來設定通知中樞。

    ![複製伺服器金鑰](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
