---
title: 使用自動調整傳送電子郵件和 Webhook 警示通知
description: 瞭解如何在 Azure 監視器中使用自動調整動作呼叫 web Url 或傳送電子郵件通知。
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009002"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>使用自動調整動作在 Azure 監視器中傳送電子郵件和 Webhook 警示通知
本文將告訴您如何設定觸發程序，讓您可以根據 Azure 中的自動調整動作呼叫特定的 Web URl 或傳送電子郵件。  

## <a name="webhooks"></a>Webhook
Webhook 可讓您將 Azure 警示通知路由到其他系統進行後處理或自訂通知。 例如，將警示路由傳送至可處理傳入 web 要求的服務，以傳送 SMS、記錄錯誤、使用聊天或訊息服務通知小組等。Webhook URI 必須是有效的 HTTP 或 HTTPS 端點。

## <a name="email"></a>電子郵件
電子郵件可以傳送至任何有效的電子郵件地址。 也會通知執行該規則的訂用帳戶的系統管理員和共同管理員。

## <a name="cloud-services-and-app-services"></a>雲端服務和應用程式服務
您可以從雲端服務和伺服器陣列的 Azure 入口網站中加入 (App Service) 。

* 選擇做為 [調整依據]  的度量。

![調整依據](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集
對於使用 Resource Manager 建立的較新虛擬機器 (虛擬機器擴展集)，您可以使用 REST API、Resource Manager 範本、PowerShell 和 CLI 設定此項目。 目前尚無入口網站介面。
使用 REST API 或 Resource Manager 範本時，請在 [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) 中包含 [通知] 元素，並提供下列選項。

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| 欄位 | 是否為強制？ | 描述 |
| --- | --- | --- |
| 作業 |是 |值必須是 [調整] |
| sendToSubscriptionAdministrator |是 |值必須是 "true" 或 "false" |
| sendToSubscriptionCoAdministrators |是 |值必須是 "true" 或 "false" |
| customEmails |是 |值可以是 null 或電子郵件的字串陣列 |
| Webhook |是 |值可以是 null 或有效的 Uri |
| serviceUri |是 |有效的 https Uri |
| properties |是 |值必須是空的 {}，也可以包含索引鍵-值組 |

## <a name="authentication-in-webhooks"></a>Webhook 中的驗證
Webhook 可以使用權杖型驗證來驗證，您會在其中儲存 Webhook URI 並以權杖識別碼做為查詢參數。 例如，HTTPs： \/ /mysamplealert/webcallback？ tokenid = sometokenid&someparameter = somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>自動調整通知 Webhook 承載結構描述
產生自動調整通知時，Webhook 承載會包含下列中繼資料︰

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| 欄位 | 是否為強制？ | 描述 |
| --- | --- | --- |
| status |是 |此狀態表示產生了自動調整動作 |
| 作業 |是 |若執行個體增加，它會「相應放大」，若執行個體減少，它會「相應縮小」。 |
| 內容 |是 |自動調整動作內容 |
| timestamp |是 |自動調整動作觸發時的時間戳記 |
| id |是 |自動調整設定的 Resource Manager 識別碼 |
| NAME |是 |自動調整設定的名稱 |
| 詳細資料 |是 |說明自動調整服務所採取的動作和執行個體計數的變更 |
| subscriptionId |是 |正在調整的目標資源的訂用帳戶識別碼 |
| resourceGroupName |是 |正在調整的目標資源的資源群組 |
| resourceName |是 |正在調整的目標資源的名稱 |
| resourceType |是 |支援三個值：microsoft.classiccompute/domainnames/slots/roles" (雲端服務角色)、"microsoft.compute/virtualmachinescalesets" (虛擬機器擴展集) 以及 "Microsoft.Web/serverfarms" - (Web 應用程式) |
| resourceId |是 |正在調整的目標資源的 Resource Manager 識別碼 |
| portalLink |是 |連到目標資源摘要頁面的 Azure 入口網站連結 |
| oldCapacity |是 |自動調整進行調整動作時的當前 (舊) 執行個體計數 |
| newCapacity |是 |自動調整要將資源調整為此數目的新執行個體計數 |
| properties |否 |選擇性。 <索引鍵, 值> 組 (例如，字典 <字串, 字串>)。 properties 欄位是選擇性的。 在自訂 UI 或邏輯應用程式的工作流程中，您可以輸入可使用承載傳遞的索引鍵和值。 另一個將自訂屬性傳回給連出 Webhook 呼叫的替代做法，是使用 Webhook URI 本身 (做為查詢參數) |
