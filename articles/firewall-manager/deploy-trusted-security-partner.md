---
title: 部署 Azure 防火牆管理員信任的安全性合作夥伴
description: 瞭解如何使用 Azure 入口網站部署 Azure 防火牆管理員的受信任安全性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73931309"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>部署信任的安全性合作夥伴 (預覽)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 防火牆管理員中*受信任的安全性合作夥伴*可讓您使用熟悉、最適合的協力廠商安全性即服務（SECaaS）供應專案，來保護您的使用者的網際網路存取。

若要深入瞭解支援的案例和最佳作法指導方針，請參閱[什麼是信任的安全性合作夥伴（預覽）？](trusted-security-partners.md)。

支援的安全性合作夥伴為此預覽版的**ZScaler**和**iboss** 。 支援的區域為 WestCentralUS、NorthCentralUS、WestUS、WestUS2 和 EastUS。

## <a name="prerequisites"></a>Prerequisites

> [!IMPORTANT]
> 您必須使用 `Register-AzProviderFeature` PowerShell 命令，明確地啟用 Azure 防火牆管理員預覽。

從 PowerShell 命令提示字元執行下列命令：

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
需要 30 分鐘才能完成功能註冊。 執行下列命令以檢查您的註冊狀態：

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新的中樞部署協力廠商安全性提供者

1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 在 [**搜尋**] 中，輸入**防火牆管理員**，然後在 [**服務**] 底下選取它。
3. 流覽至**消費者入門**。 選取 [**建立安全的虛擬中樞**]。 
4. 輸入您的訂用帳戶和資源群組，並選取支援的區域，然後新增您的中樞和虛擬 WAN 資訊。 
5. 預設會啟用 [**部署 VPN 閘道**]。 需要有 VPN 閘道，才能在中樞部署受信任的安全性合作夥伴。 
6. 選取 **[下一步]： Azure 防火牆**
   > [!NOTE]
   > 受信任的安全性合作夥伴會使用 VPN 閘道通道連接到您的中樞。 如果您刪除 VPN 閘道，與受信任的安全性合作夥伴的連線就會遺失。
7. 如果您想要部署 Azure 防火牆來篩選私人流量，並使用協力廠商服務提供者來篩選網際網路流量，請選取 Azure 防火牆的原則。 請參閱[支援的案例](trusted-security-partners.md#key-scenarios)。
8. 如果您只想要在中樞部署協力廠商安全性提供者，請選取 [ **Azure 防火牆：啟用/停用**]，將其設定為 [**停用**]。 
9. 選取 **[下一步：信任的安全性合作夥伴]**。
10. 選取 [**信任的安全性合作夥伴**]，將其設定為 [**已啟用**]。 選取合作夥伴。 
11. 選取 [下一步]  。 
12. 檢查內容，然後選取 [**建立**]。

VPN 閘道部署可能需要超過30分鐘的時間。

若要確認中樞是否已建立，請流覽至 Azure 防火牆管理員->安全的中樞。 選取 [中樞->總覽] 頁面，將 [夥伴名稱] 和 [狀態] 顯示為 [**安全性連接擱置**中]。

建立中樞並設定安全性夥伴之後，請繼續將安全性提供者連線到中樞。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在現有的中樞中部署協力廠商安全性提供者

您也可以選取虛擬 WAN 中的現有中樞，並將其轉換為*受保護的虛擬中樞*。

1. 在**消費者入門**中，選取 [**轉換現有的中樞**]。
2. 選取訂用帳戶和現有的中樞。 遵循其餘的步驟，在新的中樞部署協力廠商提供者。

請記住，必須部署 VPN 閘道，才能將現有的中樞轉換成具有協力廠商提供者的安全中樞。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>設定協力廠商安全性提供者以連線到安全的中樞

若要設定虛擬中樞 VPN 閘道的通道，協力廠商提供者需要中樞的存取權限。 若要這麼做，請將服務主體與您的訂用帳戶或資源群組建立關聯，並授與存取權限。 接著，您必須使用其入口網站，將這些認證提供給協力廠商。

1. 建立 Azure Active Directory （AD）服務主體：您可以略過重新導向 URL。 

   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 新增服務主體的存取權和範圍。
   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 您可以限制只有資源群組的存取權，以進行更細微的控制。
3. 請遵循[ZScaler：設定 Microsoft Azure 的虛擬 WAN 整合](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)指示來執行下列動作：

   - 登入合作夥伴入口網站，並新增您的認證，以授與受信任的合作夥伴對您安全中樞的存取權。
   - 在合作夥伴入口網站中同步處理虛擬中樞，並設定虛擬中樞的通道。 您可以在驗證 Azure AD 驗證認證後執行此動作。
   
4. 您可以在 azure 中的 Azure 虛擬 WAN 入口網站上查看通道建立狀態。 一旦通道顯示 Azure 和合作夥伴入口網站上的**連線**，請繼續進行後續步驟來設定路由，以選取哪些分支和 vnet 應將網際網路流量傳送給夥伴。

## <a name="configure-route-settings"></a>設定路由設定

1. 流覽至 Azure 防火牆管理員-> 安全的中樞。 
2. 選取中樞。 中樞狀態現在應該會顯示 [已布**建**]，而非 [**安全性連線擱置**中]。

   確定協力廠商提供者可以連接到中樞。 VPN 閘道上的通道應處於**連線**狀態。 相較于先前的狀態，此狀態更反映中樞與協力廠商夥伴之間的連線健全狀況。
3. 選取中樞，然後流覽至 [**路由設定**]。

   當您將協力廠商提供者部署至中樞時，它會將中樞轉換成*受保護的虛擬中樞*。 這可確保協力廠商提供者通告中樞的 0.0.0.0/0 （預設）路由。 不過，除非您選擇要取得此預設路由的連線，否則連接到中樞的 VNet 連接和網站不會取得此路由。
4. 在 [**網際網路流量**] 底下，選取 [ **VNet 對網際網路**] 或 [**分支對網際網路**]，或兩者都設定為 [透過協力廠商傳送]。

   這只會指出應該將哪種類型的流量路由傳送至中樞，但它還不會影響 Vnet 或分支上的路由。 根據預設，這些路由不會傳播到連接到中樞的所有 Vnet/分支。
5. 您必須選取 [**安全連線**]，然後選取應設定這些路由的連接。 這表示哪些 Vnet/分支可以開始傳送網際網路流量給協力廠商提供者。
6. 從 [**路由設定**] 中，選取 [網際網路流量] 底下的 [**安全連線**]，然後選取要保護的 VNet 或分支（虛擬 WAN 中的*網站*）。 選取 [**安全的網際網路流量**]。
   ![保護網際網路流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 流覽回到 [中樞] 頁面。 中樞的**受信任安全性夥伴**狀態現在應該會受到**保護**。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>透過協力廠商服務的分支或 VNet 網際網路流量

接下來，您可以檢查 VNet 虛擬機器或分支網站是否可以存取網際網路，並驗證流量是否流向協力廠商服務。

完成路由設定步驟之後，VNet 虛擬機器和分支網站會傳送0/0 給協力廠商服務路由。 您無法透過 RDP 或 SSH 連線到這些虛擬機器。 若要登入，您可以在對等互連 VNet 中部署[Azure](../bastion/bastion-overview.md)防禦服務。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站在 Azure 防火牆管理員預覽中保護您的雲端網路](secure-cloud-network.md)




