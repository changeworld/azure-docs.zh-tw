---
title: Azure Active Directory 憑證授權單位單位
description: Azure 中使用的受信任憑證清單
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88091fdf1bd39961ccf7a542aab3e7d2c3646e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172664"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory 使用的憑證授權單位單位

> [!IMPORTANT]
> 此頁面中的資訊僅與明確指定可接受的憑證授權單位單位清單的實體相關， (CAs) 。 除非沒有其他選項，否則應該避免這種作法，也就是所謂的憑證釘選。

嘗試透過 TLS/SSL 通訊協定來存取 Azure Active Directory (Azure AD) 身分識別服務的任何實體，都會顯示如下所列 Ca 的憑證。 如果實體信任這些 Ca，它可能會使用憑證來驗證身分識別和身分識別服務的合法性，並建立安全連線。

憑證授權單位單位可以分類為根 Ca 和中繼 Ca。 一般而言，根 Ca 會有一或多個相關聯的中繼 Ca。 本文列出 Azure AD 身分識別服務和與這些根目錄相關聯的中繼 Ca 所使用的根 Ca。 針對每個 CA，我們會將統一資源識別項包含 (Uri) 以下載相關聯的授權資訊存取 (AIA) ，以及 (CDP) 檔的憑證撤銷清單發佈點。 在適當的情況下，我們也會提供線上憑證狀態通訊協定 (OCSP) 端點的 URI。

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Azure 公用和 Azure 美國政府雲端中使用的 Ca

不同的服務可能會使用不同的根或中繼 Ca。 因此，以下列出的所有專案都是必要專案。

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| 根 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 2013 年 8 月 1日 <br>2038年1月15日| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [友邦 保險](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[Cdp](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>相關聯的中繼 Ca

| 發出和中繼 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - | 
| Microsoft Azure TLS 發行 CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 2020年7月29日<br>2024年6月27日| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [友邦 保險](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[Cdp](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS 發行 CA 02| 0c6ae97cced59983 8690a00a9ea53214| 2020年7月29日<br>2024年6月27日| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [友邦 保險](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[Cdp](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS 發行 CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 2020年7月29日<br>2024年6月27日| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [友邦 保險](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[Cdp](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS 發行 CA 06| 02e79171fb8021e93fe 2d983834c50c0| 2020年7月29日<br>2024年6月27日| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [友邦 保險](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[Cdp](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| 根 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 2000月12日<br>2025月12日| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[Cdp](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>相關聯的中繼 Ca

| 發出和中繼 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 2020 年 7 月 21 日<br>2024年10月8日| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [友邦 保險](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[Cdp](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 2020 年 7 月 21 日<br>2024年5月20日| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [友邦 保險](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[Cdp](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| 根 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 2006年11月9日<br>2031年11月9日| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [Cdp](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>相關聯的中繼 Ca

| 發出和中繼 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 2013年3月8日，2023年3月8日| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [友邦 保險](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[Cdp](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |2020 年 9 月 22 日<br>2030年9月22日|626d44e704d1ceabe3bf 0d53397464ac8080142c|[友邦 保險](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[Cdp](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Azure 中國世紀雲端中使用的 Ca

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| 根 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 11月9日，2006<br>11月9日，2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [Cdp](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>相關聯的中繼 CA

| 發出和中繼 CA| 序號| 問題日期到期日| SHA1 指紋| URI |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020年3月4日<br>2030年3月4日| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [友邦 保險](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[Cdp](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>後續步驟
[瞭解 Microsoft 365 加密鏈](/microsoft-365/compliance/encryption-office-365-certificate-chains?view=o365-worldwide)