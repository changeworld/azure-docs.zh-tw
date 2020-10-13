---
title: 部署 Azure 防火牆管理員安全性合作夥伴提供者
description: 瞭解如何使用 Azure 入口網站部署 Azure 防火牆管理員的安全性合作夥伴提供者。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3323f73c137905fbe677c68d3830d7f609fa0172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611572"
---
# <a name="deploy-a-security-partner-provider"></a>部署安全性合作夥伴提供者

Azure 防火牆管理員中的*安全性合作夥伴提供者*，可讓您使用熟悉、最知名的協力廠商安全性即服務 (SECaaS) 供應專案，以保護使用者的網際網路存取。

若要深入瞭解支援的案例和最佳做法指導方針，請參閱 [什麼是安全性合作夥伴提供者？](trusted-security-partners.md)


整合的協力廠商安全性即服務 (SECaaS) 合作夥伴現在可在所有 Azure 公用雲端區域中使用。 **Zscaler** 整合將于2020年7月3日正式推出。 **Check Point** 是支援的 SECaaS 合作夥伴，將于2020年7月3日推出預覽。 **iboss** 整合將于2020年7月31日正式推出。

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新的中樞部署協力廠商安全性提供者

如果您要將協力廠商提供者部署至現有的中樞，請略過本節。

1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 在 [ **搜尋**] 中輸入 **防火牆管理員** ，並在 [ **服務**] 下選取它。
3. 流覽至 **消費者入門**。 選取 [ **View 安全虛擬中樞**]。
4. 選取 [ **建立新的安全虛擬中樞**]。
5. 輸入您的訂用帳戶和資源群組、選取支援的區域，然後新增您的中樞和虛擬 WAN 資訊。 
6. 選取 [ **包含 VPN 閘道] 以啟用安全性合作夥伴提供者**。
7. 選取適合您需求的 **閘道縮放單位** 。
8. 選取 **下一個： Azure 防火牆**
   > [!NOTE]
   > 安全性合作夥伴提供者會使用 VPN 閘道通道來連線到您的中樞。 如果您刪除 VPN 閘道，則會遺失安全性夥伴提供者的連線。
