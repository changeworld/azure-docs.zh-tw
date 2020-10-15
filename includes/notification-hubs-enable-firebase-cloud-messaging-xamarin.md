---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68728840"
---
1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]  。 

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. 在 [將 Firebase 新增至 Android 應用程式]  頁面上，採取下列步驟： 
    1. 針對 [Android 套件名稱]  ，請輸入套件的名稱。 例如： `tutorials.tutoria1.xamarinfcmapp` 。 

        ![指定套件名稱](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 選取 [註冊應用程式]  。  
    1. 選取 [下載 google-services.json]  。 然後將檔案儲存到專案的資料夾，再選取 [下一步]  。 如果您尚未建立 Visual Studio 專案，則可以在建立專案之後執行此步驟。 

        ![下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. 選取 [下一步]  。 
    7. 選取 [略過此步驟]  。 

        ![略過最後一個步驟](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]  。

    ![選取專案設定](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果尚未下載 **google-services.json** 檔案，您可以在此頁面下載。 

    ![從 [一般] 索引標籤下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. 切換到頂端的 [雲端通訊]  索引標籤。 複製並儲存**伺服器金鑰**以供稍後使用。 您可使用此值來設定通知中樞。

    ![複製伺服器金鑰](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
