---
title: Windows 虛擬桌面 MSIX app 附加入口網站預覽-Azure
description: 如何使用 Azure 入口網站設定適用于 Windows 虛擬桌面的 MSIX 應用程式連接。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ffae02a11f2857e72cf7c5d2bbc84d8ab6174e17
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425793"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>使用 Azure 入口網站設定 MSIX 應用程式附加

> [!IMPORTANT]
> MSIX 應用程式附加目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文將逐步引導您瞭解如何在 Windows 虛擬桌面環境中設定 MSIX 應用程式附加 (預覽) 。

## <a name="requirements"></a>規格需求

>[!IMPORTANT]
>開始之前，請務必填寫並提交 [此表單](https://aka.ms/enablemsixappattach) ，以在您的訂用帳戶中啟用 MSIX 應用程式附加。 如果您沒有核准的要求，MSIX app attach 將無法運作。 要求的核准可能需要最多24小時的工作天。 當您的要求已接受且完成時，您會收到一封電子郵件。

以下是設定 MSIX app attach 所需的內容：

- 運作中的 Windows 虛擬桌面部署。 若要瞭解如何將 Windows 虛擬桌面部署 (傳統) ，請參閱 [在 Windows 虛擬桌面中建立租](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)使用者。 若要瞭解如何使用 Azure Resource Manager 整合部署 Windows 虛擬桌面，請參閱 [使用 Azure 入口網站建立主機集](./create-host-pools-azure-marketplace.md)區。
- 至少有一個作用中工作階段主機的 Windows 虛擬桌面主機集區。
- MSIX 封裝工具。
- MSIX 封裝的應用程式會展開至已上傳至檔案共用的 MSIX 映射中。
- 您的 Windows 虛擬桌面部署中將儲存 MSIX 封裝的檔案共用。
- 您上傳 MSIX 映射所在的檔案共用必須也可供主機集區中)  (Vm 的所有虛擬機器存取。 使用者將需要唯讀許可權才能存取該映射。

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>關閉 MSIX 應用程式附加應用程式的自動更新

開始之前，您必須停用 MSIX 應用程式附加應用程式的自動更新。 若要停用自動更新，您將必須在提升權限的命令提示字元中執行下列命令：

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

停用自動更新之後，您必須啟用 Hyper-v，因為您將使用 `Mount-VHD` 命令來暫存和卸載 VHD 至 [移至]。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>此變更需要您重新啟動虛擬機器。

## <a name="configure-the-msix-app-attach-management-interface"></a>設定 MSIX 應用程式附加管理介面

接下來，您必須下載並設定 Azure 入口網站的 MSIX 應用程式附加管理介面。

若要設定管理介面：

1. [開啟預覽入口網站](https://preview.portal.azure.com/?feature.msixapplications=true#home)。
2. 如果您收到提示，詢問您是否將延伸模組視為可信任，請選取 [ **允許**]。

      > [!div class="mx-imgBorder"]
      > ![未受信任的延伸模組視窗的螢幕擷取畫面。 [允許] 會以紅色反白顯示。](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>將 MSIX 映射新增至主機集區

接下來，您必須將 MSIX 映射新增至主機集區。

若要新增 MSIX 映射：

1. 開啟 Azure 入口網站。

2. 在搜尋列中輸入 **Windows 虛擬桌面** ，然後選取服務名稱。

      > [!div class="mx-imgBorder"]
      > ![使用者從 Azure 入口網站的搜尋列下拉式功能表中選取 [Windows 虛擬桌面] 的螢幕擷取畫面。 [Windows 虛擬桌面] 會以紅色反白顯示。](media/find-and-select.png)

3. 選取您打算放置 MSIX apps 的主機集區。

4. 選取 [ **MSIX 套件** ] 以開啟資料格，其中包含目前新增至主機集區的所有 **MSIX 套件** 。

5. 選取 [ **+ 新增** ] 以開啟 [ **新增 MSIX 封裝** ] 索引標籤。

6. 在 [ **新增 MSIX 封裝** ] 索引標籤中，輸入下列值：

      - 針對 [ **MSIX 映射路徑**]，輸入指向檔案共用上 MSIX 映射的有效 UNC 路徑。  (例如 `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` ) 。當您完成時，請選取 [ **新增** ]，以查閱 MSIX 容器，以檢查路徑是否有效。

      - 針對 **MSIX 套件**，請從下拉式功能表中選取相關的 MSIX 封裝名稱。 只有在您已在 **MSIX 映射路徑** 中輸入有效的影像路徑時，才會填入此功能表。

      - 針對 **封裝應用程式**，請確定清單包含您想要在 MSIX 套件中提供給使用者使用的所有 MSIX 應用程式。

      - （選擇性）如果您想要讓套件在使用者部署中有更好的使用者，請輸入 **顯示名稱** 。

      - 請確定 **版本** 具有正確的版本號碼。

      - 選取您要使用的 **註冊類型** 。 您要使用哪一個取決於您的需求：

    - **視需要註冊** 會延遲 MSIX 應用程式的完整註冊，直到使用者啟動應用程式為止。 這是我們建議您使用的註冊類型。

    - 當使用者登入時，只會註冊 **封鎖的登** 入。 我們不建議這種類型，因為它可能會導致使用者的登入時間更長。

7.  針對 [ **狀態**]，選取您偏好的狀態。
    -  作用 **中狀態可** 讓使用者與套件互動。
    -  **非** 作用中狀態會導致 Windows 虛擬桌面忽略套件，而不會將它傳遞給使用者。

8. 完成後，選取 [新增]。

## <a name="publish-msix-apps-to-an-app-group"></a>將 MSIX 應用程式發佈至應用程式群組

接下來，您必須將應用程式發佈到套件中。 您必須為桌上型電腦和遠端應用程式群組進行這項作業。

如果您已經有 MSIX 映射，請直接跳到將 [MSIX 應用程式發佈至應用程式群組](#publish-msix-apps-to-an-app-group)。 如果您想要測試舊版應用程式，請遵循[在 VM 上從桌面安裝程式建立 MSIX 套件](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的指示，將舊版應用程式轉換成 MSIX 套件。

若要發佈應用程式：

1. 在 Windows 虛擬桌面資源提供者中，選取 [ **應用程式群組** ] 索引標籤。

2. 選取您要用來發佈應用程式的應用程式群組。

   >[!NOTE]
   >MSIX 應用程式可透過 MSIX app 附加至遠端應用程式和桌面應用程式群組來傳遞

3. 一旦您進入應用程式群組，請選取 [ **應用程式** ] 索引標籤。 **應用程式** 方格將會顯示應用程式群組中所有現有的應用程式。

4. 選取 [ **+ 新增** ] 以開啟 [ **新增應用程式** ] 索引標籤。

      > [!div class="mx-imgBorder"]
      > ![使用者選取 [+ 新增] 以開啟 [新增應用程式] 索引標籤的螢幕擷取畫面](media/select-add.png)

5. 針對 [ **應用程式來源**]，選擇應用程式的來源。
    - 如果您使用的是桌面應用程式群組，請選擇 [ **MSIX 套件**]。
      
      > [!div class="mx-imgBorder"]
      > ![客戶從 [應用程式來源] 下拉式功能表中選取 [MSIX 套件] 的螢幕擷取畫面。 MSIX 封裝會以紅色反白顯示。](media/select-source.png)
    
    - 如果您使用的是遠端應用程式群組，請選擇下列其中一個選項：
        
        - 開始功能表
        - 應用程式路徑
        - MSIX 套件

    - 在 [ **應用程式名稱**] 中，輸入應用程式的描述性名稱。

    您也可以設定下列選用功能：
   
    - 針對 [ **顯示名稱**]，輸入您的使用者將會看到之套件的新名稱。

    - 在 [ **描述**] 中，輸入應用程式套件的簡短描述。

    - 如果您是使用遠端應用程式群組，您也可以設定下列選項：

        - **圖示路徑**
        - **圖示索引**
        - **在 web 摘要中顯示**

6. 完成時，選取 [儲存]。

>[!NOTE]
>當使用者從相同的主機集區指派給遠端應用程式群組和桌面應用程式群組時，桌面應用程式群組將會顯示在摘要中。

## <a name="assign-a-user-to-an-app-group"></a>將使用者指派給應用程式群組

將 MSIX 應用程式指派給應用程式群組之後，您必須授與使用者存取權。 您可以將使用者或使用者群組新增至具有已發佈 MSIX 應用程式的應用程式群組，藉以指派存取權。 遵循 [使用 Azure 入口網站管理應用程式群組](manage-app-groups.md) 中的指示，將您的使用者指派給應用程式群組。

## <a name="change-msix-package-state"></a>變更 MSIX 套件狀態

接下來，您必須將 MSIX 封裝狀態變更為「作用中 **」或「** **非** 作用中」，視您要對封裝進行的作業而定。 作用中的套件是您的使用者在發佈之後可與其互動的封裝。 Windows 虛擬桌面會忽略非使用中的套件，讓您的使用者無法與內部的應用程式互動。

### <a name="change-state-with-the-applications-list"></a>變更應用程式清單的狀態

若要使用應用程式清單變更封裝狀態：

1. 移至您的主機集區，然後選取 [ **MSIX 套件**]。 您應該會看到主機集區中所有現有 MSIX 套件的清單。

2. 選取您需要變更其狀態的 MSIX 套件，然後選取 [ **變更狀態**]。

### <a name="change-state-with-update-package"></a>變更更新套件的狀態

若要使用更新套件來變更套件狀態：

1. 移至您的主機集區，然後選取 [ **MSIX 套件**]。 您應該會看到主機集區中所有現有 MSIX 套件的清單。

2. 從 [MSIX 套件] 清單中選取您想要變更其狀態的封裝名稱。 這會開啟 [ **更新封裝** ] 索引標籤。

3. 切換 **狀態** 切換為 [**非****使用中]** 或 [作用中]，然後選取 [儲存] **。**

## <a name="change-msix-package-registration-type"></a>變更 MSIX 套件註冊類型

若要變更套件的註冊類型：

1. 選取 [ **MSIX 套件**]。 您應該會看到主機集區中所有現有 MSIX 套件的清單。

2. 在 [ **MSIX 套件] 方格** 中選取 [**封裝名稱**]，這會開啟該分頁以更新套件。

3. 視需要透過 **視需要/登入封鎖** 按鈕切換 **註冊類型**，然後選取 [**儲存]。**

## <a name="remove-an-msix-package"></a>移除 MSIX 套件

若要從您的主機集區中移除 MSIX 套件：

1. 選取 [ **MSIX 套件**]。  您應該會看到主機集區中所有現有 MSIX 套件的清單。

2. 選取您要刪除的封裝名稱右邊的省略號，然後選取 [ **移除**]。

## <a name="remove-msix-apps"></a>移除 MSIX 應用程式

若要從您的套件中移除個別的 MSIX 應用程式：

1. 移至主機集區，然後選取 [ **應用程式群組**]。

2. 選取您要從中移除 MSIX apps 的應用程式群組。

3. 開啟 [ **應用程式** ] 索引標籤。

4. 選取您要移除的應用程式，然後選取 [ **移除**]。

## <a name="next-steps"></a>後續步驟

在 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)詢問有關這項功能的問題。

您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)留下 Windows 虛擬桌面的意見反應。

以下是您可能會覺得有用的一些其他文章：

- [MSIX 應用程式附加詞彙](app-attach-glossary.md)
- [MSIX 應用程式附加常見問題](app-attach-faq.md)
