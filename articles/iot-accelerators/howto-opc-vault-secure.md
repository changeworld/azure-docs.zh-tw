---
title: 使用 OPC 保存庫保護 OPC UA 裝置的通訊-Azure |Microsoft Docs
description: 如何註冊 OPC UA 應用程式，以及如何使用 OPC 保存庫為您的 OPC UA 裝置發出已簽署的應用程式憑證。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926405"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>使用 OPC 保存庫憑證管理服務

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

本文說明如何註冊應用程式，以及如何為您的 OPC UA 裝置發出已簽署的應用程式憑證。

## <a name="prerequisites"></a>先決條件

### <a name="deploy-the-certificate-management-service"></a>部署憑證管理服務

首先，將服務部署至 Azure 雲端。 如需詳細資訊，請參閱 [部署 OPC 保存庫憑證管理服務](howto-opc-vault-deploy.md)。

### <a name="create-the-issuer-ca-certificate"></a>建立簽發者 CA 憑證

如果您還沒有這麼做，請建立簽發者 CA 憑證。 如需詳細資訊，請參閱 [建立和管理 OPC 保存庫的簽發者憑證](howto-opc-vault-manage.md)。

## <a name="secure-opc-ua-applications"></a>保護 OPC UA 應用程式

### <a name="step-1-register-your-opc-ua-application"></a>步驟1：註冊您的 OPC UA 應用程式 

> [!IMPORTANT]
> 需要寫入器角色才能註冊應用程式。

1. 開啟您的憑證服務 `https://myResourceGroup-app.azurewebsites.net` ，然後登入。
2. 移至 [ **註冊新** 的]。 若要註冊應用程式，使用者至少需要指派寫入器角色。
2. 專案表單遵循 OPC UA 中的命名慣例。 例如，在下列螢幕擷取畫面中，會顯示 OPC UA .NET Standard 堆疊中 [OPC Ua 參考伺服器](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) 範例的設定：

   ![UA 參考伺服器註冊的螢幕擷取畫面](media/howto-opc-vault-secure/reference-server-registration.png "UA 參考伺服器註冊")

5. 選取 [ **註冊** ]，在憑證服務應用程式資料庫中註冊應用程式。 工作流程會直接引導使用者進行下一個步驟，以要求應用程式的簽署憑證。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>步驟2：使用 CA 簽署的應用程式憑證來保護您的應用程式

根據 (CSR) 的憑證簽署要求發出簽署的憑證，以保護您的 OPC UA 應用程式。 或者，您可以要求新的金鑰組，其中包含 PFX 或 PEM 格式的新私密金鑰。 如需應用程式所支援之方法的詳細資訊，請參閱 OPC UA 裝置的檔。 一般情況下，建議使用 CSR 方法，因為它不需要透過網路傳送私密金鑰。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>使用新的金鑰組要求新憑證

1. 移至 [ **應用程式** ]。
3. 針對列出的應用程式選取 **新的要求** 。

   ![要求新憑證的螢幕擷取畫面](media/howto-opc-vault-secure/request-new-certificate.png "要求新憑證")

3. 選取 [ **要求新的金鑰組和憑證** ]，以使用您應用程式的公開金鑰要求私密金鑰和新的已簽署憑證。

   ![產生新的金鑰組和憑證的螢幕擷取畫面](media/howto-opc-vault-secure/generate-new-key-pair.png "產生新的金鑰組")

4. 在表單中填入主旨和功能變數名稱。 針對私密金鑰，選擇 [PEM] 或 [具有密碼的 PFX]。 選取 [ **產生新的金鑰** 組] 來建立憑證要求。

   ![顯示 [查看憑證要求詳細資料] 畫面和 [產生新的金鑰組] 按鈕的螢幕擷取畫面。](media/howto-opc-vault-secure/approve-reject.png "核准憑證")

