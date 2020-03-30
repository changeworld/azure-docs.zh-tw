---
title: 部署 Azure 防火牆管理器受信任的安全合作夥伴
description: 瞭解如何使用 Azure 門戶部署 Azure 防火牆管理器受信任的安全性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931309"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>部署信任的安全性合作夥伴 (預覽)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 防火牆管理器中的*可信安全合作夥伴*允許您使用您熟悉的、同類最佳的協力廠商安全即服務 （SECaaS） 產品來保護使用者的 Internet 訪問。

要瞭解有關受支援方案和最佳實踐指南的更多內容，請參閱[什麼是受信任的安全合作夥伴（預覽）？](trusted-security-partners.md)

支援的安全合作夥伴是**ZScaler**和**iboss。** 這些預覽。 支援的區域是西中環、北中美、西US、西US2和東US。

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

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新中心中部署協力廠商安全提供程式

1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 在 **"搜索"** 中，鍵入**防火牆管理器**並在**服務**下選擇它。
3. 導航到**入門**。 選擇**創建安全虛擬中心**。 
4. 輸入訂閱和資源組，選擇受支援的區域，然後添加中心和虛擬廣域網路資訊。 
5. 預設情況下，**部署 VPN 閘道**已啟用。 在集線器中部署受信任的安全夥伴需要 VPN 閘道。 
6. 選擇**下一步：Azure 防火牆**
   > [!NOTE]
   > 受信任的安全合作夥伴使用 VPN 閘道隧道連接到您的集線器。 如果刪除 VPN 閘道，則與受信任安全合作夥伴的連接將丟失。
7. 如果要部署 Azure 防火牆以篩選專用流量以及協力廠商服務提供者以篩選 Internet 流量，請選擇 Azure 防火牆的策略。 請參閱[受支援的方案](trusted-security-partners.md#key-scenarios)。
8. 如果只想在中心部署協力廠商安全提供程式，請選擇**Azure 防火牆：已啟用/禁用**以將其設置為 **"已禁用**"。 
9. 選擇 **"下一步"：受信任的安全合作夥伴**。
10. 選擇 **"受信任的安全合作夥伴**"將其設置為 **"已啟用**"。 選擇合作夥伴。 
11. 選取 [下一步]****。 
12. 查看內容，然後選擇 **"創建**"。

VPN 閘道部署可能需要 30 分鐘以上。

要驗證中心是否已創建，請導航到 Azure 防火牆管理器>安全中心。 選擇中心>概述頁面以顯示合作夥伴名稱和狀態為 **"安全連線掛起**"。

創建集線器並設置安全夥伴後，繼續將安全提供程式連接到集線器。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在現有中心部署協力廠商安全提供程式

您還可以在虛擬 WAN 中選擇現有集線器並將其轉換為*安全的虛擬集線器*。

1. 在**入門時**，選擇 **"轉換現有集線器**"。
2. 選擇訂閱和現有中心。 按照其餘步驟在新中心部署協力廠商提供程式。

請記住，必須部署 VPN 閘道才能將現有集線器轉換為具有協力廠商供應商的安全集線器。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>配置協力廠商安全提供程式以連接到安全集線器

要設置虛擬中心 VPN 閘道的隧道，協力廠商供應商需要訪問您的中心的許可權。 為此，請將服務主體與您的訂閱或資源組相關聯，並授予存取權限。 然後，您必須使用協力廠商門戶將這些憑據授予協力廠商。

1. 創建 Azure 活動目錄 （AD） 服務主體：可以跳過重定向 URL。 

   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 添加服務主體的存取權限和作用域。
   [操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 您可以僅限制對資源組的訪問，以便進行更精細的控制。
3. 按照[ZScaler：配置 Microsoft Azure 虛擬 WAN 集成](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)說明，以便：

   - 登錄到合作夥伴門戶並添加憑據，以便授予受信任的合作夥伴對安全中心的許可權。
   - 同步合作夥伴門戶中的虛擬中心，並將隧道設置為虛擬中心。 驗證 Azure AD 身份驗證憑據後，可以執行此操作。
   
4. 您可以在 Azure 中的 Azure 虛擬 WAN 門戶上查看隧道創建狀態。 隧道在 Azure 和合作夥伴門戶上顯示**連接**後，繼續執行後續步驟以設置路由，以選擇哪些分支和 VNet 應向合作夥伴發送 Internet 流量。

## <a name="configure-route-settings"></a>配置路由設置

1. 流覽到 Azure 防火牆管理器 ->安全集線器。 
2. 選擇中心。 中心狀態現在應顯示**預配**，而不是**安全連線掛起**。

   確保協力廠商供應商可以連接到集線器。 VPN 閘道上的隧道應處於**連接**狀態。 與以前的狀態相比，此狀態更能反映集線器與協力廠商合作夥伴之間的連接運行狀況。
3. 選擇中心，然後導航到**路由設置**。

   將協力廠商提供程式部署到中心時，它會將中心轉換為*安全的虛擬中心*。 這可確保協力廠商供應商向中心通告 0.0.0.0/0（預設）路由。 但是，VNet 連接和連接到集線器的網站不會獲取此路由，除非您選擇在哪些連接上獲取此預設路由。
4. 在**互聯網流量**下，選擇**VNet 到 Internet**或**分支到 Internet，** 或者通過協力廠商配置路由。

   這僅指示應路由到集線器的流量類型，但它尚未影響 VNet 或分支上的路由。 預設情況下，這些路由不會傳播到連接到中心的所有 VNet/分支。
5. 您必須選擇**安全連線**並選擇應設置這些路由的連接。 這表示哪些 VNet/分支可以開始向協力廠商供應商發送 Internet 流量。
6. 從**路由設置**中，選擇 Internet 流量下**的安全連線**，然後選擇要保護的 VNet 或分支（虛擬 WAN 中的*網站*）。 選擇**安全互聯網流量**。
   ![安全的互聯網流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 導航回集線器頁面。 集線器的**受信任安全夥伴**狀態現在應該**是安全的**。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>通過協力廠商服務的分支機搆或 VNet 互聯網流量

接下來，您可以檢查 VNet 虛擬機器或分支網站是否可以訪問 Internet，並驗證流量是否流向協力廠商服務。

完成路由設置步驟後，VNet 虛擬機器以及分支網站將發送 0/0 到協力廠商服務路由。 不能將 RDP 或 SSH 引入這些虛擬機器。 要登錄，可以在對等 VNet 中部署[Azure 堡壘](../bastion/bastion-overview.md)服務。

## <a name="next-steps"></a>後續步驟

- [教程：使用 Azure 門戶使用 Azure 防火牆管理器預覽保護雲網路](secure-cloud-network.md)




