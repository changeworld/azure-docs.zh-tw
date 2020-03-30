---
title: Azure 應用程式閘道的 TLS 策略概述
description: 瞭解如何為 Azure 應用程式閘道配置 TLS 策略，以及減少後端伺服器場的加密和解密開銷。
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257371"
---
# <a name="application-gateway-tls-policy-overview"></a>應用程式閘道 TLS 策略概述

您可以使用 Azure 應用程式閘道集中 TLS/SSL 憑證管理，並減少後端伺服器場的加密和解密開銷。 此集中式 TLS 處理還允許您指定適合您的組織安全要求的中央 TLS 策略。 這可協助您符合法規需求，以及安全性指導方針和建議的做法。

TLS 策略包括 TLS 協定版本的控制以及密碼套件以及 TLS 握手期間使用密碼的順序。 應用程式閘道提供了兩種用於控制 TLS 策略的機制。 您可以使用預先定義的原則或自訂原則。

## <a name="predefined-tls-policy"></a>預定義的 TLS 策略

應用程式閘道有三個預先定義的安全性原則。 您可以使用這些原則的任何一項來設定閘道，以取得適當的安全性層級。 原則名稱藉由它們設定時的年和月來標註。 每個策略都提供不同的 TLS 協定版本和密碼套件。 我們建議您使用最新的 TLS 策略來確保最佳的 TLS 安全性。

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|屬性  |值  |
|---|---|
|名稱     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|預設| True (如果未定義任何預先定義原則) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|屬性  |值  |
|   ---      |  ---       |
|名稱     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|預設| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|屬性  |值  |
|---|---|
|名稱     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|預設| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>自訂 TLS 策略

如果需要根據您的要求配置預定義的 TLS 策略，則必須定義自己的自訂 TLS 策略。 使用自訂 TLS 策略，您可以完全控制要支援的最低 TLS 協定版本，以及支援的密碼套件及其優先順序順序。
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL 協定版本

* 依預設，所有應用程式閘道都停用 SSL 2.0 和 3.0。 這些通訊協定版本無法設定。
* 自訂 TLS 策略允許您選擇以下三個協定中的任何一個作為閘道的最低 TLS 協定版本：TLSv1_0、TLSv1_1 和TLSv1_2。
* 如果未定義 TLS 策略，則啟用所有三個協定（TLSv1_0、TLSv1_1和TLSv1_2）。

### <a name="cipher-suites"></a>加密套件

應用程式閘道支援下列加密套件，您可從套件中選擇自訂原則。 密碼套件的順序決定了 TLS 協商期間的優先順序順序。


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> 用於連接的 TLS 密碼套件也基於所使用的證書類型。 在用戶端到應用程式閘道連接中，使用的密碼套件基於應用程式閘道攔截器上的伺服器憑證類型。 在後端池連接的應用程式閘道中，使用的密碼套件基於後端池伺服器上的伺服器憑證類型。

## <a name="known-issue"></a>已知問題
應用程式閘道 v2 當前不支援以下密碼：
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>後續步驟

如果要瞭解如何配置 TLS 策略，請參閱[在應用程式閘道上配置 TLS 策略版本和密碼套件](application-gateway-configure-ssl-policy-powershell.md)。
