---
title: 在 Windows 虛擬桌面中的 Windows 10 Vm 上安裝語言套件-Azure
description: 如何在 Windows 虛擬桌面中安裝適用于 Windows 10 多會話 Vm 的語言套件。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80634031"
---
# <a name="install-language-packs"></a>安裝語言套件

當您以國際方式設定 Windows 虛擬桌面部署時，最好先確定您的部署支援多種語言。 您可以在 Windows 10 企業版多會話虛擬機器（VM）映射上安裝語言套件，以支援您的組織所需的多種語言。 本文將告訴您如何安裝語言套件和抓取影像，讓您的使用者可以選擇自己的顯示語言。

若要深入瞭解如何在 Azure 中部署 VM，請前往[使用 Azure 入口網站在可用性區域中建立 Windows 虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)。

>[!NOTE]
>本文適用于 Windows 10 企業版多會話 Vm。

## <a name="install-a-language-pack"></a>安裝語言套件

若要建立具有語言套件的 VM 映射，您必須先在電腦上安裝語言套件，並捕捉其映射。

若要安裝語言套件：

1. 以系統管理員身分登入。
2. 請確定您已安裝所有最新的 Windows 和 Windows Store 更新。
3. 移至 [**設定** > ] [**時間] & [語言** > **區域**]。
4. 在 [**國家或地區**] 底下，從下拉式功能表中選取您偏好的國家或地區。
    在此範例中，我們將選取 [**法國**]，如下列螢幕擷取畫面所示：

    ![[區域] 頁面的螢幕擷取畫面。 目前選取的區域是法國。](media/region-page-france.png)

5. 之後，選取 [**語言**]，然後選取 [**新增語言**]。 從清單中選擇您想要安裝的語言，然後選取 **[下一步]**。
6. 當 [**安裝語言功能**] 視窗開啟時，請選取標示為 [安裝語言套件] 的核取方塊，**然後設定為 [我的 Windows 顯示語言**]。
7. 選取 [安裝]  。
8. 若要一次新增多個語言，請選取 [**新增語言**]，然後重複此程式，在步驟5和6中新增語言。 針對您想要安裝的每種語言重複此程式。 不過，您一次只能設定一種語言作為顯示語言。

    讓我們透過快速的視覺化示範來執行。 下列影像顯示如何安裝法文和荷蘭文語言套件，然後將法文設定為顯示語言。

    ![程式開始時的語言頁面螢幕擷取畫面。 選取的 Windows 顯示語言為英文。](media/language-page-default.png)

    ![[語言選擇] 視窗的螢幕擷取畫面。 使用者已在搜尋列中輸入「法文」，以尋找法文語言套件。](media/select-language-french.png)

    ![[安裝語言功能] 頁面的螢幕擷取畫面。 選取法文做為慣用的語言。 選取的選項為 [設定我的顯示語言]、[安裝語言套件]、[語音辨識] 和 [手寫]。](media/install-language-features.png)

    安裝語言套件之後，您應該會看到語言套件的名稱出現在語言清單中。

    ![[語言] 頁面的螢幕擷取畫面，其中安裝了新的語言套件。 法文和荷蘭文言套件列在 [慣用語言] 之下。](media/language-page-complete.png)

9. 如果出現視窗，要求您登出會話。 登出，然後再次登入。 您的顯示語言現在應為您所選取的語言。

10.  移至 [**控制台** > ] [**時鐘] 和 [區域** > **區域**]。

11.  當 [**區域**] 視窗開啟時，選取 [系統**管理**] 索引標籤，然後選取 [**複製設定**]。

12.  選取標示為 [**歡迎畫面] 和 [系統帳戶**] 和 [**新增使用者帳戶**] 的核取方塊。

13.  選取 [確定]  。

14.  視窗隨即開啟，並告訴您重新開機會話。 選取 [**立即重新開機**]。

15.  在您重新登入之後，請回到 [**控制台** > ] [**時鐘] 和 [區域** > **區域**]。

16.  選取 [系統**管理**] 索引標籤。

