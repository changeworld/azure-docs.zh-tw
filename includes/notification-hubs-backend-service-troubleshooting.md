---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095177"
---
#### <a name="no-response-from-the-backend-service"></a>沒有來自後端服務的回應

在本機測試時，請確定後端服務正在執行，並且使用正確的連接埠。

如果對 **Azure API 應用程式**進行測試，請檢查服務是否正在執行，以及是否已部署，且在沒有錯誤的情況下啟動。

透過用戶端進行測試時，請務必確認您已在 **[Postman](https://www.postman.com/downloads)** 或行動裝置應用程式設定中正確指定基底位址 (Base Address)。 在本機測試時，基底位址 (Base Address) 應是具有指示性的 `https://<api_name>.azurewebsites.net/` 或 `https://localhost:5001/`。

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>啟動或停止偵錯工作階段之後，未在 Android 上收到通知

請確定您在啟動或停止偵錯工作階段之後有再次註冊。 偵錯工具將會產生新的 **Firebase** 權杖。 通知中樞安裝也必須更新。

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>從後端服務收到 401 狀態碼

確定您已設定 **apikey** 要求標頭，且此值符合您為後端服務所設定的值。

如果您在本機測試時收到此錯誤，請確定您在用戶端設定中定義的金鑰值符合 [API](#create-the-api-app) 所使用的 **Authentication:ApiKey** 使用者設定值。

如果您要使用 **API 應用程式**進行測試，請確定用戶端設定檔中的金鑰值符合您在 [API 應用程式](#create-the-api-app)中使用的 **Authentication:ApiKey** 應用程式設定。

> [!NOTE]
> 如果您已在部署後端服務後建立或變更此設定，則必須重新啟動服務，才能使其生效。

如果您選擇不完成[使用 API 金鑰驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節，請確定您未將 **Authorize** 屬性套用至 **NotificationsController** 類別。

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>從後端服務接收 404 狀態碼

驗證端點和 HTTP 要求方法是否正確。 例如，端點應該是具有指示性的：

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

或在本機測試時：

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

在用戶端應用程式中指定基底位址 (Base Address) 時，請確定其結尾是 `/`。 在本機測試時，基底位址 (Base Address) 應是具有指示性的 `https://<api_name>.azurewebsites.net/` 或 `https://localhost:5001/`。

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>無法註冊，並顯示通知中樞錯誤訊息

確認測試裝置具有網路連線能力。 然後，藉由設定中斷點來檢查 **HttpResponse** 中的 **StatusCode** 屬性值，以判斷 Http 回應狀態碼。

根據狀態碼，查看先前的疑難排解建議。

針對個別的 API，在傳回這些特定狀態碼的行列上設定中斷點。 然後在本機進行偵錯時，嘗試呼叫後端服務。

使用適當的承載，透過 **[Postman](https://www.postman.com/downloads)** 驗證後端服務是否如預期般運作。 針對有問題的平台，請使用用戶端程式碼所建立的實際承載。

請參閱平台專屬的設定章節，以確保不會錯過任何步驟。 檢查是否已針對適當的平台，為 `installation id` 和 `token` 變數解析適當的值。

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>顯示無法解析裝置識別碼的錯誤訊息

請參閱平台專屬的設定章節，以確保不會錯過任何步驟。
