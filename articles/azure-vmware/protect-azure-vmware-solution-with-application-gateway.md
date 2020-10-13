---
title: 使用 Azure 應用程式閘道來保護 Azure VMware 解決方案上的 web 應用程式
description: 設定 Azure 應用程式閘道，以安全地公開在 Azure VMware 解決方案上執行的 web 應用程式。
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: b63b7348419500504cb45917d741cc17e2b8a622
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951446"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>使用 Azure 應用程式閘道來保護 Azure VMware 解決方案上的 web 應用程式

[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 是第7層 web 流量負載平衡器，可讓您管理 web 應用程式的流量。 它提供許多功能：以 cookie 為基礎的會話親和性、以 URL 為基礎的路由，以及 Web 應用程式防火牆 (WAF) 來命名。  (如需完整的功能清單，請參閱 [Azure 應用程式閘道功能](../application-gateway/features.md)。 ) 它提供兩種版本： v1 和 v2。 兩者都已經過在 Azure VMware 解決方案上執行的 web apps 進行測試。

在本文中，我們將逐步解說在 web 伺服器陣列前面使用應用程式閘道的常見案例，並提供一組設定和建議，以保護在 Azure VMware 解決方案上執行的 web 應用程式。 

## <a name="topology"></a>拓撲
如下圖所示，應用程式閘道可以用來保護 Azure IaaS 虛擬機器、Azure 虛擬機器擴展集或內部部署伺服器。 應用程式閘道會將 Azure VMware 解決方案虛擬機器視為內部部署伺服器。

![應用程式閘道可保護 Azure VMware 解決方案 Vm。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure 應用程式閘道目前是公開在 Azure VMware 解決方案虛擬機器上執行之 web 應用程式的唯一支援方法。

下圖顯示用來驗證使用 Azure VMware 解決方案 web 應用程式之應用程式閘道的測試案例。

![應用程式閘道與執行 web apps 的 Azure VMware 解決方案整合。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

應用程式閘道實例會部署在中樞的專用子網中。 它有 Azure 公用 IP 位址;建議您啟用虛擬網路的標準 DDoS 保護。 Web 服務器裝載于位於 NSX T0 和 T1 路由器後方的 Azure VMware 解決方案私用雲端。 Azure VMware 解決方案使用 [ExpressRoute 全球存取範圍](../expressroute/expressroute-global-reach.md) ，以啟用與中樞和內部部署系統的通訊。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。
- 已部署並正在執行的 Azure VMware 解決方案私人雲端。

## <a name="deployment-and-configuration"></a>部署和設定

1. 在 Azure 入口網站中，搜尋 **應用程式閘道** ，然後選取 [ **建立應用程式閘道**]。

2. 提供基本的詳細資料，如下圖所示：然後選取 **[下一步：前端>] **。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="應用程式閘道建立":::

3. 選擇前端 IP 位址類型。 若為公用，請選擇現有的公用 IP 位址，或建立一個新的位址。 選取 **[下一步：後端>] **。

    > [!NOTE]
    > 私人前端只支援標準和 Web 應用程式防火牆 (WAF) Sku。

4. 接下來，新增後端集區，其中會描述屬於應用程式或服務的一組實例， (在此案例中是在 Azure VMware 解決方案基礎結構) 上執行的虛擬機器。 提供在 Azure VMware 解決方案私人雲端上執行之 web 伺服器的詳細資料，然後選取 [ **新增**];然後選取 **[下一步：設定>] **。

1. **在 [設定**] 索引標籤上，選取 [**新增路由規則**]。

6. **在 [接聽程式] 索引**標籤上，提供接聽程式的詳細資料。 如果選取 HTTPS，則必須從 PFX 檔案或 Azure Key Vault 的現有憑證提供憑證。 

7. 選取 [ **後端目標** ] 索引標籤，然後選取先前建立的後端集區。 針對 [ **HTTP 設定** ] 欄位，選取 [ **加入新**的]。

8. 設定 HTTP 設定的參數。 選取 [新增]。

9. 如果您想要設定以路徑為基礎的規則，請選取 [ **新增多個目標]，以建立以路徑為基礎的規則**。 

10. 新增以路徑為基礎的規則，然後選取 [ **新增**]。 重複以新增以路徑為基礎的其他規則。 

11. 當您完成新增以路徑為基礎的規則時，請再次選取 [ **新增** ];然後選取 **[下一步：標記>] **。 

12. 新增任何所需的標記。 選取 **[下一步：檢查 + 建立>] **。

13. 驗證將會在您的應用程式閘道上執行;如果成功，請選取 [ **建立** ] 以部署。

## <a name="configuration-examples"></a>組態範例

在本節中，您將瞭解如何使用 Azure VMware 解決方案虛擬機器，將應用程式閘道設定為下列使用案例的後端集區： 

- [裝載多個網站](#hosting-multiple-sites)
- [依 URL 路由](#routing-by-url)

### <a name="hosting-multiple-sites"></a>裝載多個網站

當您建立應用程式閘道時，可以使用 Azure 入口網站設定裝載多個網站。 在本教學課程中，您可以使用現有應用程式閘道上的 Azure VMware 解決方案私人雲端上執行的虛擬機器，來定義後端位址集區。 應用程式閘道是中樞虛擬網路的一部分，如 [將 Azure VMware 解決方案整合到中樞和輪輻架構](concepts-hub-and-spoke.md)中所述。 本教學課程假設您擁有多個網域，並使用 www.contoso.com 和 www.fabrikam.com 的範例。

1. 建立虛擬機器。 在 Azure VMware 解決方案私人雲端上，建立兩個不同的虛擬機器集區;其中一個會代表 Contoso 和第二個 Fabrikam。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="應用程式閘道建立":::

    為了說明本教學課程，我們使用了 Windows Server 2016 搭配 Internet Information Services (安裝的 IIS) 角色。 一旦安裝虛擬機器之後，請執行下列 PowerShell 命令，在每個 Vm 上設定 IIS。 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. 新增後端集區。 在現有的應用程式閘道實例中，從左側功能表中選取 [ **後端** 集區]  **，選取 [新增]**，然後輸入新集區的詳細資料。 選取右窗格中的 [ **新增** ]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="應用程式閘道建立" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. **在 [接聽**程式] 區段中，為每個網站建立新的接聽程式。 輸入每個接聽程式的詳細資料，然後選取 [ **新增**]。

4. 在左側導覽中，選取 [ **HTTP 設定** ]，然後選取左窗格中的 [ **新增** ]。 填入詳細資料以建立新的 HTTP 設定，然後選取 [ **儲存**]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="應用程式閘道建立" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. 在左側功能表的 [ **規則** ] 區段中建立規則。 將每個規則與對應的接聽程式產生關聯。 選取 [新增]。

6. 設定對應的後端集區和 HTTP 設定。 選取 [新增]。

7. 測試連接。 開啟您慣用的瀏覽器，並流覽至裝載于 Azure VMware 解決方案環境的不同網站 http://www.fabrikam.com 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="應用程式閘道建立":::

### <a name="routing-by-url"></a>依 URL 路由

您可以使用 Azure 應用程式閘道來設定以 URL 路徑為基礎的路由規則。 在本教學課程中，您可以使用現有應用程式閘道上的 Azure VMware 解決方案私人雲端上執行的虛擬機器，來定義後端位址集區。 應用程式閘道是中樞虛擬網路的一部分，如 [Azure VMware 解決方案 Azure 原生整合檔](concepts-hub-and-spoke.md)中所述。 然後，您可以建立路由規則，確保 Web 流量會抵達集區中的適當伺服器。

1. 建立虛擬機器。 在 Azure VMware 解決方案私人雲端上，建立虛擬機器的集區來代表 web 伺服陣列。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="應用程式閘道建立"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. 新增後端集區。 您將需要在現有的應用程式閘道實例中新增三個新的後端集區。 從左側功能表中中選取 [後端集區]。 選取 [ **新增** ]，然後輸入第一個集區 **contoso web**的詳細資料。 新增一部 VM 作為目標。 選取 [新增]。 針對 **contoso-images** 和 **contoso-video**重複此程式，並將一個唯一的 VM 新增至每個作為目標。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="應用程式閘道建立" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. **在 [接聽**程式] 區段中，使用通訊埠8080建立 [基本] 類型的新接聽程式。

4. 在左側導覽中，選取 [ **HTTP 設定** ]，然後選取左窗格中的 [ **新增** ]。 填入詳細資料以建立新的 HTTP 設定，然後選取 [ **儲存**]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="應用程式閘道建立":::

5. 在左側功能表的 [ **規則** ] 區段中建立規則。 將每個規則與先前建立的接聽程式產生關聯。 然後設定主要後端集區和 HTTP 設定。 選取 [新增]。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="應用程式閘道建立":::

6. 測試設定。 存取 Azure 入口網站上的應用程式閘道，並在 [ **總覽** ] 區段中複製公用 IP 位址。 然後開啟新的瀏覽器視窗，並輸入 URL `http://<app-gw-ip-address>:8080` 。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="應用程式閘道建立":::

    將 URL 變更為 `http://<app-gw-ip-address>:8080/images/test.htm`。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="應用程式閘道建立":::

    請再次將 URL 變更為 `http://<app-gw-ip-address>:8080/video/test.htm` 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="應用程式閘道建立":::

## <a name="next-steps"></a>後續步驟

如需更多設定範例，請參閱 [Azure 應用程式閘道檔](../application-gateway/index.yml) 。