17.  選取 [**變更系統地區**設定]。

18. 在 [**目前的系統地區**設定] 下的下拉式功能表中，選取您要使用的地區設定語言。 之後，選取 **[確定]**。

19. 選取 [**立即重新開機**]，再次重新開機您的會話。

恭喜，您已安裝語言套件！

在繼續之前，請確定您的系統已安裝最新版本的 Windows 和 Windows store。

## <a name="sysprep"></a>Sysprep

接下來，您需要 sysprep 電腦來準備它以進行映射捕獲程式。

若要 sysprep 您的電腦：

1. 以系統管理員身分開啟 PowerShell。
2. 執行下列 Cmdlet 以移至正確的目錄：
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. 接下來，執行下列 Cmdlet：
    
    ```powershell
    .\sysprep.exe
    ```

4. 當 [系統準備工具] 視窗開啟時，請選取標示為 [**一般化**] 的核取方塊，然後移至 [**關機選項**]，然後從下拉式功能表中選取 [**關機**]。

>[!NOTE]
>Syprep 程式需要幾分鐘的時間才能完成。 當 VM 關閉時，您的遠端會話將會中斷連線。

### <a name="resolve-sysprep-errors"></a>解決 sysprep 錯誤

如果您在 sysprep 程式期間看到錯誤訊息，以下是您應該執行的動作：

1. 開啟**磁片磁碟機 C**並移至**Windows** > **System32 Sysprep** > **Panther**，然後開啟**setuperr.log**檔案。

   錯誤檔案中的文字會告訴您需要卸載特定的語言套件，如下圖所示。 複製 [語言套件名稱] 以進行下一個步驟。

   ![Setuperr.log 檔案的螢幕擷取畫面。 具有封裝名稱的文字會反白顯示為深藍色。](media/setuperr-package-name.png)

2. 開啟新的 PowerShell 視窗，並使用您在步驟2中複製的套件名稱來執行下列 Cmdlet，以移除語言套件：

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. 再次執行`Remove-AppxPackage` Cmdlet，檢查並確定您已移除封裝。 如果您已成功移除封裝，您應該會看到一則訊息，指出您嘗試移除的套件不在該處。

4. 再次執行`sysprep.exe` Cmdlet。

## <a name="capture-the-image"></a>擷取映像

既然您的系統已準備就緒，您可以捕捉映射，讓其他使用者可以根據您的系統開始使用 Vm，而不需要重複設定程式。

若要捕獲映射：

1. 移至 [Azure 入口網站並選取您在[安裝語言套件](#install-a-language-pack)和[sysprep](#sysprep)中設定的電腦名稱稱。

2. 選取 [ **Capture**]。

3. 在 [**名稱**] 欄位中輸入您影像的名稱，並使用 [**資源群組**] 下拉式功能表將它指派給資源群組，如下圖所示。

   ![[建立映射] 視窗的螢幕擷取畫面。 使用者為此測試映射提供的名稱為 "vmwvd-image-fr"，且已將其指派給 "testwvdimagerg" 資源群組。](media/create-image.png)

4. 選取 [建立]  。

5. 等候幾分鐘讓 capture 進程完成。 當映射準備就緒時，您應該會在 [通知中心] 看到一則訊息，讓您知道已捕獲該映射。

您現在可以使用新的映射來部署 VM。 當您部署 VM 時，請務必遵循[使用 Azure 入口網站在可用性區域中建立 Windows 虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)中的指示進行。

### <a name="how-to-change-display-language-for-standard-users"></a>如何變更標準使用者的顯示語言

標準使用者可以變更其 Vm 上的顯示語言。

變更顯示語言：

1. 移至 [**語言設定**]。 如果您不知道其中的位置，您可以在 [開始] 功能表的搜尋列中輸入**語言**。

2. 在 [Windows 顯示語言] 下拉式功能表中，選取您想要用來做為顯示語言的語言。

3. 登出您的會話，然後重新登入。 顯示語言現在應該是您在步驟2中選取的語言。
