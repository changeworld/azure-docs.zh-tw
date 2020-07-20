---
title: 針對 Azure 中的應用程式閘道進行疑難排解 – ILB ASE | Microsoft Docs
description: 了解如何在 Azure 中使用內部負載平衡器搭配 App Service Environment，針對應用程式閘道進行疑難排解
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84628521"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>針對使用內部 Load Balancer 與 App Service 環境的應用程式閘道，不允許列出後端伺服器憑證

本文會針對下列問題進行疑難排解：當您在 Azure 中使用端對端 TLS 時，使用內部 Load Balancer （ILB）連同 App Service 環境（ASE）在後端建立應用程式閘道時，不允許列出憑證。

## <a name="symptoms"></a>徵狀

當您在後端使用 ILB 搭配 ASE 來建立應用程式閘道時，後端伺服器可能會變成狀況不良。 如果應用程式閘道的驗證憑證不符合在後端伺服器上設定的憑證，就會發生此問題。 請參閱下列案例作為範例：

**應用程式閘道組態：**

- **接聽程式：** 多網站
- **埠：** 443
- **主機名稱：** test.appgwtestase.com
- **SSL 憑證：** CN=test.appgwtestase.com
- **後端集區：** IP 位址或 FQDN
- **IP 位址：** 10.1.5.11
- **HTTP 設定：** HTTPS
- **埠：**：443
- **自訂探查：** 主機名稱 – test.appgwtestase.com
- **驗證憑證：** test.appgwtestase.com 的 .cer
- **後端健康情況：** 狀況不良–後端伺服器憑證不允許以應用程式閘道列出。

**ASE 組態：**

- **ILB IP：** 10.1.5.11
- **網域名稱：** appgwtestase.com
- **App Service：** test.appgwtestase.com
- **SSL 繫結：** SNI SSL – CN=test.appgwtestase.com

當您存取應用程式閘道時，您會因為後端伺服器狀況不良而收到下列錯誤訊息：

**502– Web 服務器作為閘道或 proxy 伺服器時收到不正確回應。**

## <a name="solution"></a>解決方案

當您未使用主機名稱來存取 HTTPS 網站時，後端伺服器會傳回預設網站上已設定的憑證，以防 SNI 已停用。 對於 ILB ASE，預設憑證來自 ILB 憑證。 如果沒有針對 ILB 設定的憑證，則憑證來自 ASE 應用程式憑證。

當您使用完整網域名稱 (FQDN) 來存取 ILB 時，後端伺服器會傳回在 HTTP 設定中上傳的正確憑證。 如果情況不是這樣，請考慮下列選項：

- 在應用程式閘道的後端集區中使用 FQDN，以指向 ILB 的 IP 位址。 此選項僅適用於您已設定私人 DNS 區域或自訂 DNS 時。 否則，您必須為公用 DNS 建立 "A" 記錄。

- 使用 ILB 上已上傳的憑證或 HTTP 設定中的預設憑證 (ILB 憑證)。 應用程式閘道會在存取 ILB 的 IP 以供探查時取得憑證。

- 在 ILB 和後端伺服器上使用萬用字元憑證，這樣該憑證對所有網站就會是通用的。 不過，此解決方案只適用於子網域的情況，如果每個網站需要不同的主機名稱就不適用。

- 將應用程式閘道的 [為應用程式服務使用]**** 選項取消選取，以免您是使用 ILB 的 IP 位址。

若要減少額外負荷，您可以上傳 HTTP 設定中的 ILB 憑證，以進行探查路徑工作。 （此步驟僅適用于允許清單。 它不會用於 TLS 通訊）。您可以透過在 HTTPS 上瀏覽器中的 IP 位址存取 ILB，然後以 Base-64 編碼的 CER 格式匯出 TLS/SSL 憑證，並上傳個別 HTTP 設定上的憑證，藉此取得 ILB 憑證。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