9. 如果您想要部署 Azure 防火牆來篩選私人流量，以及使用協力廠商服務提供者來篩選網際網路流量，請選取 Azure 防火牆的原則。 請參閱 [支援的案例](trusted-security-partners.md#key-scenarios)。
10. 如果您只想在中樞部署協力廠商安全性提供者，請選取 [ **Azure 防火牆：已啟用/已停用** ]，將其設定為 **停用**。 
11. 選取  **[下一步：安全性合作夥伴提供者]**。
12. 將 **安全性合作夥伴提供者** 設定為 **啟用**。 
13. 選取合作夥伴。 
14. 選取 [下一步：檢閱 + 建立]。 
15. 檢查內容，然後選取 [ **建立**]。

VPN 閘道部署可能需要30分鐘以上的時間。

若要確認已建立中樞，請流覽至 Azure 防火牆管理員 >安全中樞。 選取 [中樞 >總覽] 頁面，以顯示夥伴名稱和狀態為 [ **擱置安全性連接**]。

建立中樞並設定安全性合作夥伴之後，請繼續將安全性提供者連接到中樞。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在現有的中樞內部署協力廠商安全性提供者

您也可以在虛擬 WAN 中選取現有的中樞，並將其轉換為 *安全的虛擬中樞*。

1. 在 **消費者入門**中，選取 [ **View 安全虛擬中樞**]。
2. 選取 [ **轉換現有的中樞**]。
3. 選取訂用帳戶和現有的中樞。 遵循其餘的步驟，在新的中樞部署協力廠商提供者。

請記住，必須部署 VPN 閘道，才能使用協力廠商提供者將現有中樞轉換成安全中樞。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>設定協力廠商安全性提供者以連接到安全的中樞

若要設定虛擬中樞 VPN 閘道的通道，協力廠商提供者需要您中樞的存取權。 若要這樣做，請將服務主體與您的訂用帳戶或資源群組建立關聯，並授與存取權限。 然後，您必須使用入口網站將這些認證提供給協力廠商。

### <a name="create-and-authorize-a-service-principal"></a>建立及授權服務主體

1. 建立 Azure Active Directory (AD) 服務主體：您可以略過重新導向 URL。 

   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. 新增服務主體的存取權限和範圍。
   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > 您可以限制只有資源群組的存取權，才能進行更細微的控制。

### <a name="visit-partner-portal"></a>造訪合作夥伴入口網站

1. 請依照您的合作夥伴提供的指示來完成設定。 這包括提交 AAD 資訊以偵測並聯機至中樞、更新輸出原則，以及檢查線上狀態和記錄。

   - [Zscaler：設定 Microsoft Azure 的虛擬 WAN 整合](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)。
   - [Check Point (preview) ：設定 Microsoft Azure 的虛擬 WAN 整合](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm)。
   - [iboss (preview) ：設定 Microsoft Azure 的虛擬 WAN 整合](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security)。 
   
2. 您可以在 Azure 中的 Azure 虛擬 WAN 入口網站上查看通道建立狀態。 一旦通道顯示在 Azure 和合作夥伴入口網站上 **連線** ，請繼續進行後續步驟來設定路由，以選取要將網際網路流量傳送給夥伴的分支和 vnet。

## <a name="configure-route-settings"></a>設定路由設定

1. 流覽至 Azure 防火牆管理員 > 安全中樞。 
2. 選取中樞。 中樞狀態現在應該會顯示已布 **建** ，而不是 **擱置安全性**連線。

   確定協力廠商提供者可以連接到中樞。 VPN 閘道上的通道應該處於 **已連線** 狀態。 相較于先前的狀態，此狀態會更反映中樞與協力廠商夥伴之間的連線健康情況。
3. 選取中樞，然後流覽至 [ **路由設定**]。

   當您將協力廠商提供者部署到中樞時，它會將中樞轉換成 *安全虛擬中樞*。 這可確保協力廠商提供者會通告 0.0.0.0/0 (預設) 路由至中樞。 不過，連線到中樞的 VNet 連線和網站無法取得此路由，除非您選擇要讓哪些連線取得此預設路由。
4. 在 [ **網際網路流量**] 下，選取 [ **VNet 對網際網路** ] 或 [ **分支對網際網路** ]，或兩者都設定為透過協力廠商傳送。

   這只會指出應將何種類型的流量路由傳送至中樞，但不會影響 Vnet 或分支上的路由。 依預設，這些路由不會傳播到連接至中樞的所有 Vnet/分支。
5. 您必須選取 [ **安全連線** ]，然後選取應該設定這些路由的連接。 這表示哪些 Vnet/分支可以開始將網際網路流量傳送給協力廠商提供者。
6. 在 [ **路由設定**] 中，選取 [網際網路流量] 下的 [ **安全** 連線]，然後選取要保護之虛擬 WAN) 中的 VNet 或分支 (*網站* 。 選取 [ **安全的網際網路流量**]。
   ![保護網際網路流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 流覽回到 [中樞] 頁面。 中樞的 **安全性合作夥伴提供者** 狀態現在應該會受到  **保護**。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>透過協力廠商服務的分支或 VNet 網際網路流量

接下來，您可以檢查 VNet 虛擬機器或分支網站是否可以存取網際網路，並驗證流量是否流經協力廠商服務。

完成路由設定步驟之後，VNet 虛擬機器和分支網站會傳送0/0 至協力廠商服務路由。 您無法透過 RDP 或 SSH 連線到這些虛擬機器。 若要登入，您可以在對等互連 VNet 中部署 [Azure](../bastion/bastion-overview.md) 防禦服務。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站以 Azure 防火牆管理員保護您的雲端網路](secure-cloud-network.md)