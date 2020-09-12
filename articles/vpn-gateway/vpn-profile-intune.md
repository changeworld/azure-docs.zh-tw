---
title: 建立適用于 Azure VPN 用戶端的 Intune 設定檔
titleSuffix: Azure VPN Gateway
description: 瞭解如何建立 Intune 自訂設定檔以部署 Azure VPN 用戶端設定檔
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/11/2020
ms.author: cherylmc
ms.openlocfilehash: 5a754a14cada1547a83e29d474e9b77aed7a2728
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024344"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>建立 Intune 設定檔以部署 VPN 用戶端設定檔

您可以使用 Microsoft Intune (Windows 10) 來部署 Azure VPN 用戶端的設定檔。 本文可協助您使用自訂設定來建立 Intune 設定檔。

## <a name="prerequisites"></a>必要條件

* 裝置已向 Intune MDM 註冊。
* 已在用戶端電腦上部署 Windows 10 的 Azure VPN Client。
* 僅支援 Windows 版本19H2 或更高版本。

## <a name="modify-xml"></a><a name="xml"></a>修改 XML

在下列步驟中，我們會使用適用于 Intune 之自訂 OMA-URI 設定檔的範例 XML，其具有下列設定：

* 自動連接開啟
* 受信任的網路偵測已啟用。

如需其他支援的選項，請參閱 [>VPNV2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) 文章。

1. 從 Azure 入口網站下載 VPN 設定檔，並從套件中解壓縮 *azurevpnconfig.xml* 檔案。
1. 將下列文字複製並貼到新的文字編輯器檔案中。

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. ```<ServerUrlList>``` ```</ServerUrlList>``` 使用您所下載設定檔中的專案來修改和之間的專案 ( # A0) 。 變更 "TrustedNetworkDetection" FQDN 以符合您的環境。
1. 開啟 Azure 下載的設定檔 ( # A0) ，藉由反白顯示文字並按 + C 將內容複寫到剪貼簿 <ctrl> 。請複製下列 AzVpnProfile 行之間的所有內容，但不要複製 AzVpnProfile 行本身：

   ```
   <AzVpnProfile xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.datacontract.org/2004/07/">
     <any xmlns:d2p1="http://schemas.datacontract.org/2004/07/System.Xml"
       i:nil="true" />

   For example - copy the text in your xml that is located here.

   </AzVpnProfile>
   ```
1. 將上一個步驟所複製的文字貼到您在步驟2中于標記之間建立的檔案 ```<CustomConfiguration>  </CustomConfiguration>``` 。 使用 xml 副檔名來儲存檔案。
1. 記下標記中的值 ```<name>  </name>``` 。 這是設定檔的名稱。 當您在 Intune 中建立設定檔時，您將需要此名稱。 關閉檔案，並記住儲存檔案的位置。

## <a name="create-intune-profile"></a>建立 Intune 設定檔

在本節中，您會建立具有自訂設定的 Microsoft Intune 設定檔。

1. 登入 Intune，然後流覽至 [ **裝置]->** 設定設定檔。 選取 [ **+ 建立設定檔**]。

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="組態設定檔":::
1. 針對 [平台]  ，選取 [Windows 10 及更新版本]  。 針對 [ **設定檔**]，選取 [ **自訂**]。 然後，選取 [Create] \(建立\)。
1. 提供設定檔的名稱和描述，然後選取 **[下一步]**。
1. 在 [ **設定設定** ] 索引標籤上，選取 [ **新增**]。

    * **名稱：** 輸入設定的名稱。
    * **描述：** 選擇性描述。
    * **OMA-URI：** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (可以在<name> </name>標記) 的 azurevpnconfig.xml 檔案中找到這項資訊。
    * **資料類型：** (XML 檔案) 的字串。

   選取資料夾圖示，然後選取您在步驟6中儲存的檔案（ [XML](#xml) 步驟）。 選取 [新增]。

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="組態設定" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. 選取 [下一步] 。
1. 在 [指派] 底下，選取您要推送 **設定**的群組。 然後，選取 [下一步]。
1. 適用性規則是選擇性的。 視需要定義任何規則，然後選取 **[下一步]**。
1. 在 [ **審核 + 建立** ] 頁面上，選取 [ **建立**]。

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="建立設定檔":::
1. 現在已建立您的自訂設定檔。 如需部署此設定檔的 Microsoft Intune 步驟，請參閱 [指派使用者和裝置設定檔](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign)。
 
## <a name="next-steps"></a>後續步驟

如需點對站詳細資訊，請參閱[關於點對站](point-to-site-about.md)。
