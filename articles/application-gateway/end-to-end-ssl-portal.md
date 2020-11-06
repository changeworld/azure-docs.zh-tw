---
title: 使用入口網站設定端對端 TLS 加密
titleSuffix: Azure Application Gateway
description: 瞭解如何使用 Azure 入口網站建立具有端對端 TLS 加密的應用程式閘道。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397496"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>使用應用程式閘道搭配入口網站來設定端對端 TLS

本文說明如何使用 Azure 入口網站，透過 (Gateway v1 SKU，設定端對端傳輸層安全性 (TLS) 加密，之前稱為安全通訊端層) SSL Azure 應用程式加密。

> [!NOTE]
> 應用程式閘道 v2 SKU 需要受信任的根憑證，才能啟用端對端設定。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

若要使用應用程式閘道設定端對端 TLS，您需要閘道的憑證。 後端伺服器也需要憑證。 閘道憑證用來衍生對稱金鑰，以符合 TLS 通訊協定規格。 接下來，對稱金鑰可以用來加密和解密傳送至閘道的流量。 

若要進行端對端 TLS 加密，應用程式閘道中必須允許正確的後端伺服器。 若要允許此存取，請將後端伺服器的公開憑證（也稱為驗證憑證 (v1) 或受信任的根憑證 (v2) ）上傳至應用程式閘道。 新增憑證可確保應用程式閘道只會與已知的後端實例通訊。 此設定可進一步保護端對端通訊。

若要深入瞭解，請參閱 [使用應用程式閘道的 tls 終止和端對端 Tls 總覽](./ssl-overview.md)。

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>使用端對端 TLS 建立新的應用程式閘道

若要建立具有端對端 TLS 加密的新應用程式閘道，您必須先啟用 TLS 終止，同時建立新的應用程式閘道。 此動作可讓用戶端與應用程式閘道之間的通訊進行 TLS 加密。 然後，您必須在 [安全收件者] 下，將後端伺服器的憑證列在 HTTP 設定中。 此設定可讓應用程式閘道與後端伺服器之間的通訊能夠進行 TLS 加密。 這會完成端對端 TLS 加密。

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>建立新的應用程式閘道時啟用 TLS 終止

若要深入瞭解，請參閱 [在建立新的應用程式閘道時啟用 TLS 終止](./create-ssl-portal.md)。

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>新增後端伺服器的驗證/根憑證

1. 選取 [所有資源]  ，然後選取 [myAppGateway]  。

2. 從左側功能表中選取 [ **HTTP 設定** ]。 當您建立應用程式閘道時，Azure 會自動建立預設的 HTTP 設定 **appGatewayBackendHttpSettings** 。 

3. 選取 [ **appGatewayBackendHttpSettings** ]。

4. 在 [ **通訊協定** ] 底下，選取 [ **HTTPS** ]。 **後端驗證憑證或受信任的根憑證** 的窗格隨即出現。

5. 選取 [建立新的]。

6. 在 [ **名稱** ] 欄位中，輸入適當的名稱。

7. 在 [ **上傳 CER 憑證** ] 方塊中選取憑證檔案。

   針對標準和 WAF (v1) 應用程式閘道，您應該將後端伺服器憑證的公開金鑰上傳 .cer 格式。

   ![新增憑證](./media/end-to-end-ssl-portal/addcert.png)

   針對 Standard_v2 和 WAF_v2 的應用程式閘道，您應該上傳 .cer 格式的後端伺服器憑證的根憑證。 如果後端憑證是由知名的憑證授權單位單位所簽發 (CA) ，您可以選取 [ **使用知名的 CA 憑證** ] 核取方塊，然後您不需要上傳憑證。

   ![新增受信任的根憑證](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![根憑證](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 選取 [儲存]。

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>為現有的應用程式閘道啟用端對端 TLS

若要使用端對端 TLS 加密來設定現有的應用程式閘道，您必須先在接聽程式中啟用 TLS 終止。 此動作可讓用戶端與應用程式閘道之間的通訊進行 TLS 加密。 然後，將這些憑證放在 [安全收件者] 清單中的 [HTTP 設定] 內的後端伺服器。 此設定可讓應用程式閘道與後端伺服器之間的通訊能夠進行 TLS 加密。 這會完成端對端 TLS 加密。

您必須使用具有 HTTPS 通訊協定的接聽程式和憑證來啟用 TLS 終止。 您可以使用符合這些條件的現有接聽程式，或建立新的接聽程式。 如果您選擇先前的選項，您可以忽略下列「在現有的應用程式閘道中啟用 TLS 終止」一節，直接移至「為後端伺服器新增驗證/受信任的根憑證」一節。

如果您選擇第二個選項，請套用下列程式中的步驟。
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>在現有的應用程式閘道中啟用 TLS 終止

1. 選取 [所有資源]  ，然後選取 [myAppGateway]  。

2. 從左側功能表 **中選取 [** 接聽程式]。

3. 根據您的需求，選取 [ **基本** ] 或 [ **多網站** ] 接聽程式。

4. 在 [ **通訊協定** ] 底下，選取 [ **HTTPS** ]。 [ **憑證** ] 窗格隨即出現。

5. 上傳您想要用於用戶端與應用程式閘道之間 TLS 終止的 PFX 憑證。

   > [!NOTE]
   > 基於測試目的，您可以使用自我簽署憑證。 不過，這不建議用於生產工作負載，因為它們較難管理且不是完全安全。 如需詳細資訊，請參閱 [建立自我簽署憑證](./create-ssl-portal.md#create-a-self-signed-certificate)。

6. 根據您的 **需求，新增接聽程式的其他** 必要設定。

7. 選取 [確定]  以儲存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>新增後端伺服器的驗證/受信任根憑證

1. 選取 [所有資源]  ，然後選取 [myAppGateway]  。

2. 從左側功能表中選取 [ **HTTP 設定** ]。 您可以在 [安全收件者] 清單中將憑證放在現有的後端 HTTP 設定中，或建立新的 HTTP 設定。  (在下一個步驟中，預設 HTTP 設定 **appGatewayBackendHttpSettings** 的憑證會新增至安全收件者清單。 ) 

3. 選取 [ **appGatewayBackendHttpSettings** ]。

4. 在 [ **通訊協定** ] 底下，選取 [ **HTTPS** ]。 **後端驗證憑證或受信任的根憑證** 的窗格隨即出現。 

5. 選取 [建立新的]。

6. 在 [ **名稱** ] 欄位中，輸入適當的名稱。

7. 在 [ **上傳 CER 憑證** ] 方塊中選取憑證檔案。

   針對標準和 WAF (v1) 應用程式閘道，您應該將後端伺服器憑證的公開金鑰上傳 .cer 格式。

   ![新增憑證](./media/end-to-end-ssl-portal/addcert.png)

   針對 Standard_v2 和 WAF_v2 的應用程式閘道，您應該上傳 .cer 格式的後端伺服器憑證的根憑證。 如果後端憑證是由知名的 CA 所發行，您可以選取 [ **使用已知的 Ca 憑證** ] 核取方塊，然後不需要上傳憑證。

   ![新增受信任的根憑證](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)