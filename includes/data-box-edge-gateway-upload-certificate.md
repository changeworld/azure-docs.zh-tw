---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448642"
---
正確的 SSL 憑證可確保向正確的伺服器發送加密資訊。 除了加密之外，證書還允許進行身份驗證。 您可以通過設備的 PowerShell 介面上傳您自己的可信 SSL 憑證。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`Cmdlet 上載證書。 當出現提示時，提供以下參數：

   - `CertificateFilePath`- 以 *.pfx*格式包含證書檔的共用的路徑。
   - `CertificatePassword`- 用於保護證書的密碼。
   - `Credentials`- 訪問包含證書的共用的使用者名。 提示時向網路共用提供密碼。

     下面的示例顯示了此 Cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

