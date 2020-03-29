---
title: 疑難排解 Azure 流量管理員上的已降級狀態
description: 如何在流量管理員顯示為降級狀態時疑難排解流量管理員設定檔。
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938374"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>疑難排解 Azure 流量管理員上的已降級狀態

本文說明如何針對顯示降級狀態的 Azure 流量管理員設定檔進行疑難排解。 作為排除 Azure 流量管理器降級狀態的第一步是啟用診斷日誌記錄。  有關詳細資訊，請參閱[啟用診斷日誌](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs)。 在此案例中，假設您已設定流量管理員設定檔來指向您的一些 cloudapp.net 託管服務。 如果流量管理員的健康情況顯示 [降級]**** 狀態，則可能有一或多個端點的狀態是 [降級]****：

![降級端點狀態](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

如果流量管理員的健康情況顯示 [非使用中]**** 狀態，則可能有一或多個端點的狀態是 [非使用中]****：

![非使用中流量管理員狀態](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>了解流量管理員探查

* 只有當探查收到探查路徑傳回 HTTP 200 回應時，流量管理員才會將端點視為在「線上」。 如果應用程式返回任何其他 HTTP 回應代碼，則應將該回應代碼添加到流量管理器設定檔[的預期狀態碼範圍](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring)。
* 30x 重定向回應被視為失敗，除非您在流量管理器設定檔[的預期狀態碼範圍內](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring)將其指定為有效回應代碼。 流量管理器不探測重定向目標。
* 若為 HTTP 探查，會忽略憑證錯誤。
* 只要傳回 200，探查路徑的實際內容並不重要。 探查靜態內容 (例如 "/favicon.ico") 的 URL 是常用的技巧。 即使應用程式狀況良好，動態內容 (例如 ASP 頁面) 也不一定會傳回 200。
* 最佳做法是將探測路徑設置為具有足夠邏輯以確定網站是向上還是向下的東西。 在上述範例中，您將路徑設為 "favicon.ico"，只是測試 w3wp.exe 是否有回應。 此探查可能不會指出您的 Web 應用程式狀況良好。 較好的選擇是將路徑設為 "/Probe.aspx" 之類的項目，它具有邏輯可判斷網站的健康狀態。 例如，您可以使用效能計數器來監視 CPU 使用率，或測量失敗的要求數。 或者，您可以嘗試存取資料庫資源或工作階段狀態，以確定 Web 應用程式正在運作。
* 如果設定檔中的所有端點都已降級，流量管理員會將所有端點視為狀況良好，並將流量路由傳送至所有端點。 此行為可確保探查機制的問題不會造成您的服務完全中斷。

## <a name="troubleshooting"></a>疑難排解

若要針對探查失敗進行疑難排解，您需要工具來顯示從探查 URL 傳回的 HTTP 狀態碼。 有許多工具可顯示原始 HTTP 回應。

* [Fiddler](https://www.telerik.com/fiddler)
* [捲曲](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

您也可以在 Internet Explorer 中，使用 [F12 偵錯工具] 的 [網路] 索引標籤來檢視 HTTP 回應。

對於此示例，我們希望查看來自探測 URL 的回應：HTTP：\//watestsdp2008r2.cloudapp.net:80/Probe。 下列 PowerShell 範例說明問題。

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

範例輸出︰

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

請注意，我們收到重新導向回應。 如先前所述，200 以外的任何 StatusCode 都視為失敗。 流量管理員將端點狀態變更為「離線」。 若要解決此問題，請檢查網站設定，確保可以從探查路徑傳回適當的 StatusCode。 重新設定流量管理員探查，以指向傳回 200 的路徑。

如果您的探查使用 HTTPS 通訊協定，您可能需要停用憑證檢查，以避免在測試期間發生 SSL/TLS 錯誤。 下列 PowerShell 陳述式會停用目前 PowerShell 工作階段的憑證驗證︰

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>後續步驟

[關於流量管理員流量路由方法](traffic-manager-routing-methods.md)

[什麼是流量管理器](traffic-manager-overview.md)

[雲端服務](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[流量管理員的相關作業 (REST API 參考)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
