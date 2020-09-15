---
title: 設定內部負載平衡器 (ILB) 端點
titleSuffix: Azure Application Gateway
description: 本文提供如何設定具有私人前端 IP 位址的應用程式閘道的相關資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: 64dfe284772faf2a345b7959f1a1bd6f474cd1bf
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562480"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用內部負載平衡器 (ILB) 端點來設定應用程式閘道

Azure 應用程式閘道可設定為使用網際網路對應的 VIP，或具有未公開到網際網路的內部端點。 內部端點會使用前端的私人 IP 位址，也稱為 *內部負載平衡器 (ILB) 端點*。

使用前端私人 IP 位址設定閘道適用于不會向網際網路公開的內部企業營運應用程式。 它也適用于多層式應用程式內的服務和層級，而這些應用程式位於不會公開至網際網路的安全性界限內，但仍需要迴圈配置資源負載散發、會話輸出或傳輸層安全性 (TLS) ，之前稱為安全通訊端層 (SSL) 終止。

本文將引導您完成使用 Azure 入口網站設定具有前端私人 IP 位址的應用程式閘道的步驟。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

在 <https://portal.azure.com> 登入 Azure 入口網站

## <a name="create-an-application-gateway"></a>建立應用程式閘道

Azure 需要虛擬網路才能在您所建立的資源之間進行通訊。 您可以建立新的虛擬網路，或使用現有的虛擬網路。 在此範例中，您會建立新的虛擬網路。 您建立應用程式閘道時，可以同時建立虛擬網路。 在不同的子網路中，建立應用程式閘道執行個體。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。

