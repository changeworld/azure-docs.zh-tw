---
title: 使用 Azure 應用程式閘道來保護 Azure VMware 解決方案上的 web 應用程式
description: 設定 Azure 應用程式閘道，以安全地公開在 Azure VMware 解決方案上執行的 web 應用程式。
ms.topic: how-to
ms.date: 11/13/2020
ms.openlocfilehash: 02e439989c985354dbe06fa3e231d5daf7099d70
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628959"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>使用 Azure 應用程式閘道來保護 Azure VMware 解決方案上的 web 應用程式

[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 是第7層 web 流量負載平衡器，可讓您管理 web 應用程式的流量。 這兩個版本都是在 Azure VMware Solution 1.0 和 v2.0 中提供。 這兩個版本都是使用 Azure VMware 解決方案上執行的 web apps 進行測試。

功能包括： 
- Cookie 型工作階段同質
- URL 型路由
- Web 應用程式防火牆 (WAF)

如需功能的完整清單，請參閱 [Azure 應用程式閘道功能](../application-gateway/features.md)。 

本文說明如何在 web 伺服器陣列前方使用應用程式閘道，以保護在 Azure VMware 解決方案上執行的 web 應用程式。 

## <a name="topology"></a>拓撲
下圖顯示如何使用應用程式閘道來保護 (Vm) 、Azure 虛擬機器擴展集或內部部署伺服器上的 Azure IaaS 虛擬機器。 應用程式閘道會將 Azure VMware 解決方案 Vm 視為內部部署伺服器。 

![此圖顯示應用程式閘道如何保護 (Vm) 、Azure 虛擬機器擴展集或內部部署伺服器上的 Azure IaaS 虛擬機器。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure 應用程式閘道目前是公開在 Azure VMware 解決方案 Vm 上執行之 web 應用程式的唯一支援方法。

下圖顯示用來驗證使用 Azure VMware 解決方案 web 應用程式之應用程式閘道的測試案例。

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="此圖顯示用來驗證 Azure VMware 解決方案 web 應用程式的應用程式閘道的測試案例。" border="false":::

應用程式閘道實例會部署在中樞的專用子網中。 它有 Azure 公用 IP 位址。 建議您啟用虛擬網路的標準 DDoS 保護。 Web 服務器裝載于位於 NSX T0 和 T1 路由器後方的 Azure VMware 解決方案私用雲端。 Azure VMware 解決方案使用 [ExpressRoute Global 觸及](../expressroute/expressroute-global-reach.md) ，以啟用與中樞和內部部署系統的通訊。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。
- 已部署並正在執行的 Azure VMware 解決方案私人雲端。

## <a name="deployment-and-configuration"></a>部署和設定

1. 在 Azure 入口網站中，搜尋 **應用程式閘道** ，然後選取 [ **建立應用程式閘道** ]。

2. 提供基本的詳細資料，如下圖所示：然後選取 **[下一步：前端>]** 。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="顯示 Azure 入口網站中 [建立應用程式閘道] 頁面的螢幕擷取畫面。":::

3. 選擇前端 IP 位址類型。 若為公用，請選擇現有的公用 IP 位址，或建立一個新的位址。 選取 **[下一步：後端>]** 。

    > [!NOTE]
    > 私人前端只支援標準和 Web 應用程式防火牆 (WAF) Sku。

4. 新增 Vm 的後端集區，以在 Azure VMware 解決方案基礎結構上執行。 提供在 Azure VMware 解決方案私人雲端上執行之 web 伺服器的詳細資料，然後選取 [ **新增** ]。  然後選取 **[下一步：設定>]** 。

1. **在 [設定** ] 索引標籤上，選取 [ **新增路由規則** ]。

6. **在 [接聽程式] 索引** 標籤上，提供接聽程式的詳細資料。 如果選取 HTTPS，您必須從 PFX 檔案或現有的 Azure Key Vault 憑證提供憑證。 

7. 選取 [ **後端目標** ] 索引標籤，然後選取先前建立的後端集區。 針對 [ **HTTP 設定** ] 欄位，選取 [ **加入新** 的]。

8. 設定 HTTP 設定的參數。 選取 [新增]  。

9. 如果您想要設定以路徑為基礎的規則，請選取 [ **新增多個目標]，以建立以路徑為基礎的規則** 。 

10. 新增以路徑為基礎的規則，然後選取 [ **新增** ]。 重複以新增以路徑為基礎的其他規則。 

11. 當您完成新增以路徑為基礎的規則時，請再次選取 [ **新增** ];然後選取 **[下一步：標記>]** 。 

12. 新增標記，然後選取 **[下一步：檢查 + 建立>]** 。

13. 驗證將會在您的應用程式閘道上執行;如果成功，請選取 [ **建立** ] 以部署。

## <a name="configuration-examples"></a>組態範例

在本節中，您會瞭解如何使用 Azure VMware 解決方案 Vm 來設定應用程式閘道，作為這些使用案例的後端集區： 

- [裝載多個網站](#hosting-multiple-sites)
- [依 URL 路由](#routing-by-url)

### <a name="hosting-multiple-sites"></a>裝載多個網站

此程式示範如何在現有的應用程式閘道上，使用在 Azure VMware 解決方案私人雲端上執行的 Vm 來定義後端位址集區。 

>[!NOTE]
>此程式假設您有多個網域，因此我們將使用 www.contoso.com 和 www.fabrikam.com 的範例。


1. 在您的私人雲端中，建立兩個不同的 Vm 集區。 其中一個代表 Contoso 和第二個 Fabrikam。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="顯示 VSphere 用戶端中網頁伺服器詳細資料摘要的螢幕擷取畫面。":::

    我們使用 Windows Server 2016 搭配 Internet Information Services (安裝的 IIS) 角色來說明本教學課程。 一旦安裝 Vm 之後，請執行下列 PowerShell 命令，在每個 Vm 上設定 IIS。 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. 在現有的應用程式閘道實例中，從左側功能表中選取 [ **後端** 集區]  **，選取 [新增]** ，然後輸入新集區的詳細資料。 選取右窗格中的 [ **新增** ]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="新增後端集區的後端集區頁面螢幕擷取畫面。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. **在 [接聽** 程式] 區段中，為每個網站建立新的接聽程式。 輸入每個接聽程式的詳細資料，然後選取 [ **新增** ]。

4. 在左側選取 [ **HTTP 設定** ]，然後選取左窗格中的 [ **新增** ]。 填入詳細資料以建立新的 HTTP 設定，然後選取 [ **儲存** ]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="[HTTP 設定] 頁面的螢幕擷取畫面，可建立新的 HTTP 設定。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. 在左側功能表的 [ **規則** ] 區段中建立規則。 將每個規則與對應的接聽程式產生關聯。 選取 [新增]  。

6. 設定對應的後端集區和 HTTP 設定。 選取 [新增]  。

7. 測試連接。 開啟您慣用的瀏覽器，並流覽至裝載于 Azure VMware 解決方案環境的不同網站，例如 http://www.fabrikam.com 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="顯示成功測試連接的瀏覽器頁面螢幕擷取畫面。":::

### <a name="routing-by-url"></a>依 URL 路由

此程式示範如何在現有的應用程式閘道上，使用在 Azure VMware 解決方案私人雲端上執行的 Vm 來定義後端位址集區。 然後，您可以建立路由規則，確保 Web 流量會抵達集區中的適當伺服器。

1. 在您的私人雲端中，建立虛擬機器集區來代表 web 伺服陣列。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="VMSphere 用戶端中頁面的螢幕擷取畫面，其中顯示另一部 VM 的摘要。":::

    已使用已安裝 IIS 角色的 Windows Server 2016 來說明此教學課程。 一旦安裝 Vm 之後，請執行下列 PowerShell 命令，為每個 VM 教學課程設定 IIS。 

    第一部虛擬機器（contoso-web-01）將裝載主要網站。

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    第二部虛擬機器（contoso-web-02）將裝載映射網站。
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    第三個虛擬機器（contoso-web-03）將裝載影片網站。

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. 將三個新的後端集區新增至現有的應用程式閘道實例。 

   1. 從左側功能表中中選取 [後端集區]。 
   1. 選取 [ **新增** ]，然後輸入第一個集區 **contoso web** 的詳細資料。 
   1. 新增一部 VM 作為目標。 
   1. 選取 [新增]  。 
   1. 針對 **contoso-images** 和 **contoso-video** 重複此程式，並新增一個唯一的 VM 作為目標。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="[後端集區] 頁面的螢幕擷取畫面，其中顯示三個新的後端集區。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. **在 [接聽** 程式] 區段中，使用通訊埠8080建立 [基本] 類型的新接聽程式。

4. 在左側導覽中，選取 [ **HTTP 設定** ]，然後選取左窗格中的 [ **新增** ]。 填入詳細資料以建立新的 HTTP 設定，然後選取 [ **儲存** ]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="[新增 HTTP 設定] 頁面的螢幕擷取畫面，其中顯示 HTTP 設定的設定。":::

5. 在左側功能表的 [ **規則** ] 區段中建立規則。 將每個規則與先前建立的接聽程式產生關聯。 然後設定主要後端集區和 HTTP 設定。 選取 [新增]  。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="[新增路由規則] 頁面的螢幕擷取畫面，可將路由規則設定為後端目標。":::

6. 測試設定。 存取 Azure 入口網站上的應用程式閘道，並在 [ **總覽** ] 區段中複製公用 IP 位址。 

   1. 開啟新的瀏覽器視窗，並輸入 URL `http://<app-gw-ip-address>:8080` 。 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="顯示成功測試設定的瀏覽器頁面螢幕擷取畫面。":::

   1. 將 URL 變更為 `http://<app-gw-ip-address>:8080/images/test.htm`。

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="另一個成功測試的螢幕擷取畫面，其中包含新的 URL。":::

   1. 請再次將 URL 變更為 `http://<app-gw-ip-address>:8080/video/test.htm` 。

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="具有最終 URL 的成功測試螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

如需更多設定範例，請參閱 [Azure 應用程式閘道檔](../application-gateway/index.yml) 。