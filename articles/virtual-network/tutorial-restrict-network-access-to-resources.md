---
title: 限制對 PaaS 資源的存取 -教學課程 - Azure 入口網站
description: 在本教學課程中，您將了解如何使用 Azure 入口網站透過虛擬網路服務端點來限制對 Azure 資源 (例如 Azure 儲存體和 Azure SQL Database) 的網路存取。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368239"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>教學課程：透過使用 Azure 入口網站的虛擬網路服務端點來限制對 PaaS 資源的網路存取

虛擬網路服務端點可讓您限制某些 Azure 服務資源對虛擬網路子網路的網路存取。 您也可以移除對資源的網際網路存取。 服務端點提供從您虛擬網路到受支援 Azure 服務的直接連線，讓您可以使用虛擬網路的私人位址空間來存取 Azure 服務。 透過服務端點預定流向 Azure 資源的流量，一律會留在 Microsoft Azure 骨幹網路中。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立具有一個子網路的虛擬網路
> * 新增子網路，並啟用服務端點
> * 建立 Azure 資源，並僅允許從子網路對其進行網路存取
> * 將虛擬機器 (VM) 部署到每個子網路
> * 確認從子網路對資源的存取
> * 確認從子網路和網際網路對資源的存取遭到拒絕

您可以依偏好使用 [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) 或 [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) 完成本教學課程。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 上登入 Azure 入口網站。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 選取 Azure 入口網站左上角的 [+ 建立資源]  。
2. 選取 [網絡]，然後選取 [虛擬網路]。
3. 按一下 [+新增] 並輸入下列資訊： 

   |設定|值|
   |----|----|
   |訂用帳戶| 選取您的訂用帳戶|
   |資源群組 | 選取 [新建]  ，然後輸入 *myResourceGroup*。|
   |名稱| 輸入 myVirtualNetwork |
   |區域| 選取 **(美國) 美國東部** |

   ![輸入虛擬網路的相關基本資訊](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. 按一下 **下一步IP 位址 >**
   
   |設定|值|
   |----|----|
   |IPv4 位址空間| 保持為預設值 |
   |子網路名稱| 按一下 [預設]，並將名稱從「預設」變更為「公用」|
   |子網路位址範圍| 保持為預設值|

5. 按一下 **下一步安全性 >** 
   
   |設定|值|
   |----|----|   
   |BastionHost| 停用|
   |DDoS 保護| 停用|
   |防火牆| 停用|

6. 完成時，按一下 [檢閱及建立]。
7. 如果通過驗證檢查，請按一下 [建立]。
8. 等待部署完成，然後按一下 [移至資源] 或移至下一節。 

## <a name="enable-a-service-endpoint"></a>啟用服務端點

每項服務、每個子網路都會啟用服務端點。 若要建立子網路，並啟用子網路的服務端點：

1. 如果還沒有在 [虛擬網路資源] 頁面上，可以在入口網站頂端的 **搜尋資源、服務和文件** 方塊中搜尋新建立的網路，輸入 myVirtualNetwork，然後從清單中選取。
2. 在 **設定** 功能表 (左側) 中選取 [子網路]，然後選取 [+ 子網路]，如下圖所示：

    ![新增子網路](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. 在 [新增子網路]  底下選取或輸入下列資訊，然後選取 [確定]  ︰

    |設定|值|
    |----|----|
    |名稱| Private |
    |位址範圍| 保持為預設值|
    |服務端點| 選取 [Microsoft.Storage]|
    |服務端點原則 | 保留預設值為 0 |

> [!CAUTION]
> 針對具有資源的現有子網路啟用服務端點之前，請參閱[變更子網路設定](virtual-network-manage-subnet.md#change-subnet-settings)。

4. 按一下 [儲存]，然後關閉右側的子網路視窗。 新建立的子網路應該會顯示在清單中。

## <a name="restrict-network-access-for-a-subnet"></a>限制子網路的網路存取

根據預設，子網路中的所有虛擬機器執行個體可以與所有資源通訊。 建立網路安全性群組，並將它與子網路產生關聯，即可限制與子網路中所有資源之間的通訊：

1. 在 Azure 入口網站左上角，選取 [所有服務]。
2. 選擇 [網路]，然後選取 (或搜尋) [網路安全性群組]。
3. 從 [網路安全性群組] 頁面中，按一下 [+ 新增]。
4. 輸入下列資訊 

    |設定|值|
    |----|----|
    |訂用帳戶| 選取您的訂用帳戶|
    |資源群組 | 從清單中選取 [myResourceGroup]|
    |名稱| 輸入 **myNsgPrivate** |
    |Location| 選取 [美國東部] |

5. 按一下 [檢閱 + 建立]，並在通過驗證檢查時，按一下 [建立]。
6. 建立網路安全性群組之後，按一下 [移至資源] 或搜尋 myNsgPrivate。
7. 在左側的 **設定** 下，選取 [輸出安全性規則]。
8. 選取 [+ 新增]  。
9. 建立一個規則，允許 Azure 儲存體服務的輸出通訊。 輸入或選取下列資訊，然後選取 [新增]  ︰

    |設定|值|
    |----|----|
    |來源| 選取 [VirtualNetwork]  |
    |來源連接埠範圍| * |
    |Destination | 選取 [服務標記] |
    |目的地服務標記 | 選取 [儲存體] |
    |目的地連接埠範圍| 保留預設值為 8080 |
    |通訊協定|任意|
    |動作|允許|
    |優先順序|100|
    |名稱|重新命名為 **Allow-Storage-All**|

10. 建立另一個拒絕網際網路通訊的輸出安全性規則。 此規則會覆寫所有網路安全性群組中允許輸出網際網路通訊的預設規則。 使用下列值，完成步驟 6-9：

    |設定|值|
    |----|----|
    |來源| 選取 [VirtualNetwork]  |
    |來源連接埠範圍| * |
    |Destination | 選取 [服務標記] |
    |目的地服務標記| 選取 [網際網路] |
    |目的地連接埠範圍| * |
    |通訊協定|任意|
    |動作|**將預設值變更為「拒絕」** |
    |優先順序|110|
    |名稱|變更為 *Deny-Internet-All*|

11. 建立，允許從任一處對子網路輸入遠端桌面通訊協定 (RDP) 流量的「輸入安全性規則」。 此規則會覆寫拒絕來自網際網路之所有輸入流量的預設安全性規則。 允許與子網路的遠端桌面連線，以便在稍後步驟中可以測試連線。 
12. 在 [設定] 下，選取 [輸入安全性規則]。
13. 選取 [+ 新增]，並使用下列值：

    |設定|值|
    |----|----|
    |來源| 任意 |
    |來源連接埠範圍| * |
    |Destination | 選取 [VirtualNetwork] |
    |目的地連接埠範圍| 變更為 3389 |
    |通訊協定|任意|
    |動作|允許|
    |優先順序|120|
    |名稱|變更為 Allow-RDP-All|

   >[!WARNING] 
   > RDP 連接埠 3389 已公開至網際網路。 僅建議用於測試。 針對「生產環境」，建議您使用 VPN 或私人連線。

1.  在 [設定] 下，選取 [子網路]。
2.  按一下 [+ 關聯]。
3.  在 **虛擬網路** 下，選取 [myVirtualNetwork]。
4.  在 **子網路** 下，選取 [私人]，然後選取 [確定]。

## <a name="restrict-network-access-to-a-resource"></a>限制對資源的網路存取

如果資源是透過已針對服務端點啟用的 Azure 服務建立，則限制其網路存取的必要步驟會因為服務不同而有所差異。 請參閱個別服務的文件，以了解每個服務的特定步驟。 本教學課程的其餘部分包含對 Azure 儲存體帳戶的網路存取進行限制的步驟 (以範例形式說明)。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

1. 選取 Azure 入口網站左上角的 [+ 建立資源]  。
2. 在搜尋列中輸入「儲存體帳戶」，然後從下拉式功能表中選取。
3. 按一下 [+ 新增]。
4. 輸入以下資訊：

    |設定|值|
    |----|----|
    |訂用帳戶| 選取您的訂用帳戶|
    |資源群組| 選取 *myResourceGroup*|
    |儲存體帳戶名稱| 請輸入在所有 Azure 位置間具有唯一性、長度介於 3-24 個字元，且僅使用數字和小寫字母的名稱。|
    |位置| 選取 **(美國) 美國東部** |
    |效能|標準|
    |帳戶種類| StorageV2 (一般用途 v2)|
    |複寫| 本地備援儲存體 (LRS)|

5. 選取 [建立 + 檢閱]，並在通過驗證檢查時，按一下 [建立]。 

>[!NOTE] 
> 部署需要幾分鐘才能完成。

6. 建立儲存體帳戶後，請按一下 [移至資源]

### <a name="create-a-file-share-in-the-storage-account"></a>在儲存體帳戶中建立檔案共用

1. 移至儲存體帳戶的概觀頁面。
2. 選取 [檔案共用] 應用程式圖示，然後按一下 [+ 檔案共用]。

    |設定|值|
    |----|----|
    |名稱| my-file-share|
    |Quota| '設定為最大值' |

   ![儲存體帳戶](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. 按一下 [建立]  。
4. 檔案共用應該會顯示在 Azure 視窗中；如果沒有，請按一下 [重新整理]

### <a name="restrict-network-access-to-a-subnet"></a>限制對子網路的網路存取

根據預設，儲存體帳戶會接受來自任何網路用戶端的網路連線，包括網際網路。 您可以限制來自網際網路以及所有虛擬網路中所有其他子網路的網路存取 (myVirtualNetwork 虛擬網路中的「私人」子網路除外。)限制對子網路的網路存取：

1. 在您 (唯一名稱) 儲存體帳戶的 **設定** 下，選取 [網路]。
2. 選取 [選取的網路]  。
3. 選取 [+ 新增現有的虛擬網路]。
4. 在 [新增網路]  下，選取下列值，並選取 [新增]  ：

    |設定|值|
    |----|----|
    |訂用帳戶| 選取您的訂用帳戶|
    |虛擬網路| **myVirtualNetwork**|
    |子網路| **私人**|

    ![此螢幕擷取畫面顯示可讓您輸入指定值的 [新增網路] 窗格。](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. 按一下 [新增]，然後立即按一下 [儲存] 圖示以儲存變更。
6. 在儲存體帳戶的 **設定** 下，選取 [存取金鑰]，如下圖所示：

      ![此螢幕擷取畫面顯示從 [設定] 中選取的存取金鑰；您可以從 [設定] 中取得金鑰。](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. 按一下 [顯示金鑰] 並記下 **金鑰** 值，因為當您在稍後步驟中將檔案共用對應至 VM 中的磁碟機代號時，必須以手動方式輸入此值。

## <a name="create-virtual-machines"></a>建立虛擬機器

若要測試對儲存體帳戶的網路存取，請將 VM 部署至每個子網路。

### <a name="create-the-first-virtual-machine"></a>建立第一部虛擬機器

1. 從「搜尋資源 . ." 列中，搜尋 **虛擬機器**。
2. 選取 [+ 新增 > 虛擬機器]。 
3. 輸入下列資訊：

   |設定|值|
   |----|----|
   |訂用帳戶| 選取您的訂用帳戶|
   |資源群組| 選取稍早建立的 **myResourceGroup。|
   |虛擬機器名稱| 輸入 myVmPublic|
   |區域 | (美國) 美國東部
   |可用性選項| 可用性區域|
   |可用性區域 | 1 |
   |影像 | Windows Server 2019 Datacenter - Gen1 |
   |大小 | 選取您想使用的 VM 執行個體大小 |
   |使用者名稱|輸入您選擇的使用者名稱。|
   |密碼| 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
   |公用輸入連接埠 | 允許選取的連接埠 |
   |選取輸入連接埠 | 將預設值保留為 RDP (3389) |

   ![選取虛擬網路](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. 選取 **網路** 索引標籤，然後選取 [myVirtualNetwork]。 
5. 選取 [公用] 子網路。
6. 在 [NIC 網路安全性群組] 下方，選取 [進階]。 入口網站會自動為您建立允許連接埠 3389 的網路安全性群組；您必須開啟此連接埠，才能在後續的步驟中連線至虛擬機器。 

   ![輸入虛擬機器的相關基本資訊](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. 選取 [檢閱並建立]，然後選取 [建立] 並等待部署完成。
8. 按一下 [移至資源] 或開啟 **主資料夾 > 虛擬機器** 頁面，然後選取剛才建立的 VM「myVmPublic」，此 VM 應該已啟動。

### <a name="create-the-second-virtual-machine"></a>建立第二部虛擬機器

1. 再次完成步驟 1-8，但在步驟 3 中，將虛擬機器命名為 myVmPrivate，並將 **公用輸入連接埠** 設定為「無」。 
2. 在步驟 4-5 中，選取 [私人] 子網路。

>[!NOTE]
> **NIC 網路安全性群組** 和 **公用輸入連接埠** 的設定應與如下所示的影像相同，包括說明：「預設會封鎖所有公用網際網路流量」的藍色確認視窗。

   ![建立私人虛擬機器](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. 選取 [檢閱並建立]，然後選取 [建立] 並等待部署完成。 

>[!WARNING]
> 部署完成之前，請勿繼續下一個步驟。

4. 等候出現如下所示的確認視窗，然後按一下 [移至資源]。

   ![建立私人虛擬機器確認視窗](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>確認對儲存體帳戶的存取

1. 建立 myVmPrivate VM 之後，按一下 [移至資源]。 
2. 選取 [連線 > RDP] 以連線至 VM。
3. 選取 [連線] 按鈕之後，系統會建立一個「遠端桌面通訊協定」(.rdp) 檔案。 按一下 [下載 RDP 檔案]  將檔案下載到您的電腦。  
4. 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]  。 輸入您在建立 VM 時指定的使用者名稱和密碼。 建議您選取 [其他選擇]，然後選取 [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。 在電子郵件欄位中，輸入稍早指定的「管理員帳戶：使用者名稱」認證。 
5. 選取 [確定]。
6. 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是]  或 [繼續]  以繼續進行連線。 您應該會看到 VM 啟動，如下所示：

   ![顯示私人虛擬機器正在執行](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. 在 VM 視窗中，開啟 PowerShell CLI 執行個體。
8. 使用下列指令碼，使用 PowerShell 將 Azure 檔案共用對應至磁碟機 Z。 在執行後續命令之前，請將 `<storage-account-key>` 和 `<storage-account-name>` 取代為您稍早在[建立儲存體帳戶](#create-a-storage-account)中提供的值。

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell 會傳回類似以下範例輸出的輸出：

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Azure 檔案共用已成功對應至 Z 磁碟機。

9.   關閉 myVmPrivate  虛擬機器的遠端桌面工作階段。

## <a name="confirm-access-is-denied-to-storage-account"></a>確認對儲存體帳戶的存取遭到拒絕

1. 在入口網站頂端的 [搜尋資源、服務和文件] 方塊中，輸入 myVmPublic。
2. 當 **myVmPublic** 出現在搜尋結果中時，請選取它。
3. 在 myVmPublic VM 的 [確認對儲存體帳戶的存取](#confirm-access-to-storage-account) 中，完成步驟 1-8。

   短暫等候之後，您會收到 `New-PSDrive : Access is denied` 錯誤。 存取遭到拒絕，因為 myVmPublic  VM 是部署在「公用」  子網路中。 「公用」  子網路沒有已啟用的服務端點可供 Azure 儲存體使用。 儲存體帳戶只允許「私人」  子網路，而不允許「公用」  子網路的網路存取。

4. 關閉 myVmPublic  VM 的遠端桌面工作階段。
5. 回到 Azure 入口網站，移至您稍早建立的唯一名稱儲存體帳戶。
6. 在檔案服務下，選取稍早建立的 [檔案共用]- my-file-share。
7. 您應該會收到下列錯誤訊息：

   ![拒絕存取錯誤](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> 存取遭到拒絕，因為您的電腦不在 MyVirtualNetwork 虛擬網路的「私人」子網路中。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup  時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用虛擬網路子網路的服務端點。 您已了解可以針對從多項 Azure 服務部署的資源啟用服務端點。 您已建立 Azure 儲存體帳戶，並將儲存體帳戶的網路存取限制為只能存取虛擬網路子網路內的資源。 若要深入了解服務端點，請參閱[服務端點概觀](virtual-network-service-endpoints-overview.md)和[管理子網路](virtual-network-manage-subnet.md)。

如果您的帳戶中有多個虛擬網路，您可以同時連線兩個虛擬網路，讓每個虛擬網路內的資源都可互相進行通訊。 若要了解如何連線虛擬網路，請移至下一個教學課程。

> [!div class="nextstepaction"]
> [連線虛擬網路](./tutorial-connect-virtual-networks-portal.md)
