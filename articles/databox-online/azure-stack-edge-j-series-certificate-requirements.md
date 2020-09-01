---
title: Azure Stack Edge 的憑證需求和疑難排解 |Microsoft Docs
description: 描述憑證需求，以及針對 Azure Stack Edge 裝置的憑證錯誤進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: b24b745a53b632ce32cda37058363bf974d400b3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268257"
---
# <a name="certificate-requirements"></a>憑證需求

本文說明必須符合的憑證需求，才能將憑證安裝在 Azure Stack Edge 裝置上。 這些需求與 PFX 憑證、發行授權單位、憑證主體名稱和主體別名，以及支援的憑證演算法有關。

## <a name="certificate-issuing-authority"></a>憑證發行授權單位

憑證發行需求如下所示：

* 憑證必須由內部憑證授權單位或公用憑證授權單位發出。

* 不支援使用自我簽署憑證。

* 憑證的 [ *發行至：* ] 欄位不得與其 [ *簽發者：* ] 欄位相同，除了根 CA 憑證之外。



## <a name="certificate-algorithms"></a>憑證演算法

憑證演算法必須有下列需求：

* 憑證必須使用 RSA 金鑰演算法。

* 憑證簽章演算法不能是 SHA1。

* 最小金鑰大小為4096。

## <a name="certificate-subject-name-and-subject-alternative-name"></a>憑證主體名稱和主體別名

憑證必須具有下列主體名稱和主體替代名稱需求：

* 您可以使用單一憑證來涵蓋憑證的主體替代名稱中的所有命名空間 (SAN) 欄位。 或者，您可以針對每個命名空間使用個別憑證。 這兩種方法都需要針對必要的端點使用萬用字元，例如二進位大型物件 (Blob) 。

* 確定 [主體名稱] (中的 [主體名稱]) 屬於 [主體別名] 延伸中的主體替代名稱。

* 您可以使用單一萬用字元憑證，涵蓋憑證 SAN 欄位中的所有命名空間。

* 建立端點憑證時，請使用下表：

    |類型 | (SN 的主體名稱)   | (SAN) 的主體替代名稱  |主體名稱範例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 儲存體|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |本機 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |這兩個端點的多重 SAN 單一憑證|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |節點|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate 是硬式編碼的。  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 憑證

安裝在 Azure Stack Edge 裝置上的 PFX 憑證應符合下列需求：

* 當您從 SSL 授權單位取得憑證時，請確定您取得憑證的完整簽署鏈。

* 當您匯出 PFX 憑證時，請確定您已選取 [ **包含鏈中的所有憑證（如果可能）** ] 選項。

* 使用適用于端點、本機 UI、節點、VPN 和 Wi-fi 的 PFX 憑證，因為 Azure Stack Edge 需要公開金鑰和私密金鑰。 私密金鑰必須有本機電腦金鑰屬性集。

* 憑證的 PFX 加密應該是 3DES。 這是從 Windows 10 用戶端或 Windows Server 2016 憑證存放區匯出時使用的預設加密。 如需與3DES 相關的詳細資訊，請參閱 [三重 DES](https://en.wikipedia.org/wiki/Triple_DES)。

* 憑證 PFX 檔案的 [*金鑰使用*方法] 欄位中必須有有效的*數位簽章*和*KeyEncipherment*值。

* 憑證 PFX 檔案的 [*增強金鑰使用*方法] 欄位中必須有 [*伺服器驗證 (1.3.6.1.5.5.7.3.1) *和*用戶端驗證 (1.3.6.1.5.5.7.3.2) *的值。

* 如果您使用 Azure Stack 準備就緒檢查工具，則在部署時，所有憑證 PFX 檔案的密碼都必須相同。 如需詳細資訊，請參閱 [使用 Azure Stack Hub 就緒檢查工具來建立 Azure Stack Edge 的憑證](azure-stack-edge-j-series-create-certificates-tool.md)。

* 憑證 PFX 的密碼必須是複雜密碼。 請記下此密碼，因為它是用來作為部署參數。

如需詳細資訊，請參閱 [匯出具有私密金鑰的 PFX 憑證](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)。

## <a name="next-steps"></a>後續步驟

[使用憑證搭配 Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md)

[使用 Azure Stack Hub 的就緒檢查程式工具建立 Azure Stack Edge 的憑證](azure-stack-edge-j-series-create-certificates-tool.md)

[使用私密金鑰匯出 PFX 憑證](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[針對憑證錯誤進行疑難排解](azure-stack-edge-j-series-certificate-troubleshooting.md)