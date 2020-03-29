---
title: 使用新的工作階段主機擴展現有主機池 - Azure
description: 如何在 Windows 虛擬桌面中使用新工作階段主機擴展現有主機池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365214"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新工作階段主機擴展現有主機池

在主機池內增加使用量時，可能需要使用新工作階段主機擴展現有主機池以處理新負載。

本文將介紹如何使用新的工作階段主機擴展現有主機池。

## <a name="what-you-need-to-expand-the-host-pool"></a>擴展主機池所需的內容

在開始之前，請確保使用以下方法之一創建了主機池和工作階段主機虛擬機器 （VM）：

- [Azure 應用商店產品/服務](./create-host-pools-azure-marketplace.md)
- [GitHub Azure 資源管理器範本](./create-host-pools-arm-template.md)
- [使用 PowerShell 建立主機集區](./create-host-pools-powershell.md)

首次創建主機池和工作階段主機 VM 時，還需要以下資訊：

- VM 大小、映射和名稱首碼
- 域聯接和 Windows 虛擬桌面租戶管理員憑據
- 虛擬網路名稱及子網路名稱

接下來的三節是可用於擴展主機池的三種方法。 您可以使用您喜歡的任意部署工具執行任一操作。

>[!NOTE]
>在部署階段，如果上一工作階段主機 VM 資源當前已關閉，您將看到錯誤訊息。 出現這些錯誤是因為 Azure 無法運行 PowerShell DSC 擴展，以驗證工作階段主機 VM 是否已正確註冊到現有主機池。 您可以安全地忽略這些錯誤，也可以通過在啟動部署過程之前啟動現有主機池中的所有工作階段主機 VM 來避免錯誤。

## <a name="redeploy-from-azure"></a>從 Azure 重新部署

如果已使用[Azure 應用商店產品或](./create-host-pools-azure-marketplace.md) [GitHub Azure 資源管理器範本](./create-host-pools-arm-template.md)創建了主機池和工作階段主機 VM，則可以從 Azure 門戶重新部署相同的範本。 重新部署範本會自動重新輸入您輸入到原始範本中的所有資訊，但密碼除外。

下面瞭解如何重新部署 Azure 資源管理器範本以展開主機池：

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 從 Azure 門戶頂部的搜索欄中，搜索**資源組**並選擇**服務**下的項。
3. 查找並選擇創建主機池時創建的資源組。
4. 在瀏覽器左側的面板中，選擇 **"部署**"。
5. 為主機池創建過程選擇適當的部署：
     - 如果使用 Azure 應用商店產品創建原始主機池，請選擇從**rds.wvd 預配-主機池**開始的部署。
     - 如果使用 GitHub Azure 資源管理器範本創建原始主機池，請選擇名為**Microsoft.template**的部署。
6. 選擇**重新部署**。
     
     >[!NOTE]
     >如果在選擇 **"重新部署"** 時範本未自動重新部署，請在瀏覽器左側的面板中選擇 **"範本"，** 然後選擇 **"部署**"。

7. 選擇包含現有主機池中的當前工作階段主機 VM 的資源組。
     
     >[!NOTE]
     >如果看到一個錯誤，指示您選擇其他資源組，即使您輸入的資源組正確，請選擇另一個資源組，然後選擇原始資源組。