5. 核准需要具有核准者角色的使用者，以及在 Azure Key Vault 中具有簽署許可權。 在一般工作流程中，核准者和要求者角色應指派給不同的使用者。 選取 [ **核准** ] 或 [ **拒絕** ] 以啟動或取消實際建立金鑰組和簽署作業。 新的金鑰組會安全地建立並儲存在 Azure Key Vault 中，直到憑證要求者下載為止。 使用公開金鑰產生的憑證是由 CA 簽署。 這些作業可能需要幾秒鐘的時間才能完成。

   ![查看憑證要求詳細資料的螢幕擷取畫面，並在底部核准訊息](media/howto-opc-vault-secure/view-key-pair.png "查看金鑰組")

7. 您可以從這裡下載所產生的私密金鑰 (PFX 或 PEM) 和憑證 (DER) ，格式為二進位檔下載。 例如，您也可以使用 base64 編碼版本，將憑證複製並貼到命令列或文字專案。 
8. 當私密金鑰下載並安全地儲存之後，您可以選取 [ **刪除私密金鑰** ]。 具有公開金鑰的憑證仍可供日後使用。
9. 由於使用 CA 簽署的憑證，因此也應該在這裡下載 CA 憑證和憑證撤銷清單 (CRL) 。

現在它依存于 OPC UA 裝置如何套用新的金鑰組。 CA 憑證和 CRL 通常會複製到 `trusted` 資料夾，而應用程式憑證的公開金鑰和私密金鑰則會套用至 `own` 憑證存放區中的資料夾。 某些裝置可能已支援伺服器推播進行憑證更新。 請參閱 OPC UA 裝置的檔。

#### <a name="request-a-new-certificate-with-a-csr"></a>使用 CSR 要求新憑證 

1. 移至 [ **應用程式** ]。
3. 針對列出的應用程式選取 **新的要求** 。

   ![要求新憑證的螢幕擷取畫面](media/howto-opc-vault-secure/request-new-certificate.png "要求新憑證")

3. 選取 [ **要求具有簽署要求的新憑證** ]，為您的應用程式要求新的已簽署憑證。

   ![產生新憑證的螢幕擷取畫面](media/howto-opc-vault-secure/generate-new-certificate.png "產生新的憑證")

4. 選取本機檔案或在表單中貼上 base64 編碼的 CSR，以上傳 CSR。 選取 [ **產生新憑證** ]。

   ![查看憑證要求詳細資料的螢幕擷取畫面](media/howto-opc-vault-secure/approve-reject-csr.png "核准 CSR")

5. 核准需要具有核准者角色的使用者，以及在 Azure Key Vault 中具有簽署許可權。 選取 [ **核准** ] 或 [ **拒絕** ]，以啟動或取消實際的簽署操作。 使用公開金鑰產生的憑證是由 CA 簽署。 此作業可能需要幾秒鐘的時間才能完成。

   ![顯示 View Certificate 要求詳細資料的螢幕擷取畫面，並在底部包含核准訊息。](media/howto-opc-vault-secure/view-cert-csr.png "檢視憑證")

6. 您可以從這裡將產生的憑證 (DER) 下載為二進位檔案。 例如，您也可以使用 base64 編碼版本，將憑證複製並貼到命令列或文字專案。 
10. 憑證下載並安全地儲存之後，您可以選取 [ **刪除憑證** ]。
11. 由於使用 CA 簽署的憑證，因此也應該在這裡下載 CA 憑證和 CRL。

現在它依存于 OPC UA 裝置如何套用新的憑證。 CA 憑證和 CRL 通常會複製到 `trusted` 資料夾，而應用程式憑證則會套用至 `own` 憑證存放區中的資料夾。 某些裝置可能已支援伺服器推播進行憑證更新。 請參閱 OPC UA 裝置的檔。

### <a name="step-3-device-secured"></a>步驟3：裝置安全

OPC UA 裝置現在已準備好與其他受 CA 簽署憑證保護的 OPC UA 裝置進行通訊，而不需要進一步設定。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何保護 OPC UA 裝置，您可以：

> [!div class="nextstepaction"]
> [執行安全的憑證管理服務](howto-opc-vault-secure-ca.md)
