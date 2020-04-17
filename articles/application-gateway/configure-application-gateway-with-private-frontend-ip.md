---
title: 設定內部負載均衡器 (ILB) 終結點
titleSuffix: Azure Application Gateway
description: 本文提供有關如何使用專用前端 IP 位址設定應用程式閘道的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: df21a2c40dd532ac1ff321638099ceee8a2b3e53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535582"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用內部負載均衡器 (ILB) 終結點設定應用程式閘道

Azure 應用程式閘道可以配置針對 Internet 的 VIP 或內部終結點,該終結點未向 Internet 公開。 內部終結點對前端使用專用 IP 位址,也稱為*內部負載均衡器 (ILB) 終結點*。

使用前端專用 IP 位址配置閘道對於未向 Internet 公開的內部業務線應用程式非常有用。 它還適用於多層應用程式中的服務和層,這些應用程序處於安全邊界內,這些邊界未向 Internet 公開,但仍需要迴圈負載分發、會話粘性或傳輸層安全 (TLS),以前稱為安全套接字層 (SSL))終止。

本文將指導您完成使用 Azure 門戶使用前端專用 IP 位址配置應用程式閘道的步驟。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

在 <https://portal.azure.com> 登入 Azure 入口網站

## <a name="create-an-application-gateway"></a>建立應用程式閘道

Azure 需要虛擬網路才能在您所建立的資源之間進行通訊。 您可以建立新的虛擬網路，或使用現有的虛擬網路。 在此範例中，您會建立新的虛擬網路。 您建立應用程式閘道時，可以同時建立虛擬網路。 在不同的子網路中，建立應用程式閘道執行個體。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。

1. 展開門戶功能表並選擇"**創建資源**"
2. 在 [精選] 清單中選取 [網路]****，然後選取 [應用程式閘道]****。
3. 輸入 myAppGateway** 作為應用程式閘道的名稱，輸入 myResourceGroupAG** 作為新的資源群組。
4. 對於**區域**,選擇 **(美國)美國中部**。
5. 對於**層**,請選擇 **"標準**"。
6. **在「設定虛擬網路」****選擇「建立新**」,然後輸入虛擬網路的這些值:
   - *myVNet* - 用於虛擬網路的名稱。
   - 10.0.0.0/16** - 作為虛擬網路位址空間。
   - myAGSubnet** - 作為子網路名稱。
   - 10.0.0.0/24** - 作為子網路位址空間。
   - *myBackend 子網*- 用於後端子網名稱。
   - *10.0.1.0/24* - 用於後端子網位址空間。

    ![建立虛擬網路](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. 選取 [確定]**** 以建立虛擬網路和子網路。
7. 選擇**下一個:正面**。
8. 對於**前端 IP 位址類型**,請選擇 **"專用**"。

   預設情況下,它是動態 IP 位址分配。 設定子網的第一個可用位址被指定為前端 IP 位址。
   > [!NOTE]
   > 分配後,以後無法更改 IP 位址類型(靜態或動態)。
9. 選擇**下一個:後端介面**。
10. 選擇 **「新增後端池**」。
11. 對於**名稱**,鍵入*套用閘道後端介面*。
12. 對於**添加沒有目標的後端池**,請選擇 **"是**"。 稍後將添加目標。
13. 選取 [新增]  。
14. 選擇 **「下一步」」的設定**。
15. 在 **「路由規則**」下,選擇 **「添加規則**」。。
16. 對於**規則名稱**,鍵入*Rrule-01*。
17. 對於**偵聽器名稱**,鍵入*偵聽器-01*。
18. 對於**前端 IP,** 選擇 **「專用**」 。
19. 接受剩餘的預設值並選擇 **「後端目標**」選項卡。
20. 對於**目標類型**,選擇**後端池**,然後選擇 **「應用閘道後端池**」。
21. 對於**HTTP 設定**,選擇 **"創建新**" 。
22. 對於**HTTP 設定名稱**,鍵入*http-設定-01*。
23. 此**程式協定**,選擇**HTTP**。
24. 對於**後端埠**,鍵入*80*。
25. 接受剩餘的預設值,然後選擇 **「添加**」。。
26. 在「**添加路由規則**」 頁上,選擇 **「添加**」。。
27. 選擇**下一步「標記**」 。
28. 選擇 **「下一步」:檢視 + 建立**。
29. 檢視摘要頁面上的設定,然後選擇 **「創建**」以創建網路資源和應用程式閘道。 可能需要幾分鐘的時間來建立應用程式閘道。 請等候部署成功完成後，再繼續進行至下一節。

## <a name="add-backend-pool"></a>新增後端集區

後端集區用於將要求路由傳送至可為要求提供服務的後端伺服器。 後端介面可以由 NIC、虛擬機器規模集、公共 IP 位址、內部 IP 位址、完全限定的網域名稱 (FQDN) 和多租戶後端(如 Azure 應用服務)組成。 在此範例中，您會使用虛擬機器作為目標後端。 您可以使用現有的虛擬機器，或建立新的虛擬機器。 在此範例中，您會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。

若要這樣做，請：

1. 創建兩個新的虛擬機 *,myVM*和*myVM2,* 用作後端伺服器。
2. 在虛擬機器上安裝 IIS，以確認成功建立應用程式閘道。
3. 將後端伺服器新增至後端集區。

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選取 [建立資源]  。
2. 選擇 **「計算**」,然後選擇**虛擬機器**。
4. 依虛擬機器輸入這些值：
   - 為**資源組**選擇 *"我的資源組"*
   - *myVM* - 用於**虛擬機器名稱**。
   - 選擇**Windows 伺服器 2019 資料中心****的影像**。
   - 有效的**使用者名稱**。
   - 有效的**密碼**。
5. 接受剩餘的預設值,然後選擇 **「下一步:磁碟**」。
6. 接受預設值並選擇 **「下一步:網路**」。
7. 確定您已選取 [myVNet]**** 作為虛擬網路，而且子網路是 [myBackendSubnet]****。
8. 接受剩餘的預設值,然後選擇 **「下一步:管理**」。。
9. 選取 [關閉]**** 來停用開機診斷。
10. 接受剩餘的預設值,然後選擇 **「下一步:高級**」。
11. 選擇**下一個 : 標記**。
12. 選擇**下一個 : 查看 + 建立**。
13. 檢閱摘要頁面上的設定，然後選取 [建立]****。 創建 VM 可能需要幾分鐘時間。 請等候部署成功完成後，再繼續進行至下一節。

### <a name="install-iis"></a>安裝 IIS

1. 開啟雲外殼並確保將其設定為**PowerShell**。
    ![私人前尾-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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

1. 選取 [所有資源]****，然後選取 [myAppGateway]****。
2. 選取 [後端集區]****。 選擇**應用程式閘道後端介面**。
3. 在 **「目標類型**」下選擇**虛擬機器**,並在 **「目標**」下選擇與 myVM 關聯的 vNIC。
4. 重複添加 MyVM2。
   ![私人前面-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. 選擇 **"保存"**

## <a name="test-the-application-gateway"></a>測試應用程式閘道

1. 按一下門戶中的**前端 IP 配置**頁面,檢查已分配的前端 IP。
    ![私人前面-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 複製專用 IP 位址,然後將其貼上到同一 VNet 或具有連接到此 VNet 的連接的本地 VM 中的瀏覽器位址列中,然後嘗試存取應用程式閘道。

## <a name="next-steps"></a>後續步驟

如果要監視後端執行狀況,請參閱[應用程式閘道的後端執行狀況與診斷紀錄](application-gateway-diagnostics.md)。