8. 輸入以下_artifactsLocation *URL*：`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 在*Rdsh 實例數*中輸入所需的新工作階段主機總數。 例如，如果要將主機池從 5 個工作階段主機擴展到 8 個，請輸入**8**。
10. 輸入用於現有域 UPN 的相同現有域密碼。 不要更改使用者名，因為當您運行範本時，這將導致錯誤。
11. 輸入您為*租戶管理員 Upn 或應用程式 ID*輸入的使用者或應用程式 ID 所使用的相同租戶管理員密碼。再次，不要更改使用者名。
12. 完成提交以展開主機池。

## <a name="run-the-azure-marketplace-offering"></a>運行 Azure 應用商店產品/服務

[使用 Azure 應用商店按照創建主機池](./create-host-pools-azure-marketplace.md)中的說明進行操作，直到到達["運行 Azure 應用商店"產品以預配新的主機池](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)。 達到該點時，需要為每個選項卡輸入以下資訊：

### <a name="basics"></a>基本概念

除*預設桌面使用者*外，本節中的所有值都應與首次創建主機池和工作階段主機 VM 時提供的內容匹配：

1.    對於*訂閱*，選擇首次創建主機池的訂閱。
2.    對於*資源組*，選擇現有主機池工作階段主機 VM 所在的同一資源組。
3.    對於*區域*，選擇現有主機池工作階段主機 VM 所在的同一區域。
4.    對於*主機池名稱*，輸入現有主機池的名稱。
5.    對於*桌面類型*，選擇與現有主機池匹配的桌面類型。
6.    對於*預設桌面使用者*，輸入要登錄到 Windows 虛擬桌面用戶端並在 Azure 應用商店產品完成後訪問桌面的任何其他使用者的逗號分隔清單。 例如，如果要user3@contoso.com分配和user4@contoso.com訪問，請輸入user3@contoso.com。user4@contoso.com
7.    選擇**下一個 ：配置虛擬機器**。

>[!NOTE]
>除*預設桌面使用者*外，所有欄位必須與現有主機池中配置的內容完全符合。 如果存在導致新主機池的不匹配。

### <a name="configure-virtual-machines"></a>設定虛擬機器

除 VM 總數外，本節中的所有參數值都應與首次創建主機池和工作階段主機 VM 時提供的內容相匹配。 您輸入的 VM 數將是擴展主機池中的 VM 數：

1. 選擇與現有工作階段主機 VM 匹配的 VM 大小。
    
    >[!NOTE]
    >如果您要尋找的特定 VM 大小未出現在 VM 大小選取器中，原因是我們尚未將其上架到 Azure Marketplace 工具中。 若要要求某個 VM 大小，請在 [Windows 虛擬桌面 UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中建立要求或附議現有要求。

2. 自訂*使用方式設定檔*、*使用者總數*和*虛擬機器參數數*，以選擇要在主機池中具有的工作階段主機總數。 例如，如果要將主機池從 5 個工作階段主機擴展到 8 個，請將這些選項配置為訪問 8 個虛擬機器。
3. 輸入虛擬機器的名稱前置詞。 比方說，如果您輸入名稱「前置詞」，則虛擬機器會稱為 "prefix-0"、"prefix-1"，依此類推。
4. 選擇 **"下一步 ：虛擬機器設置**"。

### <a name="virtual-machine-settings"></a>虛擬機器設定

本節中的所有參數值都應與首次創建主機池和工作階段主機 VM 時提供的內容相匹配：

1. 對於*映射源*和*圖像作業系統版本*，輸入首次創建主機池時提供的資訊。
2. 對於*AD 域加入 UPN*和相關密碼，請輸入您首次創建主機池以將 VM 加入活動目錄域時提供的資訊。 這些憑據將用於在虛擬機器上創建本地帳戶。 您可以重置這些本地帳戶，以便以後更改其憑據。
3. 對於虛擬網路資訊，為現有主機池工作階段主機 VM 所在的位置選擇相同的虛擬網路和子網。
4. 選擇**下一個 ：配置 Windows 虛擬桌面資訊**。

### <a name="windows-virtual-desktop-information"></a>Windows 虛擬桌面資訊

本節中的所有參數值都應與首次創建主機池和工作階段主機 VM 時提供的內容相匹配：

1. 針對 [Windows 虛擬桌面租用戶群組名稱]**，輸入包含您租用戶的租用戶群組。 除非系統將特定租用戶群組名稱提供給您，否則將它保留為預設值。
2. 針對 [Windows 虛擬桌面租用戶名稱]**，輸入您將在其中建立此主機集區的租用戶名稱。
3. 指定首次創建主機池和工作階段主機 VM 時使用的相同憑據。 如果使用服務主體，請輸入服務主體所在的 Azure 活動目錄實例的 ID。
4. 選擇**下一個 ： 查看 + 創建**。

## <a name="run-the-github-azure-resource-manager-template"></a>運行 GitHub Azure 資源管理器範本

按照運行 Azure[資源管理器範本中的說明來預配新的主機池](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)，並提供除*Rdsh 實例數*之外的所有相同的參數值。 在運行範本後，輸入主機池中所需的工作階段主機 VM 數。 例如，如果要將主機池從 5 個工作階段主機擴展到 8 個，請輸入**8**。

## <a name="next-steps"></a>後續步驟

現在，您已經擴展了現有的主機池，您可以登錄到 Windows 虛擬桌面用戶端以作為使用者會話的一部分對其進行測試。 您可以使用以下任一用戶端連接到會話：

- [與 Windows 桌面用戶端連線](./connect-windows-7-and-10.md)
- [與 Web 用戶端連線](./connect-web.md)
- [與 Android 用戶端連線](./connect-android.md)
- [與 macOS 用戶端連線](./connect-macos.md)
- [與 iOS 用戶端連線](./connect-ios.md)
