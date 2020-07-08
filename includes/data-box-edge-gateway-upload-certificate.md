---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67448642"
---
適當的 SSL 憑證可確保您將加密的資訊傳送至正確的伺服器。 除了加密，憑證也允許進行驗證。 您可以透過裝置的 PowerShell 介面上傳您自己的受信任 SSL 憑證。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Set-HcsCertificate` Cmdlet 來上傳憑證。 出現提示時，請提供下列參數：

   - `CertificateFilePath`-包含 *.pfx*格式之憑證檔案的共用路徑。
   - `CertificatePassword`-用來保護憑證的密碼。
   - `Credentials`-Username，用以存取包含憑證的共用。 出現提示時，請提供網路共用的密碼。

     下列範例顯示此 Cmdlet 的使用方式：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

