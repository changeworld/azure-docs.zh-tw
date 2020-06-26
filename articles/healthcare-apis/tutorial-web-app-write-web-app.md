---
title: Web 應用程式教學課程 - 撰寫 Web 應用程式
description: 此教學課程會逐步解說部署簡單 Web 應用程式的範例。 教學課程的這一節將逐步解說如何撰寫 Web 應用程式。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 8d9153cfa645c380a7e53dd75c7f7ed08eaa95c4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "84870318"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>撰寫 Azure Web 應用程式以讀取 FHIR 資料
既然您可以連線到 FHIR 伺服器並張貼資料，就可以開始撰寫可讀取 FHIR 資料的 Web 應用程式。 在此教學課程的最後一個步驟中，我們將逐步解說如何撰寫和存取 Web 應用程式。

## <a name="create-web-application"></a>建立 Web 應用程式
在 Azure 中，選取 [建立資源]****，然後選取 [Web 應用程式]****。 務必將您的 Web 應用程式命名為您在重新導向 URI 中為用戶端應用程式指定的任何內容，或返回並將重新導向 URI 更新為新的名稱。 

![建立 Web 應用程式](media/tutorial-web-app/create-web-app.png)

Web 應用程式可供使用之後，請**移至資源**。 選取右側開發工具底下的 [App Service 編輯器 (預覽)]****，然後選取 [執行]****。 選取 [執行] 將開啟 App Service 編輯器。 以滑鼠右鍵按一下 [瀏覽]** 底下的灰色空間，然後建立名為 **index.html** 的新檔案。

以下是您可以輸入 **index.html** 的程式碼。 您將需要更新下列項目：
* **clientId** - 以您的用戶端應用程式識別碼更新。 此識別碼會是您在提取權杖時所擷取的相同識別碼
* **authority** - 以您的 Azure AD 租用戶識別碼更新
* **FHIRendpoint** - 將 FHIRendpoint 更新為具有您的 FHIR 服務名稱
* **scopes** - 更新以反映對象的完整 URL

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

從這裡，您可以回到 Web 應用程式資源，然後開啟可在 [概觀] 頁面上找到的 URL。 登入以查看您先前建立的病患 James Tiberious Kirk。

## <a name="next-steps"></a>後續步驟
您已成功部署 Azure API for FHIR、註冊公用用戶端應用程式、測試存取權，並建立小型 Web 應用程式。 下一個步驟，請查看 Azure API for FHIR 支援的功能。

>[!div class="nextstepaction"]
>[支援的功能](fhir-features-supported.md)