1. 展開入口網站功能表，然後選取 [ **建立資源**]。
2. 選取 [ **網路** ]，然後選取 [精選] 清單中的 [ **應用程式閘道** ]。
3. 輸入 myAppGateway** 作為應用程式閘道的名稱，輸入 myResourceGroupAG** 作為新的資源群組。
4. 針對 [ **區域**]，選取 ** (Us) 美國中部**。
5. 針對 **[** 階層]，選取 [ **標準**]。
6. 在 [ **設定虛擬網路** ] 下，選取 [ **建立新**的]，然後輸入虛擬網路的下列值：
   - myVNet  - 作為虛擬網路的名稱。
   - 10.0.0.0/16  - 作為虛擬網路位址空間。
   - myAGSubnet** - 作為子網路名稱。
   - 10.0.0.0/24  - 作為子網路位址空間。
   - *myBackendSubnet* -作為後端子網名稱。
   - *10.0.1.0/24* -代表後端子網位址空間。

    ![建立虛擬網路](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. 選取 [確定]**** 以建立虛擬網路和子網路。
7. 選取 **[下一步：前端]**。
8. 針對 [ **前端 IP 位址類型**]，選取 [ **私**用]。

   依預設，它是動態 IP 位址指派。 已設定子網的第一個可用位址會指派為前端 IP 位址。
   > [!NOTE]
   > 一旦配置之後，就無法在稍後變更靜態或動態)  (的 IP 位址類型。
9. 選取 **[下一步：後端]**。
10. 選取 [ **新增後端集**區]。
11. 針對 [ **名稱**]，輸入 *appGatewayBackendPool*。
12. 針對 [ **新增沒有目標的後端集**區]，選取 **[是]**。 您稍後將會新增目標。
13. 選取 [新增]  。
14. 選取 **[下一步：設定]**。
15. 在 [ **路由規則**] 底下，選取 [ **新增規則**]。
16. 在 [ **規則名稱**] 中，輸入 *Rrule-01*。
17. 針對 [接聽程式 **名稱**]，輸入接聽 *程式-01*。
18. 針對 [ **前端 IP**]，選取 [ **私**用]。
19. 接受其餘的預設值，然後選取 [ **後端目標** ] 索引標籤。
20. 針對 [ **目標型別**]，選取 [ **後端集**區]，然後選取 [ **appGatewayBackendPool**]。
21. 針對 [ **HTTP 設定**]，選取 [ **建立新**的]。
22. 針對 [ **HTTP 設定名稱**]，輸入 *HTTP-設定-01*。
23. 針對 **後端通訊協定**，選取 [ **HTTP**]。
24. 針對 [ **後端埠**]，輸入 *80*。
25. 接受其餘的預設值，然後選取 [ **新增**]。
26. 在 [ **新增路由規則** ] 頁面上，選取 [ **新增**]。
27. 選取 **[下一步：標記]**。
28. 完成時，選取 [下一步:檢閱 + 建立]。
29. 查看 [摘要] 頁面上的設定，然後選取 [ **建立** ] 以建立網路資源和應用程式閘道。 可能需要幾分鐘的時間來建立應用程式閘道。 請等候部署成功完成後，再繼續進行至下一節。

## <a name="add-backend-pool"></a>新增後端集區

後端集區用於將要求路由傳送至可為要求提供服務的後端伺服器。 後端可以包含 Nic、虛擬機器擴展集、公用 IP 位址、內部 IP 位址、完整功能變數名稱 (FQDN) ，以及多租使用者後端（例如 Azure App Service）。 在此範例中，您會使用虛擬機器作為目標後端。 您可以使用現有的虛擬機器，或建立新的虛擬機器。 在此範例中，您會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。

若要這樣做，請：

1. 建立兩個新的虛擬機器， *myVM* 和 *myVM2*作為後端伺服器使用。
2. 在虛擬機器上安裝 IIS，以確認成功建立應用程式閘道。
3. 將後端伺服器新增至後端集區。

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選取 [建立資源]。
2. 選取 [計算]，然後選取 [虛擬機器]。
4. 依虛擬機器輸入這些值：
   - 選取 [ *myResourceGroupAG* ] 作為 [ **資源群組**]。
   - *myVM* -適用于 **虛擬機器名稱**。
   - 選取 [適用于**映射**的**Windows Server 2019 Datacenter** ]。
   - 有效的使用者 **名稱**。
   - 有效的 **密碼**。
5. 接受其餘的預設值，然後選取 **[下一步：磁片]**。
6. 接受預設值，然後選取 **[下一步：網路]**。
7. 確定您已選取 [myVNet]**** 作為虛擬網路，而且子網路是 [myBackendSubnet]****。
8. 接受其餘的預設值，然後選取 **[下一步：管理]**。
9. 選取 [關閉]  來停用開機診斷。
10. 接受其餘的預設值，然後選取 **[下一步： Advanced]**。
11. 選取 [下一步:標籤]。
12. 選取 **[下一步]： [檢查 + 建立]**。
13. 檢閱摘要頁面上的設定，然後選取 [建立]。 建立 VM 可能需要幾分鐘的時間。 請等候部署成功完成後，再繼續進行至下一節。

### <a name="install-iis"></a>安裝 IIS

1. 開啟 Cloud Shell，並確定它已設定為 **PowerShell**。
    ![螢幕擷取畫面顯示使用 PowerShell 的開啟 Azure Cloud Shell 主控台視窗。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 執行下列命令以在虛擬機器上安裝 IIS：

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. 建立第二個虛擬機器，並使用您剛完成的步驟來安裝 IIS。 輸入 myVM2 作為其名稱，及作為 Set-AzVMExtension 中的 VMName。

### <a name="add-backend-servers-to-backend-pool"></a>將後端伺服器新增至後端集區

1. 選取 [所有資源]  ，然後選取 [myAppGateway]  。
2. 選取 [後端集區]****。 選取 [ **appGatewayBackendPool**]。
3. 在 [ **目標型別** ] 下選取 [ **虛擬機器**  ]，然後在 [ **目標**] 底下選取與 myVM 相關聯的 vNIC。
4. 重複以新增 MyVM2。
   ![螢幕擷取畫面顯示 [編輯後端集區] 窗格，其中已反白顯示目標型別和目標。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. 選取 [ **儲存]。**

## <a name="test-the-application-gateway"></a>測試應用程式閘道

1. 按一下入口網站中的 [ **前端 ip** 設定] 頁面，檢查已指派的前端 ip。
    ![螢幕擷取畫面顯示 [前端 IP 設定] 窗格，其中已反白顯示 [私用] 類型。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 複製私人 IP 位址，然後將其貼到位於相同 VNet 或內部部署的 VM 中的瀏覽器位址列，且該 VM 可以連線到此 VNet，並嘗試存取應用程式閘道。

## <a name="next-steps"></a>後續步驟

如果您想要監視後端的健康情況，請參閱 [應用程式閘道的後端健康情況和診斷記錄](application-gateway-diagnostics.md)。
