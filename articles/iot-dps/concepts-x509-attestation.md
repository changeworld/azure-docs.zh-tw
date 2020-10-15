---
title: Azure IoT 中樞裝置布建服務-x.509 憑證證明
description: 說明使用 x.509 憑證證明搭配裝置布建服務 (DPS) 和 IoT 中樞的特定概念
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3e06c79b9cbd5643d119974a4ed8628ea1b1cd4f
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096754"
---
# <a name="x509-certificate-attestation"></a>X.509 憑證證明

本文概述使用 x.509 憑證證明布建裝置時所涉及的裝置布建服務 (DPS) 相關概念。 本文與為裝置進行部署準備工作的所有角色相關。

X.509 憑證可以存放在硬體安全性模組 HSM 中。

> [!TIP]
> 強烈建議您在生產環境中的裝置上，使用 HSM 搭配裝置安全地儲存秘密，例如 x.509 憑證。


## <a name="x509-certificates"></a>X.509 憑證

使用 X.509 憑證作為證明機制是調整生產環境並簡化裝置佈建的絕佳方式。 X.509 憑證通常會排列在信任鏈結中，其中每個憑證是由下一個較高憑證的私密金鑰簽署，依此類推，於自我簽署的根憑證終止。 這種方式會從受信任根憑證授權單位 (CA) 產生的根憑證，透過每個中繼 CA 將委派的信任鏈結建立至裝置上安裝之終端實體「分葉」憑證。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](/azure/iot-hub/iot-hub-x509ca-overview)。 

通常憑證鏈結代表與裝置相關聯的某些邏輯或實體階層。 例如，製造商可能：
- 發出自我簽署根 CA 憑證
- 使用根憑證來產生每個處理站的唯一中繼 CA 憑證
- 使用每個處理站的憑證來產生工廠中每個生產線的唯一中繼 CA 憑證
- 最後使用生產線憑證來產生該生產線上所製造之每個裝置的唯一裝置 (終端實體) 憑證。 

若要深入了解，請參閱[概念性了解 IoT 產業中的 X.509 CA 憑證](/azure/iot-hub/iot-hub-x509ca-concept)。 

### <a name="root-certificate"></a>根憑證

根憑證是自我簽署 X.509 憑證，代表憑證授權單位 (CA)。 它是憑證鏈結的終點或信任錨點。 根憑證可以由組織自我發行，或是向根憑證授權單位購買。 若要深入了解，請參閱[取得 X.509 CA 憑證](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)。 根憑證也可以稱為根 CA 憑證。

### <a name="intermediate-certificate"></a>中繼憑證

中繼憑證是根憑證 (或其鏈結中具有根憑證的另一個中繼憑證) 簽署的一種 X.509 憑證。 鏈結中的最後一個中繼憑證會用來簽署分葉憑證。 中繼憑證也可以稱為中繼 CA 憑證。

##### <a name="why-are-intermediate-certs-useful"></a>為什麼中繼憑證很有用？
中繼憑證的使用方式有許多種。 例如，中繼憑證可以用來依產品線、購買裝置、公司部門或工廠的客戶來分組裝置。 

假設 Contoso 是一家大型公司，其專屬的公開金鑰基礎結構 (PKI) 使用名為 *ContosoRootCert*的根憑證。 Contoso 的每個子公司都有自己的中繼憑證，其是由 *ContosoRootCert*所簽署。 接著，每個子公司都會使用其中繼憑證來簽署每個裝置的分葉憑證。 在此案例中，Contoso 可以使用單一 DPS 實例，其中 *ContosoRootCert* 已驗證 [擁有權證明](./how-to-verify-certificates.md)。 每個子公司可以有一個註冊群組。 如此一來，每個個別的子公司都不需要擔心驗證憑證。


### <a name="end-entity-leaf-certificate"></a>終端實體「分葉」憑證

分葉憑證 (或終端實體憑證) 會識別憑證持有者。 它在其憑證鏈結中有根憑證，以及零或多個中繼憑證。 分葉憑證無法用來簽署其他任何憑證。 它會唯一識別裝置來佈建服務，且有時稱為裝置憑證。 在驗證期間，裝置會使用與此憑證相關聯的私密金鑰，回應以證明來自服務的持有挑戰。

