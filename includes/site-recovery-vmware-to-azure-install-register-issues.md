---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73747828"
---
### <a name="installation-failures"></a>安裝失敗
| **範例錯誤訊息** | **建議的動作** |
|--------------------------|------------------------|
|ERROR   無法載入帳戶。 錯誤︰System.IO.IOException︰無法在安裝和註冊 CS 伺服器時從傳輸連線讀取資料。| 確定電腦上已啟用 TLS 1.0。 |

### <a name="registration-failures"></a>註冊失敗
您可以藉由檢查 **%ProgramData%\ASRLogs**資料夾中的記錄，來調試註冊失敗。

| **範例錯誤訊息** | **建議的動作** |
|--------------------------|------------------------|
|**09:20:06**：InnerException.Type: SrsRestApiClientLib.AcsException,InnerException。<br>訊息︰ACS50008：SAML 權杖無效。<br>追蹤識別碼︰1921ea5b-4723-4be7-8087-a75d3f9e1072<br>相互關聯識別碼︰62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>時間戳記︰**2016-12-12 14:50:08Z<br>** | 請確保系統時鐘上的時間與本地時間的偏差未超過 15 分鐘。 重新執行安裝程式以完成註冊。|
|**09:35:27**：嘗試取得所選憑證的所有災害復原保存庫時發生 DRRegistrationException：擲回 Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException，Exception.Message：ACS50008：SAML 權杖無效。<br>追蹤識別碼︰e5ad1af1-2d39-4970-8eef-096e325c9950<br>相互關聯識別碼︰abe9deb8-3e64-464d-8375-36db9816427a<br>時間戳記︰**2016-05-19 01:35:39Z**<br> | 請確保系統時鐘上的時間與本地時間的偏差未超過 15 分鐘。 重新執行安裝程式以完成註冊。|
|06:28:45︰無法建立憑證<br>06:28:45：安裝程式無法繼續。 無法建立驗證 Site Recovery 所需的憑證。 重新執行安裝程式 | 請確定您是以本機系統管理員身分執行安裝程式。 |
