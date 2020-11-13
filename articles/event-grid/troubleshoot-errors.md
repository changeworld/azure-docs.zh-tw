---
title: Azure 事件方格-疑難排解指南
description: 本文提供錯誤碼、錯誤訊息、描述和建議動作的清單。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592986"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>針對 Azure 事件方格錯誤進行疑難排解
這份疑難排解指南提供下列資訊： 

- Azure 事件方格錯誤碼
- 錯誤訊息
- 錯誤的描述
- 當您收到這些錯誤時，應該採取的建議動作。 

## <a name="error-code-400"></a>錯誤碼︰400
| 錯誤碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | 主題名稱的長度必須介於3到50個字元之間。 | 自訂主題名稱長度應介於3到50個字元之間。 主題名稱只允許英數位元字母、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭： <ul><li>微軟</li><li>EventGrid</li><li>系統</li></ul> | 選擇遵守主題名稱需求的不同主題名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 功能變數名稱的長度必須介於3到50個字元之間。 | 功能變數名稱長度應該介於3到50個字元之間。 功能變數名稱中只允許英數位元字母、數位和 '-' 字元。 此外，名稱不應以下列保留字開頭：<ul><li>微軟</li><li>EventGrid</li><li>系統</li> | 選擇符合功能變數名稱需求的其他功能變數名稱。 |
| HttpStatusCode. BadRequest<br/>400 | 不正確到期時間。 | 事件訂閱的到期時間會決定何時會淘汰事件訂用帳戶。 此值在未來應該是有效的日期時間值。| 請確定事件訂閱的到期時間是有效的日期時間格式，且設定為未來的日期。 |

## <a name="error-code-409"></a>錯誤碼：409
| 錯誤碼 | 錯誤訊息 | 描述 | 建議的動作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode。衝突 <br/>409 | 具有指定名稱的主題已經存在。 選擇不同的主題名稱。   | 自訂主題名稱在單一 Azure 區域中必須是唯一的，以確保正確的發佈作業。 相同的名稱可用於不同的 Azure 區域。 | 為主題選擇不同的名稱。 |
| HttpStatusCode。衝突 <br/> 409 | 具有指定的網域已經存在。 選擇不同的功能變數名稱。 | 功能變數名稱在單一 Azure 區域中必須是唯一的，以確保發佈作業正確無誤。 相同的名稱可用於不同的 Azure 區域。 | 為網域選擇不同的名稱。 |
| HttpStatusCode。衝突<br/>409 | 已達到配額限制。 如需這些限制的詳細資訊，請參閱 [Azure 事件方格限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。  | 每個 Azure 訂用帳戶都有其可使用的 Azure 事件方格資源數目限制。 已超過部分或全部配額，因此無法建立其他資源。 |    檢查您目前的資源使用量，並刪除任何不需要的資源。 如果您仍然需要增加配額，請將電子郵件傳送到 [aeg@microsoft.com](mailto:aeg@microsoft.com) 所需的確切資源數目。 |

## <a name="error-code-403"></a>錯誤碼：403

| 錯誤碼 | 錯誤訊息 | 描述 | 建議的動作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode。禁止 <br/>403 | 由於 IpAddress 篩選規則的原因，用戶端 {IpAddress} 發行至 {主題/網域} 遭到拒絕。 | 主題或網域已設定 IP 防火牆規則，且存取僅限於已設定的 IP 位址。 | 將 IP 位址新增至 IP 防火牆規則，請參閱 [設定 ip 防火牆](configure-firewall.md) |
| HttpStatusCode。禁止 <br/> 403 | 因為來自私人端點的要求，且找不到任何與資源相符的私人端點連線，所以拒絕用戶端發佈至 {主題/網域}。 | 主題或網域具有私人端點，而發佈要求來自未設定或核准的私人端點。 | 設定主題/網域的私人端點。 [設定私人端點](configure-private-endpoints.md) |

此外，檢查您的 webhook 是否位於 Azure 應用程式閘道或 Web 應用程式防火牆後方。 如果是，請停用下列防火牆規則，並再次執行 HTTP POST：

- 920300 (要求遺漏 accept 標頭) 
- 942430 (受限制的 SQL 字元異常偵測 (args) ：超過 (12) # A5 的特殊字元數
- 920230 (偵測到多個 URL 編碼) 
- 942130 (SQL 插入式攻擊：偵測到 SQL tautology。 ) 
- 931130 (可能的遠端檔案包含 (RFI) 攻擊 = Off 網域參考/連結) 



## <a name="next-steps"></a>後續步驟
如果您需要更多協助，請將您的問題張貼在 [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或開啟[支援票證](https://azure.microsoft.com/support/options/)。 
