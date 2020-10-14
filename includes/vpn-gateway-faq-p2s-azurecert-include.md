---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75751715"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>如果我在使用憑證驗證連線時發生憑證不符的情況，該怎麼辦？

取消核取 [驗證憑證以確認伺服器的身分識別] 或在手動建立設定檔時**新增伺服器 FQDN 和憑證**。 若要這麼做，您可以從命令提示字元執行 **rasphone**，然後從下拉式清單中挑選設定檔。

一般不建議略過伺服器身分識別驗證，但進行 Azure 憑證驗證時，會使用相同的憑證來進行 VPN 通道通訊協定 (IKEv2/SSTP) 和 EAP 通訊協定中的伺服器驗證。 因為伺服器憑證和 FQDN 已經由 VPN 通道通訊協定驗證，所以在 EAP 中再次驗證相同項目是多餘的。

![點對站](./media/vpn-gateway-faq-p2s-all-include/servercert.png "伺服器憑證")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>是否可以使用自己的內部 PKI 根 CA 來產生點對站連線的憑證？

是。 先前只能使用自我簽署的根憑證。 您仍然可以上傳 20 個根憑證。

### <a name="can-i-use-certificates-from-azure-key-vault"></a>是否可以使用來自 Azure Key Vault 的憑證？

否。

### <a name="what-tools-can-i-use-to-create-certificates"></a>我可以使用哪些工具來建立憑證？

您可以使用 Enterprise PKI 解決方案 (您的內部 PKI)、Azure PowerShell、MakeCert 和 OpenSSL。

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>是否有憑證設定及參數的指示？

* **內部 PKI/Enterprise PKI 解決方案：** 請參閱步驟來[產生憑證](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。

* **Azure PowerShell：** 請參閱 [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) 文章以了解步驟。

* **MakeCert：** 請參閱 [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) 文章以了解步驟。

* **OpenSSL：** 

    * 匯出憑證時，請務必將根憑證轉換為 Base64。

    * 針對用戶端憑證：

      * 建立私密金鑰時，請將長度指定為 4096。
      * 建立憑證時，針對 -extensions** 參數，請指定 usr_cert**。
