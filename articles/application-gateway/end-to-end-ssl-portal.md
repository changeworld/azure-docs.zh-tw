---
title: 使用門戶配置端到端 TLS 加密
titleSuffix: Azure Application Gateway
description: 瞭解如何使用 Azure 門戶使用端到端 TLS 加密創建應用程式閘道。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133018"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>將應用程式閘道及閘戶一起使用來配置端到端 TLS

本文介紹如何使用 Azure 門戶通過 Azure 應用程式閘道 v1 SKU 配置端到端傳輸層安全 （TLS） 加密（以前稱為安全通訊端層 （SSL） 加密。

> [!NOTE]
> 應用程式閘道 v2 SKU 需要受信任的根憑證才能啟用端到端配置。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

要使用應用程式閘道配置端到端 TLS，需要閘道的證書。 後端伺服器也需要證書。 閘道證書用於根據 TLS 協定規範派生對稱金鑰。 然後，對稱金鑰用於加密和解密發送到閘道的流量。 

對於端到端 TLS 加密，必須在應用程式閘道中允許右後端伺服器。 要允許此訪問，請將後端伺服器的公共證書（也稱為身份驗證憑證 （v1） 或受信任的根憑證 （v2） 上載到應用程式閘道。 添加證書可確保應用程式閘道僅與已知的後端實例通信。 此配置進一步保護端到端通信。

要瞭解更多資訊，請參閱[使用應用程式閘道 的 TLS 終止和端到端 TLS 概述](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>使用端到端 TLS 創建新的應用程式閘道

要使用端到端 TLS 加密創建新的應用程式閘道，您需要首先啟用 TLS 終止，同時創建新的應用程式閘道。 此操作允許 TLS 加密，以便用戶端和應用程式閘道之間的通信。 然後，您需要在 HTTP 設置中將後端伺服器的證書放在安全收件者清單中。 此配置允許 TLS 加密，用於應用程式閘道和後端伺服器之間的通信。 這實現了端到端 TLS 加密。

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>在創建新應用程式閘道的同時啟用 TLS 終止

要瞭解更多資訊，請參閱[在創建新應用程式閘道時啟用 TLS 終止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>添加後端伺服器的身份驗證/根憑證

1. 選取 [所有資源]****，然後選取 [myAppGateway]****。

2. 從左側功能表中選擇**HTTP 設置**。 Azure 在創建應用程式閘道時自動創建了預設 HTTP 設置 **"門戶後端設置**"。 

3. 選擇**應用程式閘道後端Http設置**。

4. 在 **"協定**"下，選擇**HTTPS**。 將顯示**後端身份驗證憑證或受根信任證書**的窗格。

5. 選取 [建立新的]****。

6. 在 **"名稱"** 欄位中，輸入合適的名稱。

7. 在 **"上載 CER 證書"** 框中選擇證書檔。

   對於標準和 WAF （v1） 應用程式閘道，應以 .cer 格式上載後端伺服器憑證的公開金鑰。

   ![新增憑證](./media/end-to-end-ssl-portal/addcert.png)

   對於Standard_v2和WAF_v2應用程式閘道，應以 .cer 格式上載後端伺服器憑證的根憑證。 如果後端證書由知名憑證授權單位 （CA） 頒發，則可以選擇"**使用已知 CA 憑證**"核取方塊，然後無需上載證書。

   ![添加受信任的根憑證](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![根憑證](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 選取 [儲存]****。

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>為現有應用程式閘道啟用端到端 TLS

要使用端到端 TLS 加密配置現有應用程式閘道，必須首先在攔截器中啟用 TLS 終止。 此操作允許 TLS 加密，以便用戶端和應用程式閘道之間的通信。 然後，將後端伺服器的這些證書放在"安全收件者"清單中的 HTTP 設置中。 此配置允許 TLS 加密，用於應用程式閘道和後端伺服器之間的通信。 這實現了端到端 TLS 加密。

您需要使用具有 HTTPS 協定的攔截器和啟用 TLS 終止的證書。 您可以使用滿足這些條件的現有攔截器，也可以創建新攔截器。 如果選擇前一個選項，則可以忽略以下"在現有應用程式閘道中啟用 TLS 終止"部分，並直接移動到"為後端伺服器添加身份驗證/受信任的根憑證"部分。

如果選擇後一個選項，請應用以下步驟。
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>在現有應用程式閘道中啟用 TLS 終止

1. 選取 [所有資源]****，然後選取 [myAppGateway]****。

2. 從左側功能表中選擇**攔截器**。

3. 根據您的要求選擇 **"基本**"或 **"多網站**攔截器"。

4. 在 **"協定**"下，選擇**HTTPS**。 將顯示**證書**窗格。

5. 上傳要用於用戶端和應用程式閘道之間的 TLS 終止的 PFX 證書。

   > [!NOTE]
   > 出於測試目的，您可以使用自簽章憑證。 但是，對於生產工作負載，不建議這樣做，因為它們更難管理，而且並不完全安全。 有關詳細資訊，請參閱[創建自簽章憑證](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 根據您的要求為**攔截器**添加其他必需的設置。

7. 選取 [確定]**** 以儲存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>添加後端伺服器的身份驗證/受信任的根憑證

1. 選取 [所有資源]****，然後選取 [myAppGateway]****。

2. 從左側功能表中選擇**HTTP 設置**。 您可以將證書放在"安全收件者"清單的現有後端 HTTP 設置中，也可以創建新的 HTTP 設置。 （在下一步中，預設 HTTP 設置**AppGatewayBackendHttpSettings**的證書將添加到安全收件者清單中。

3. 選擇**應用程式閘道後端Http設置**。

4. 在 **"協定**"下，選擇**HTTPS**。 將顯示**後端身份驗證憑證或受根信任證書**的窗格。 

5. 選取 [建立新的]****。

6. 在 **"名稱"** 欄位中，輸入合適的名稱。

7. 在 **"上載 CER 證書"** 框中選擇證書檔。

   對於標準和 WAF （v1） 應用程式閘道，應以 .cer 格式上載後端伺服器憑證的公開金鑰。

   ![新增憑證](./media/end-to-end-ssl-portal/addcert.png)

   對於Standard_v2和WAF_v2應用程式閘道，應以 .cer 格式上載後端伺服器憑證的根憑證。 如果後端證書由知名 CA 頒發，則可以選擇"**使用已知 CA 憑證**"核取方塊，然後無需上載證書。

   ![添加受信任的根憑證](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 選取 [儲存]****。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)
