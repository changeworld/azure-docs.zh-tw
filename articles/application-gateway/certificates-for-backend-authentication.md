---
title: 允許後端伺服器所需的憑證
titleSuffix: Azure Application Gateway
description: 本文提供的範例說明如何將 TLS/SSL 憑證轉換成驗證憑證，以及允許 Azure 應用程式閘道中的後端實例所需的受信任根憑證。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 125da04c9fafe33c7f3de3a5849e4238b3b45d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976535"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>建立憑證以允許後端使用 Azure 應用程式閘道

若要執行端對端 TLS，應用程式閘道必須藉由上傳驗證/受信任的根憑證來允許後端實例。 若為 v1 SKU，則需要驗證憑證，但是針對 v2 SKU，則需要有受信任的根憑證，才能允許憑證。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> - 從後端憑證匯出驗證憑證（適用于 v1 SKU）
> - 從後端憑證匯出受信任的根憑證（適用于 v2 SKU）

## <a name="prerequisites"></a>必要條件

必須要有現有的後端憑證，才能產生允許應用程式閘道的後端實例所需的驗證憑證或受信任的根憑證。 後端憑證可以與 TLS/SSL 憑證相同或不同，以提高安全性。 應用程式閘道不會提供任何機制來建立或購買 TLS/SSL 憑證。 基於測試目的，您可以建立自我簽署的憑證，但您不應該將它用於生產工作負載。 

## <a name="export-authentication-certificate-for-v1-sku"></a>匯出驗證憑證（適用于 v1 SKU）

需要驗證憑證，才能在應用程式閘道 v1 SKU 中允許後端實例。 驗證憑證是後端伺服器憑證的公開金鑰，以 Base-64 編碼的 x.509 （。CER）格式。 在此範例中，您將使用後端憑證的 TLS/SSL 憑證，並將其公開金鑰匯出為驗證認證。 此外，在此範例中，您將使用 Windows 憑證管理員工具來匯出所需的憑證。 您可以選擇使用任何其他便利的工具。

從您的 TLS/SSL 憑證匯出公用金鑰 .cer 檔案（而非私密金鑰）。 下列步驟可協助您匯出以64編碼的 x.509 （中的 .cer 檔案。CER）格式的憑證：

1. 若要取得憑證的 .cer 檔案，請開啟 [管理使用者憑證]****。 尋找憑證，通常位於 [憑證-目前的 User\Personal\Certificates] 中，然後按一下滑鼠右鍵。 按一下 [所有工作]****，然後按一下 [匯出]****。 這會開啟 [憑證匯出精靈] ****。 若您在 Current User\Personal\Certificates 下找不到憑證，您可能已意外開啟 [憑證 - 本機電腦]，而非 [憑證 - 目前使用者]。 若要使用 PowerShell 在目前使用者範圍開啟 [憑證管理員]，您必須在主控台視窗中輸入 *certmgr*。

   ![匯出](./media/certificates-for-backend-authentication/export.png)

2. 在精靈中按 [下一步]****。

   ![匯出憑證](./media/certificates-for-backend-authentication/exportwizard.png)

3. 選取 [否，不要匯出私密金鑰]****，然後按 [下一步]****。

   ![不要匯出私密金鑰](./media/certificates-for-backend-authentication/notprivatekey.png)

4. 在 [匯出檔案格式]**** 頁面上，選取 [Base-64 編碼 X.509 (.CER)]****，然後按 [下一步]****。

   ![Base-64 編碼](./media/certificates-for-backend-authentication/base64.png)

5. 針對 [要匯出的檔案]****，[瀏覽]**** 至您要匯出憑證的位置。 針對 [檔案名稱] ****，請為憑證檔案命名。 然後按 [下一步] ****。

   ![瀏覽](./media/certificates-for-backend-authentication/browse.png)

6. 按一下 [完成]**** 匯出憑證。

   ![[完成]](./media/certificates-for-backend-authentication/finish.png)

7. 已成功匯出您的憑證。

   ![成功](./media/certificates-for-backend-authentication/success.png)

   匯出的憑證如下所示：

   ![已匯出](./media/certificates-for-backend-authentication/exported.png)

8. 如果您使用「記事本」開啟匯出的憑證，您會看到類似於此範例的內容。 藍色區段包含上傳至應用程式閘道的資訊。 如果您使用 [記事本] 開啟憑證，但看起來不像這樣，這通常表示您未使用以64編碼的 x.509 （。CER）格式。 此外，如果您想要使用不同的文字編輯器，請了解某些編輯器可能會在背景中導入非預期的格式。 這可能會在此憑證中的文字上傳至 Azure 時產生問題。

   ![使用記事本開啟](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>匯出受信任的根憑證（適用于 v2 SKU）

需要受信任的根憑證，才能在應用程式閘道 v2 SKU 中允許後端實例。 根憑證是以64編碼的 x.509 （。CER）從後端伺服器憑證格式化根憑證。 在此範例中，我們將使用後端憑證的 TLS/SSL 憑證，匯出其公開金鑰，然後從 base64 編碼格式的公開金鑰匯出受信任 CA 的根憑證，以取得受信任的根憑證。 中繼憑證應與伺服器憑證配套，並安裝在後端伺服器上。

下列步驟可協助您匯出憑證的 .cer 檔案：

1. 使用上一節[匯出驗證憑證（針對 V1 SKU）](#export-authentication-certificate-for-v1-sku)中所述的步驟 1-8，從您的後端憑證匯出公開金鑰。

2. 匯出公開金鑰之後，請開啟檔案。

   ![開啟授權憑證](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![關於憑證](./media/certificates-for-backend-authentication/general.png)

3. 移至 [憑證路徑] 視圖以查看憑證授權單位單位。

   ![憑證詳細資料](./media/certificates-for-backend-authentication/certdetails.png)

4. 選取 [根憑證]，然後按一下 [**查看憑證**]。

   ![憑證路徑](./media/certificates-for-backend-authentication/rootcert.png)

   您應該會看到 [根憑證] 詳細資料。

   ![憑證資訊](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. 移至**詳細資料**視圖，然後按一下 [**複製到檔案 ...** ]

   ![複製根憑證](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 此時，您已從後端憑證解壓縮根憑證的詳細資料。 您會看到 [**憑證匯出嚮導]**。 現在使用上方的**從後端憑證匯出驗證憑證（適用于 V1 SKU）** 一節中所述的步驟2-9，匯出以 Base-64 編碼的 x.509 （中的受信任根憑證。CER）格式。

## <a name="next-steps"></a>後續步驟

現在您已擁有驗證憑證/受信任的根憑證，其以 Base-64 編碼的 x.509 （。CER）格式。 您可以將此新增至應用程式閘道，以允許您的後端伺服器進行端對端 TLS 加密。 請參閱[使用應用程式閘道搭配 PowerShell 來設定端對端 TLS](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。

