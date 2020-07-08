---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650646"
---
## <a name="1-download-the-file"></a>1.下載檔案

執行下列命令。 將結果 URL 複製到您的瀏覽器，以便下載設定檔 zip 檔案。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2.壓縮檔 ZIP 檔案

解壓縮 zip 檔案。 檔案包含下列資料夾：

* AzureVPN
* 泛型
* OpenVPN (如果您已在閘道上啟用 OpenVPN 和 Azure AD 驗證設定)。 針對 VPN 閘道，請參閱[建立租用戶](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)。 針對虛擬 WAN，請參閱[建立租用戶 - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md)。

## <a name="3-retrieve-information"></a>3.擷取資訊

在 [AzureVPN] 資料夾中，瀏覽至 azurevpnconfig.xml 檔案，然後使用 [記事本] 開啟該檔案。 記下下列標記之間的文字。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>設定檔詳細資料

當您新增連線時，請使用您在上一個步驟中針對設定檔詳細資料頁面所收集的資訊。 欄位會對應至下列資訊：

   * **對象：** 識別權杖針對的收件者資源
   * **簽發者：** 識別發出權杖的 Security Token Service (STS)，以及 Azure AD 租用戶
   * **租用戶：** 包含發出權杖的目錄租用戶不變唯一識別碼
   * **FQDN：** Azure VPN 閘道上的完整網域名稱 (FQDN)
   * **ServerSecret：** VPN 閘道預先共用金鑰

## <a name="folder-contents"></a>資料夾內容

* **一般資料夾**包含公用伺服器憑證和 VpnSettings.xml 檔案。 VpnSettings.xml 檔案包含設定一般用戶端所需的資訊。

* 下載的 ZIP 檔案可能也會包含 **WindowsAmd64** 和 **WindowsX86** 資料夾。 這些資料夾包含適用於 Windows 用戶端的 SSTP 和 IKEv2 安裝程式。 您需要用戶端上的系統管理員權限才能進行安裝。
