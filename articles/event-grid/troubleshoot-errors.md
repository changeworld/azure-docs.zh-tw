---
title: Azure 事件方格-疑難排解指南
description: 本文提供錯誤碼、錯誤訊息、描述和建議動作的清單。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ab52cea6ab43763cf2d9dc2b57b7f369072a399e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119033"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>針對 Azure 事件方格錯誤進行疑難排解
這份疑難排解指南提供 Azure 事件方格錯誤碼清單、錯誤訊息、其描述，以及您在收到這些錯誤時應採取的建議動作。 

## <a name="error-code-400"></a>錯誤碼︰400
| 錯誤碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | 主題名稱的長度必須介於3到50個字元之間。 | 自訂主題名稱長度應介於3到50個字元之間。 主題名稱只允許英數位元字母、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭： <ul><li>Microsoft</li><li>EventGrid</li><li>系統</li></ul> | 選擇遵守主題名稱需求的不同主題名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 功能變數名稱的長度必須介於3到50個字元之間。 | 功能變數名稱長度應該介於3到50個字元之間。 主題名稱只允許英數位元字母、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭：<ul><li>Microsoft</li><li>EventGrid</li><li>系統</li> | 選擇符合功能變數名稱需求的其他功能變數名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 不正確到期時間。 | 事件訂閱的到期時間會決定何時會淘汰事件訂用帳戶。 此值在未來應該是有效的日期時間值。| 請確定事件訂閱的到期時間是有效的日期時間格式，且設定為未來的日期。 |

## <a name="error-code-409"></a>錯誤碼：409
| 錯誤碼 | 錯誤訊息 | 說明 | 建議的動作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode。衝突 <br/>409 | 具有指定名稱的主題已經存在。 選擇不同的主題名稱。   | 自訂主題名稱在單一 Azure 區域中必須是唯一的，以確保正確的發佈作業。 相同的名稱可用於不同的 Azure 區域。 | 為主題選擇不同的名稱。 |
| HttpStatusCode。衝突 <br/> 409 | 具有指定的網域已經存在。 選擇不同的功能變數名稱。 | 功能變數名稱在單一 Azure 區域中必須是唯一的，以確保發佈作業正確無誤。 相同的名稱可用於不同的 Azure 區域。 | 為網域選擇不同的名稱。 |
| HttpStatusCode。衝突<br/>409 | 已達到配額限制。 如需這些限制的詳細資訊，請參閱 [Azure 事件方格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每個 Azure 訂用帳戶都有其可使用的 Azure 事件方格資源數目限制。 已超過部分或全部配額，因此無法建立其他資源。 |    檢查您目前的資源使用量，並刪除任何不需要的資源。 如果您仍然需要增加配額，請將電子郵件傳送到 [aeg@microsoft.com](mailto:aeg@microsoft.com) 所需的確切資源數目。 |

## <a name="error-code-403"></a>錯誤碼：403

| 錯誤碼 | 錯誤訊息 | 說明 | 建議的動作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode。禁止 <br/>403 | 由於 IpAddress 篩選規則的緣故，用戶端 {IpAddress} 發行至 {主題/網域} 遭到拒絕。 | 主題或網域已設定 IP 防火牆規則，且存取僅限於已設定的 IP 位址。 | 將 IP 位址新增至 IP 防火牆規則，請參閱 [設定 ip 防火牆](configure-firewall.md) |
| HttpStatusCode。禁止 <br/> 403 | 因為來自私人端點的要求，且找不到任何與資源相符的私人端點連線，所以拒絕用戶端發佈至 {主題/網域}。 | 主題或網域已設定私人端點，且發佈要求來自未設定/核准的私人端點。 | 設定主題/網域的私人端點。 [設定私人端點](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>針對事件訂閱驗證進行疑難排解

在事件訂閱建立期間，如果您看到錯誤訊息（例如 `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` ），則表示驗證交握失敗。 若要解決此錯誤，請執行以下幾方面的驗證：

- 使用 Postman 或捲曲或類似的工具，以 [範例 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 要求本文進行 HTTP POST 至 webhook url。
- 如果您的 webhook 正在執行同步驗證交握機制，請確認 ValidationCode 會作為回應的一部分傳回。
- 如果您的 webhook 正在執行非同步驗證交握機制，請確認您是 HTTP POST 傳回的是 200 OK。
- 如果您的 Webhook 在回應中傳回 403 (禁止)，請檢查您的 Webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後方。 如果是，則您需要停用這些防火牆規則，並再次執行 HTTP POST：

  920300 (要求中遺漏 Accept 標頭，我們可以修正此問題)

  942430 (受限制的 SQL 字元異常偵測 (args): 超出的特殊字元數目 (12))

  920230 (偵測到多個 URL 編碼)

  942130 (SQL 插入式攻擊:檢測到 SQL 恆真式。)

  931130 (可能的遠端檔案包含 (RFI) 攻擊 = 關閉網域參考/連結)


## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請將您的問題張貼在 [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
