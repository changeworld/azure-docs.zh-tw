---
title: 在 Azure VM 上建立叢集的基礎結構
description: 在本教學課程中，您將了解如何設定用來執行 Service Fabric 叢集的 Azure VM 基礎結構。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614004"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>教學課程：建立用來裝載 Service Fabric 叢集的 Azure VM 基礎結構

Service Fabric 獨立叢集讓您能夠選擇自己的環境，並且在 Service Fabric 所採用的「任何 OS、任何雲端」方法中建立叢集。 在此教學課程系列中，您會建立裝載於 Azure VM 的獨立叢集，並在其中安裝應用程式。

本教學課程是一個系列的第一部分。 在本文中，您會產生要裝載 Service Fabric 的獨立叢集所需的 Azure VM 資源。 在後續的文章中，您將必須安裝 Service Fabric 獨立套件、將範例應用程式安裝到您的叢集中，最後清除您的叢集。

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立一組 AzureVM 執行個體
> * 修改安全性群組
> * 登入其中一個執行個體
> * 準備 Service Fabric 的執行個體

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要 Azure 訂用帳戶。  如果您還沒有帳戶，請移至 [Azure 入口網站](https://portal.azure.com)建立一個。

## <a name="create-azure-virtual-machine-instances"></a>建立 Azure 虛擬機器執行個體

1. 登入 Azure 入口網站，然後選取 [虛擬機器]  (而不是 [虛擬機器 (傳統)])。

   ![Azure 入口網站 VM][az-console]

2. 選取 [新增]  按鈕，開啟 [建立虛擬機器]  表單。

3. 在 [基本]  索引標籤中，確定選擇您想要的訂用帳戶和資源群組 (建議使用新的資源群組)。

4. 將 [映像]  類型變更為 [Windows Server 2016 Datacenter]  。 
 
5. 將執行個體 [大小]  變更為 [標準 DS2 v2]  。 設定系統管理員**使用者名稱**和**密碼**，記下它們的值。

6. 目前將 [輸入連接埠規則]  保持為封鎖，我們會在下一節中設定這些項目。

7. 在 [網路]  索引標籤中，建立新的**虛擬網路**，並且記下它的名稱。

8. 接下來，將 [NIC 網路安全性群組]  設定為 [進階]  。 建立新的安全性群組，記下它的名稱，並且建立下列規則以允許來自任何來源的 TCP 流量：

   ![sf 輸入][sf-inbound]

   * 連接埠 `3389`，適用於 RDP 和 ICMP (基本連線)。
   * 連接埠 `19000-19003`，適用於 Service Fabric。
   * 連接埠 `19080-19081`，適用於 Service Fabric。
   * 連接埠 `8080`，適用於網頁瀏覽器要求。

   > [!TIP]
   > 若要一併連接 Service Fabric 中的虛擬機器，裝載基礎結構的 VM 必須具有相同的認證。  有兩個常見的方法可用來取得一致的認證：將虛擬機器全部加入相同的網域，或在每個 VM 上設定相同的系統管理員密碼。 幸好 Azure 可以讓相同**虛擬網路**上的所有虛擬機器輕易地連線，因此我們可以確定擁有相同網路上的所有執行個體。

9. 新增另一個規則。 將來源設定為 [服務標籤]  ，並且將來源服務標籤設定為 [VirtualNetwork]  。 Service Fabric 需要下列連接埠開啟，以便在叢集內進行通訊：135、137-139、445、20001-20031、20606-20861。

   ![vnet 輸入][vnet-inbound]

10. 其他選項的預設狀態是可接受的。 如果您想要的話可以檢閱它們，然後啟動您的虛擬機器。

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>為您的 Service Fabric 叢集建立更多執行個體

啟動另外兩部**虛擬機器**，務必維持上一節中概述的相同設定。 特別是維持相同的系統管理員名稱和密碼。 不應該建立**虛擬網路**和 **NIC 網路安全性群組**；從下拉式功能表選取您已建立的項目。 可能需要幾分鐘讓您的每個執行個體進行部署。

## <a name="connect-to-your-instances"></a>連線至您的執行個體

1. 從 [虛擬機器]  區段選取其中一個執行個體。

2. 在 [概觀]  索引標籤中，記下*私人* IP 位址。 然後按一下 [連線]  。

3. 在 [RDP]  索引標籤中，請注意我們使用的是稍早特別開啟的公用 IP 位址和連接埠 3389。 下載 RDP 檔案。
 
4. 開啟 RDP 檔案，在系統提示時輸入您在 VM 設定中提供的使用者名稱和密碼。

5. 一旦您連線到執行個體，您必須驗證遠端登錄已執行、啟用 SMB，並開啟為 SMB 和遠端登錄必要的連接埠。

   若要啟用 SMB，PowerShell 命令如下：

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. 若要在防火牆中開啟連接埠，應使用下列 PowerShell 命令：

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. 針對您的其他執行個體重複此程序，再次記下私人 IP 位址。

## <a name="verify-your-settings"></a>確認您的設定

1. 若要驗證基本連線能力，請使用 RDP 連線至其中一部 VM。

2. 從該 VM 開啟 [命令提示字元]  ，然後使用 ping 命令從一部 VM 連線到另一部，使用您稍早記下的其中一個私人 IP 位址 (而不是您已連線 VM 的 IP) 取代以下 IP 位址。

   ```
   ping 172.31.20.163
   ```

   如果您的輸出看起來像是 `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` 重複四次，表示執行個體之間的連線運作正常。

3. 此時，請使用下列命令驗證您的 SMB 共用可運作：

   ```
   net use * \\172.31.20.163\c$
   ```

   它應傳回 `Drive Z: is now connected to \\172.31.20.163\c$.` 這樣的輸出。


   現在您的執行個體已適當地針對 Service Fabric 做好準備。

## <a name="next-steps"></a>後續步驟

在此系列的第一個單元中，您已了解如何啟動三個 Azure VM 執行個體並加以設定，以進行 Service Fabric 安裝：

> [!div class="checklist"]
> * 建立一組 Azure VM 執行個體
> * 修改安全性群組
> * 登入其中一個執行個體
> * 準備 Service Fabric 的執行個體

請繼續進行此系列的第二個部分，以在叢集上設定 Service Fabric。

> [!div class="nextstepaction"]
> [安裝 Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
