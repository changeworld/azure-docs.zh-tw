---
title: 允許後端伺服器所需的證書
titleSuffix: Azure Application Gateway
description: 本文提供了如何將 TLS/SSL 憑證轉換為身份驗證憑證和允許 Azure 應用程式閘道中的後端實例所需的可信根憑證的示例
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133038"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>創建證書以允許使用 Azure 應用程式閘道後端

為了端到端 TLS，應用程式閘道要求通過上載身份驗證/受信任的根憑證來允許後端實例。 對於 v1 SKU，需要身份驗證憑證，但對於 v2 SKU 受信任的根憑證，需要允許這些證書。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> - 從後端證書匯出身份驗證憑證（對於 v1 SKU）
> - 從後端證書匯出受信任的根憑證（對於 v2 SKU）

## <a name="prerequisites"></a>Prerequisites

需要現有的後端證書來生成允許具有應用程式閘道的後端實例所需的身份驗證憑證或受信任的根憑證。 後端證書可以與 TLS/SSL 憑證相同，也可以不同，以增加安全性。 應用程式閘道不為您提供任何創建或購買 TLS/SSL 憑證的機制。 出於測試目的，您可以創建自簽章憑證，但不應將其用於生產工作負載。 

## <a name="export-authentication-certificate-for-v1-sku"></a>匯出身份驗證憑證（用於 v1 SKU）

需要身份驗證憑證才能在應用程式閘道 v1 SKU 中允許後端實例。 身份驗證憑證是 Base-64 編碼 X.509（）中的後端伺服器憑證的公開金鑰。CER） 格式。 在此示例中，您將使用後端證書的 TLS/SSL 憑證，並匯出其公開金鑰以用作身份驗證認證。 此外，在此示例中，您將使用 Windows 證書管理器工具匯出所需的證書。 您可以選擇使用任何其他方便的工具。

從 TLS/SSL 憑證匯出公開金鑰 .cer 檔（不是私密金鑰）。 以下步驟可説明您匯出 Base-64 編碼 X.509 中的 .cer 檔。證書的 CER） 格式：

1. 若要取得憑證的 .cer 檔案，請開啟 [管理使用者憑證]****。 找到證書，通常在"證書 - 當前使用者\個人_證書"中，然後按右鍵。 按一下 [所有工作]****，然後按一下 [匯出]****。 這會開啟 [憑證匯出精靈] ****。 若您在 Current User\Personal\Certificates 下找不到憑證，您可能已意外開啟 [憑證 - 本機電腦]，而非 [憑證 - 目前使用者]。 若要使用 PowerShell 在目前使用者範圍開啟 [憑證管理員]，您必須在主控台視窗中輸入 *certmgr*。

   ![匯出](./media/certificates-for-backend-authentication/export.png)

2. 在精靈中按 [下一步]****。

   ![匯出憑證](./media/certificates-for-backend-authentication/exportwizard.png)

3. 選取 [否，不要匯出私密金鑰]****，然後按 [下一步]****。

   ![不要匯出私密金鑰](./media/certificates-for-backend-authentication/notprivatekey.png)

4. 在 [匯出檔案格式]**** 頁面上，選取 [Base-64 編碼 X.509 (.CER)]****，然後按 [下一步]****。

   ![Base-64 編碼](./media/certificates-for-backend-authentication/base64.png)

5. 針對 [要匯出的檔案]****，[瀏覽]**** 至您要匯出憑證的位置。 針對 [檔案名稱] ****，請為憑證檔案命名。 然後按 [下一步]****。

   ![瀏覽](./media/certificates-for-backend-authentication/browse.png)

6. 按一下 [完成]**** 匯出憑證。

   ![[完成]](./media/certificates-for-backend-authentication/finish.png)

7. 已成功匯出您的憑證。

   ![Success](./media/certificates-for-backend-authentication/success.png)

   匯出的憑證如下所示：

   ![已匯出](./media/certificates-for-backend-authentication/exported.png)

8. 如果您使用「記事本」開啟匯出的憑證，您會看到類似於此範例的內容。 藍色部分包含上載到應用程式閘道的資訊。 如果使用記事本打開證書，它看起來與此類似，則通常這意味著您沒有使用 Base-64 編碼的 X.509（匯出證書。CER） 格式。 此外，如果您想要使用不同的文字編輯器，請了解某些編輯器可能會在背景中導入非預期的格式。 這可能會在此憑證中的文字上傳至 Azure 時產生問題。

   ![使用記事本開啟](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>匯出受信任的根憑證（用於 v2 SKU）

在應用程式閘道 v2 SKU 中，需要受信任的根憑證來白名單後端實例。 根憑證是 Base-64 編碼的 X.509（。CER） 從後端伺服器憑證格式化根憑證。 在此示例中，我們將使用 TLS/SSL 憑證進行後端證書，匯出其公開金鑰，然後從 base64 編碼格式的公開金鑰匯出受信任的 CA 的根憑證，以獲得受信任的根憑證。 中繼憑證應與伺服器憑證捆綁在一起，並安裝在後端伺服器上。

以下步驟可説明您匯出證書的 .cer 檔：

1. 使用上述**後端證書（對於 v1 SKU）的匯出身份驗證憑證**部分中提到的步驟 1-9 從後端證書匯出公開金鑰。

2. 匯出公開金鑰後，打開該檔。

   ![打開授權證書](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![關於證書](./media/certificates-for-backend-authentication/general.png)

3. 移動到"認證路徑"視圖以查看憑證授權單位。

   ![證書詳細資訊](./media/certificates-for-backend-authentication/certdetails.png)

4. 選擇根憑證並按一下 **"查看證書**"。

   ![憑證路徑](./media/certificates-for-backend-authentication/rootcert.png)

   您應該看到根憑證詳細資訊。

   ![證書資訊](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. 移動到 **"詳細資訊"** 視圖，然後按一下"**複製到檔..."。**

   ![複製根憑證](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 此時，您從後端證書中提取了根憑證的詳細資訊。 您將看到**證書匯出嚮導**。 現在使用上面的**後端證書（對於 v1 SKU）的匯出身份驗證憑證**部分中提到的步驟 2-9 來匯出 Base-64 編碼 X.509（中的受根信任證書。CER） 格式。

## <a name="next-steps"></a>後續步驟

現在，Base-64 編碼 X.509（中具有身份驗證憑證/受信任的根憑證。CER） 格式。 您可以將此添加到應用程式閘道，以將後端伺服器列入端到端 TLS 加密的白名單。 請參閱[使用與 PowerShell 一起使用應用程式閘道來配置端到端 TLS。](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)

