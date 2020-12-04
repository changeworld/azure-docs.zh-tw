---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581795"
---
適當的 SSL 憑證可確保您將加密的資訊傳送至正確的伺服器。 除了加密，憑證也允許進行驗證。 您可以透過裝置的 PowerShell 介面，上傳您自己的受信任 SSL 憑證。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Set-HcsCertificate` Cmdlet 來上傳憑證。 出現提示時，請提供下列參數：

   - `CertificateFilePath` -包含 *.pfx* 格式之憑證檔案的共用路徑。
   - `CertificatePassword` -用來保護憑證的密碼。
   - `Credentials` -用來存取包含憑證之共用的使用者名稱。 出現提示時，請提供網路共用密碼。

     下列範例顯示此 Cmdlet 的使用方式：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
