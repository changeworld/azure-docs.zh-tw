---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "70935136"
---
1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]  。 

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 [將 Firebase 新增至 Android 應用程式]  頁面上，採取下列步驟： 
    1. 對於 [Android 套件名稱]  ，複製應用程式 build.gradle 檔案的 **applicationId** 值。 在此範例中為 `com.fabrikam.fcmtutorial1app`。 

        ![指定套件名稱](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 選取 [註冊應用程式]  。 
4. 選取 [下載 google-services.json]  ，將檔案儲存到專案的**應用程式**資料夾，然後選取 [下一步]  。 

    ![下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. 在 Android Studio 中對於專案進行下列**設定變更**。 
    1.  在專案層級 build.gradle 檔案 (&lt;project&gt;/build.gradle) 中，將下列陳述式新增至 [相依性]  區段。 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. 在應用程式層級 build.gradle 檔案 (&lt;project&gt;/&lt;app-module&gt;/build.gradle) 中，將下列陳述式新增至 [相依性]  區段。 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. 將下行新增至應用程式層級 build.gradle 檔案結尾的相依性區段之後。 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. 在工具列上選取 [立即同步]  。 
 
        ![build.gradle 組態變更](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. 選取 [下一步]  。 
7. 選取 [略過此步驟]  。 

    ![略過最後一個步驟](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]  。

    ![選取專案設定](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果您尚未將 google-services.json 檔案下載到 Android Studio 專案的 **app** 資料夾，可以在此頁面下載。 
5. 切換到頂端的 [雲端通訊]  索引標籤。 
6. 複製並儲存**伺服器金鑰**以供稍後使用。 您可以使用此值來設定中樞。