與 [個別註冊](./concepts-service.md#individual-enrollment) 專案搭配使用的分葉憑證，需要 **主體名稱** 必須設定為個別註冊專案的註冊識別碼。 搭配 [註冊群組](./concepts-service.md#enrollment-group) 專案使用的分葉憑證應該將 [ **主體名稱** ] 設為所需的裝置識別碼，並顯示在註冊群組中已驗證裝置的 **註冊記錄** 中。

若要深入了解，請參閱[驗證以 X.509 CA 憑證簽署的裝置](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)。

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>使用 X.509 憑證控制對於佈建服務的裝置存取

布建服務會公開兩種註冊類型，您可以使用 x.509 證明機制來控制裝置存取：  

- [個別註冊](./concepts-service.md#individual-enrollment)項目是以與特定裝置相關聯的裝置憑證來設定的。 這些項目會控制特定裝置的註冊。
- [註冊群組](./concepts-service.md#enrollment-group)項目是與特定的中繼或根 CA 憑證相關聯的。 這些項目會控制所有裝置的註冊，這些裝置在其憑證鏈結中具有中繼或根憑證。 

#### <a name="dps-device-chain-requirements"></a>DPS 裝置鏈需求

當裝置使用註冊群組嘗試透過 DPS 註冊時，裝置必須將憑證鏈從分葉憑證傳送到已驗證 [擁有權證明](how-to-verify-certificates.md)的憑證。 否則，驗證將會失敗。

例如，如果只驗證根憑證，並將中繼憑證上傳到註冊群組，則裝置應該會從分葉憑證將憑證鏈呈現給驗證的根憑證。 此憑證鏈會在其間包含任何中繼憑證。 如果 DPS 無法將憑證鏈到已驗證的憑證，驗證將會失敗。

例如，針對裝置使用下列裝置鏈，請考慮使用公司。

![範例裝置憑證鏈](./media/concepts-x509-attestation/example-device-cert-chain.png) 

只會驗證根憑證，並在註冊群組上上傳 *intermediate2* 憑證。

![驗證根範例](./media/concepts-x509-attestation/example-root-verified.png) 

如果裝置在布建期間只傳送下列裝置鏈，驗證將會失敗。 因為 DPS 無法嘗試驗證（假設 *intermediate1* 憑證的有效性）

![失敗的憑證鏈範例](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

如果裝置在布建期間以如下方式傳送完整的裝置鏈，則 DPS 可以嘗試驗證裝置。

![範例裝置憑證鏈](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> 中繼憑證也可以驗證擁有權 [證明](how-to-verify-certificates.md)。


#### <a name="dps-order-of-operations-with-certificates"></a>使用憑證的 DPS 作業順序
當裝置連線到佈建服務時，服務會給予更特定註冊項目比起較不特定註冊項目更高的優先順序。 也就是說，如果有裝置的個別註冊存在，佈建服務會套用該項目。 如果裝置沒有個別註冊，且裝置的憑證鏈中有第一個中繼憑證的註冊群組存在，則服務會將該專案套用至根節點，依此類推。 服務會套用它所找到的第一個適用項目，例如：

- 如果找到的第一個註冊項目已啟用，則服務會佈建裝置。
- 如果找到的第一個註冊項目已停用，則服務不會佈建裝置。  
- 如果在裝置的憑證鏈結中找不到任何憑證的註冊項目，則服務不會佈建裝置。 

這項機制和憑證鏈結的階層式結構提供功能強大具彈性的方式，讓您可以控制個別裝置以及裝置群組的存取。 例如，假設有五個裝置具有下列憑證鏈結： 

- 裝置 1**：根憑證 -> 憑證 A -> 裝置 1 憑證
- 裝置 2**：根憑證 -> 憑證 A -> 裝置 2 憑證
- 裝置 3**：根憑證 -> 憑證 A -> 裝置 3 憑證
- 裝置 4**：根憑證 -> 憑證 B -> 裝置 4 憑證
- 裝置 5**：根憑證 -> 憑證 B -> 裝置 5 憑證

一開始，您可以為根憑證建立單一已啟用群組註冊項目，以啟用所有五個裝置的存取。 如果憑證 B 稍後洩露，您可以為憑證 B 建立已停用註冊群組項目，以防止裝置 4** 和裝置 5** 註冊。 如果稍後裝置 3** 也遭到入侵，您可以針對其憑證建立已停用個別註冊項目。 這樣會撤銷裝置 3** 的存取權，但是仍然允許裝置 1** 和裝置 2** 註冊。