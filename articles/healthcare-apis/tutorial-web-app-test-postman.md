---
title: Web 應用程式教學課程 - 測試對 Azure API for FHIR 的連線
description: 這些教學課程會逐步解說部署簡單 Web 應用程式的範例。 此教學課程的這一節將逐步解說如何使用 Postman 來測試對 FHIR 伺服器的連線
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023307"
---
# <a name="testing-the-fhir-api"></a>測試 FHIR API
在前兩個步驟中，您已部署 Azure API for FHIR 並註冊您的用戶端應用程式。 您現在已準備好要測試您的 Azure API for FHIR 是否已設定使用您的用戶端應用程式。 

## <a name="retrieve-capability-statement"></a>擷取功能陳述式
首先，我們會取得您 Azure API for FHIR 的功能陳述式。 
1. 開啟 Postman
1. 藉由執行 GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata 來擷取功能陳述式。 在下圖中，FHIR 伺服器名稱是 **fhirserver**。

![功能陳述式](media/tutorial-web-app/postman-capability-statement.png)

接下來，我們將嘗試取得病患。 若要擷取病患，請輸入 GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient。 您會收到 401 未經授權錯誤。 此錯誤是因為您尚未證明您應該具有病患資料的存取權。

## <a name="get-patient-from-fhir-server"></a>從 FHIR 伺服器取得病患
![失敗的病患](media/tutorial-web-app/postman-patient-authorization-failed.png)

若要取得存取權，您需要有存取權杖。
1. 在 Postman 中，選取 [授權] 並將類型設定為 **OAuth 2.0**
1. 選取 [取得新的存取權杖]
1. 填寫欄位並選取 [要求權杖]。 您可以在以下看到此教學課程中每個欄位的值。

|欄位                |值                                                               |
|---------------------|--------------------------------------------------------------------|
|權杖名稱           |您的權杖名稱                                               |
|授與類型           |授權碼                                                  |
|回呼 URL         |https://www.getpostman.com/oauth2/callback                          |
|驗證 URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|存取權杖 URL     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|用戶端識別碼            |您在先前步驟中複製的用戶端識別碼             |
|用戶端密碼        |\<BLANK>                                                            |
|影響範圍                |\<BLANK>                                                            |
|State                |1234                                                                |
|用戶端驗證|在主體中傳送用戶端認證                                     |

4. 使用您的認證登入，並選取 [接受]
1. 在結果上向下滾動，並選取 [使用權杖]
1. 再次選取頂端的 [傳送]，這次您應該會取得結果 ![成功病患](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>將病患發佈至 FHIR 伺服器
既然您具有存取權，您便可以建立新病患。 以下是您可以新增至 FHIR 伺服器之簡單病患的範例。 在 Postman 的 [主體] 區段中，輸入下列程式碼。

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
此文章會在您的 FHIR 伺服器中使用名稱 James Tiberious Kirk 建立新病患。
![發佈病患](media/tutorial-web-app/postman-post-patient.png)

如果您執行上述的 GET 步驟再次擷取病患，則會在輸出中看到 James Tiberious Kirk 列出。

## <a name="troubleshooting-access-issues"></a>疑難排解存取問題
如果您在上述任何步驟中遇到問題，請參閱我們在 Azure Active Directory 和 Azure API for FHIR 上匯集在一起的文件。 

* [Azure AD 和 Azure API for FHIR](azure-ad-hcapi.md) - 此文件概述 Azure Active Directory 的一些基本原則，以及其如何與 Azure API for FHIR 互動。
* [存取權杖驗證](azure-ad-hcapi-token-validation.md) - 本操作指南提供有關存取權杖驗證與解決存取問題需採取步驟的更具體詳細資料。

## <a name="next-steps"></a>後續步驟
既然您可以成功地連線到用戶端應用程式，您就可以開始撰寫 Web 應用程式。

>[!div class="nextstepaction"]
>[撰寫 Web 應用程式](tutorial-web-app-write-web-app.md)



