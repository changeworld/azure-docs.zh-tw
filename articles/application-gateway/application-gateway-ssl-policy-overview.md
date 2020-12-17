---
title: Azure 應用程式閘道的 TLS 原則總覽
description: 瞭解如何設定 Azure 應用程式閘道的 TLS 原則，並減少後端伺服器陣列的加密和解密額外負荷。
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631685"
---
# <a name="application-gateway-tls-policy-overview"></a>應用程式閘道 TLS 原則總覽

您可以使用 Azure 應用程式閘道來集中進行 TLS/SSL 憑證管理，並減少後端伺服器陣列的加密和解密額外負荷。 此集中式 TLS 處理也可讓您指定適合您組織安全性需求的中央 TLS 原則。 這可協助您符合法規需求，以及安全性指導方針和建議的做法。

TLS 原則包含 TLS 通訊協定版本的控制以及加密套件，以及在 TLS 信號交換期間使用密碼的順序。 應用程式閘道提供兩種機制來控制 TLS 原則。 您可以使用預先定義的原則或自訂原則。

## <a name="predefined-tls-policy"></a>預先定義的 TLS 原則

應用程式閘道有三個預先定義的安全性原則。 您可以使用這些原則的任何一項來設定閘道，以取得適當的安全性層級。 原則名稱藉由它們設定時的年和月來標註。 每個原則都提供不同的 TLS 通訊協定版本和加密套件。 建議您使用最新的 TLS 原則，以確保最適合的 TLS 安全性。

## <a name="known-issue"></a>已知問題
應用程式閘道 v2 不支援下列 DHE 的密碼，而且它們不會用於與用戶端的 TLS 連線，即使它們已在預先定義的原則中提及也一樣。 建議您不要使用 DHE 加密，而是建議使用安全且更快速的 >ECDHE 密碼。

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

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
|預設| 否 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|屬性  |值  |
|---|---|
|名稱     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|預設| 否 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>自訂 TLS 原則

如果預先定義的 TLS 原則需要針對您的需求進行設定，您必須定義您自己的自訂 TLS 原則。 使用自訂的 TLS 原則，您可以完全掌控要支援的最低 TLS 通訊協定版本，以及支援的加密套件及其優先順序。

> [!IMPORTANT]
> 如果您在應用程式閘道 v1 SKU 中使用自訂 SSL 原則 (Standard 或 WAF) ，請確定您已將強制密碼 "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 新增至清單。 這是在應用程式閘道 v1 SKU 中啟用計量和記錄的必要密碼。
> 應用程式閘道 v2 SKU (Standard_v2 或 WAF_v2) 並不是必要的。
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL 通訊協定版本

* 依預設，所有應用程式閘道都停用 SSL 2.0 和 3.0。 這些通訊協定版本無法設定。
* 自訂的 TLS 原則可讓您選擇下列三種通訊協定中的任何一種，作為閘道的最小 TLS 通訊協定版本： TLSv1_0、TLSv1_1 和 TLSv1_2。
* 如果未定義任何 TLS 原則，則會啟用所有三種通訊協定 (TLSv1_0、TLSv1_1 和 TLSv1_2) 。

### <a name="cipher-suites"></a>加密套件

應用程式閘道支援下列加密套件，您可從套件中選擇自訂原則。 加密套件的順序會決定 TLS 協商期間的優先順序。


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
> 連接所使用的 TLS 加密套件也會根據所使用的憑證類型而定。 在用戶端對應用程式閘道連線中，使用的加密套件是以應用程式閘道接聽程式上的伺服器憑證類型為基礎。 在應用程式閘道中的後端集區連線中，使用的密碼套件是以後端集區伺服器上的伺服器憑證類型為基礎。

## <a name="next-steps"></a>下一步

如果您想要瞭解如何設定 TLS 原則，請參閱 [設定應用程式閘道上的 tls 原則版本和加密套件](application-gateway-configure-ssl-policy-powershell.md)。
