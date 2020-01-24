---
title: 入口網站中的 HTTP 至 HTTPS 重新導向-Azure 應用程式閘道
description: 了解如何使用 Azure 入口網站，以建立可將流量從 HTTP 重新導向到 HTTPS 的應用程式閘道。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 51c191a7815bb64243e2324e150c00c2dcb7ec4c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705321"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 入口網站來建立具有 HTTP 到 HTTPS 重新導向功能的應用程式閘道

您可以使用 Azure 入口網站來建立包含 SSL 終止憑證的[應用程式閘道](overview.md)。 路由規則可用來將 HTTP 流量重新導向至您應用程式閘道中的 HTTPS 連接埠。 在此範例中，您也會為應用程式閘道的後端集區，建立一個包含兩個虛擬機器執行個體的[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立自我簽署憑證
> * 設定網路
> * 建立包含憑證的應用程式閘道
> * 新增接聽程式和重新導向規則
> * 建立包含預設後端集區的虛擬機器擴展集

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本教學課程需要 Azure PowerShell 模組1.0.0 版或更新版本，才能建立憑證並安裝 IIS。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 若要在本教學課程中執行命令，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

若要在生產環境中使用，您應該匯入由受信任提供者所簽署的有效憑證。 在此教學課程中，您要使用 [New-selfsignedcertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。 您可以使用 [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) 搭配系統所傳回的指紋，以將 pfx 檔案從憑證匯出。

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

您應該會看到類似這個結果的內容：

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

使用指紋來建立 pfx 檔案：

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>建立應用程式閘道

需要虛擬網路，才能在您所建立的資源之間進行通訊。 這個範例中會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。 您建立應用程式閘道時，可以同時建立虛擬網路。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 按一下 Azure 入口網站左上角的 [建立資源]。
3. 在 [精選] 清單中選取 [網路]，然後選取 [應用程式閘道]。
4. 針對應用程式閘道輸入這些值：

   - myAppGateway - 作為應用程式閘道的名稱。
   - myResourceGroupAG - 作為新資源群組。

     ![建立新的應用程式閘道](./media/create-url-route-portal/application-gateway-create.png)

5. 接受其他設定的預設值，然後按一下 [確定]。
6. 按一下 [選擇虛擬網路]，按一下 [新建]，然後針對虛擬網路輸入這些值：

   - myVNet - 作為虛擬網路的名稱。
   - 10.0.0.0/16 - 作為虛擬網路位址空間。
   - myAGSubnet - 作為子網路名稱。
   - 10.0.0.0/24 - 作為子網路位址空間。

     ![建立虛擬網路](./media/create-url-route-portal/application-gateway-vnet.png)

7. 按一下 [確定] 以建立虛擬網路和子網路。
8. 在 [前端 IP 組態] 下方確認 [IP 位址類型] 為 [公用]，並已選取 [新建]。 輸入 *myAGPublicIPAddress* 作為名稱。 接受其他設定的預設值，然後按一下 [確定]。
9. 在 [接聽程式設定] 下方，選取 **HTTPS**，然後選取 [選取檔案]，再瀏覽到 *c:\appgwcert.pfx* 檔案並選取 [開啟]。
10. 輸入 *appgwcert* 作為憑證名稱，而 *Azure123456!* 作為密碼。
11. 讓 Web 應用程式防火牆保持停用，然後選取 [確定]。
12. 檢閱 [摘要] 頁面上的設定，然後選取 [確定] 以建立網路資源和應用程式閘道。 建立應用程式閘道可能需要幾分鐘的時間，請等候部署成功完成後，再繼續進行至下一節。

### <a name="add-a-subnet"></a>新增子網路

1. 選取左側功能表中的 [所有資源]，然後從 [資源] 清單中選取 [myVNet]。
2. 選取 [子網路]，然後按一下 [子網路]。

    ![建立子網路](./media/create-url-route-portal/application-gateway-subnet.png)

3. 輸入 *myBackendSubnet* 作為子網路名稱。
4. 輸入 *10.0.2.0/24* 作為位址範圍，然後選取 [確定]。

## <a name="add-a-listener-and-redirection-rule"></a>新增接聽程式和重新導向規則

### <a name="add-the-listener"></a>新增接聽程式

首先，針對連接埠 80 新增名為 *myListener* 的接聽程式。

1. 開啟 **myResourceGroupAG** 資源群組，然後選取 **myAppGateway**。
2. 選取 [接聽程式]，然後選取 [+ 基本]。
3. 輸入 *MyListener* 作為名稱。
4. 輸入 *httpPort* 作為新的前端連接埠名稱，而 *80* 作為連接埠。
5. 確定將通訊協定設定為 **HTTP**，然後選取 [確定]。

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>新增具有重新導向設定的路由規則

1. 在**myAppGateway**上，選取 [**規則**]，然後選取 [ **+ 要求路由規則**]。
2. 在 [**規則名稱**] 中，輸入*Rule2*。
3. 確定已選取 **MyListener** 作為接聽程式。
4. 按一下 [**後端目標**] 索引標籤，然後選取 [**目標型別** *] 做為*重新導向
5. 針對 [重新導向類型]，選取 [永久]。
6. 針對 [重新導向目標]，選取 [接聽程式]。
7. 確定 [目標接聽程式] 設定為 **appGatewayHttpListener**。
8. 針對 [**包含查詢字串**] 和 [**包含路徑**]，選取 *[是]* 。
9. 選取 [新增]。

## <a name="create-a-virtual-machine-scale-set"></a>建立虛擬機器擴展集

在此範例中，您會建立虛擬機器擴展集，以在應用程式閘道中提供後端集區的伺服器。

1. 在入口網站左上角選取 [+建立資源]。
2. 選取 [計算]。
3. 在搜尋方塊中，輸入*擴展集*，然後按 Enter 鍵。
4. 選取 [虛擬機器擴展集]，然後選取 [建立]。
5. 針對 [虛擬機器擴展集名稱]，輸入 *myvmss*。
6. 針對作業系統磁碟映像，** 確定已選取 [Windows Server 2016 Datacenter]。
7. 針對 [資源群組]，選取 **myResourceGroupAG**。
8. 針對 [使用者名稱]，輸入 *azureuser*。
9. 針對 [密碼]，輸入 *Azure123456!* 並確認密碼。
10. 確認 [執行個體計數] 的值為 **2**。
11. 針對 [執行個體大小]，選取**D2s_v3**。
12. 在 [網路] 下方，確定 [選擇負載平衡選項] 設定為 [應用程式閘道]。
13. 確定 [應用程式閘道] 設定為 **myAppGateway**。
14. 確定 [子網路] 設定為 **myBackendSubnet**。
15. 選取 [建立]。

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>建立擴展集與適當後端集區的關聯

虛擬機器擴展集入口網站 UI 建立擴展集的新後端集區，但您想要將之與現有的 appGatewayBackendPool 建立關聯。

1. 開啟 **myResourceGroupAG** 資源群組。
2. 選取 **myAppGateway**。
3. 選取 [後端集區]。
4. 選取 **myAppGatewaymyvmss**。
5. 選取 [移除後端集區中的所有目標]。
6. 選取 [儲存]。
7. 此流程完成後，選取 **myAppGatewaymyvmss** 後端集區，選取 [刪除]，然後選取 [確定] 加以確認。
8. 選取 **appGatewayBackendPool**。
9. 在 [目標] 下方，選取 **VMSS**。
10. 在 **VMSS** 下方，選取 **myvmss**。
11. 在 [網路介面組態] 下方，選取 **myvmssNic**。
12. 選取 [儲存]。

### <a name="upgrade-the-scale-set"></a>升級擴展集

最後，您必須將含有這些變更擴展集升級。

1. 選取 **myvmss** 擴展集。
2. 在 [設定] 底下，選取 [執行個體]。
3. 兩個執行個體都選取，然後選取 [升級]。
4. 選取 [是] 加以確認。
5. 如此完成之後，返回 **myAppGateway**，然後選取 [後端集區]。 您現在應會看到 **appGatewayBackendPool** 有兩個目標，而 **myAppGatewaymyvmss** 有零個目標。
6. 選取 **myAppGatewaymyvmss**，然後選取 [刪除]。
7. 選取 [確定] 來進行確認。

### <a name="install-iis"></a>安裝 IIS

輕鬆地在擴展集上安裝 IIS，就是使用 PowerShell。 從入口網站按一下 Cloud Shell 圖示，並確定已選取 **PowerShell**。

將下列指令碼貼到 PowerShell 視窗中，然後按 ENTER 鍵。

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>升級擴展集

使用 IIS 變更執行個體之後，您必須再次將含有這項變更的擴展集升級。

1. 選取 **myvmss** 擴展集。
2. 在 [設定] 底下，選取 [執行個體]。
3. 兩個執行個體都選取，然後選取 [升級]。
4. 選取 [是] 加以確認。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

您可以從應用程式閘道的 [概觀] 頁面，取得應用程式的公用 IP 位址。

1. 選取 **myAppGateway**。
2. 在 [概觀] 頁面上，記下 [前端公用 IP 位址] 下方的 IP 位址。

3. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 例如， http://52.170.203.149

   ![安全警告](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. 若要在使用自我簽署憑證時接受安全性警告，請依序按一下 [詳細資料] 與 [繼續瀏覽網頁]。 接著會顯示受保護的 IIS 網站，如下列範例所示：

   ![在應用程式閘道中測試基底 URL](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>後續步驟

了解如何[建立具有內部重新導向的應用程式閘道](redirect-internal-site-powershell.md)。
