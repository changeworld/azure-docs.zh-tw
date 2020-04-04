---
title: Windows 虛擬桌面中的 Windows 10 VM 安裝語言套件 - Azure
description: 如何在 Windows 虛擬桌面中為 Windows 10 多會話 VM 安裝語言包。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634031"
---
# <a name="install-language-packs"></a>安裝語言套件

在國際上設置 Windows 虛擬桌面部署時,最好確保部署支援多種語言。 您可以在 Windows 10 企業多會話虛擬機器 (VM) 映射上安裝語言包,以支援組織所需的盡可能多的語言。 本文將告訴您如何安裝語言包和捕獲允許使用者選擇自己的顯示語言的圖像。

詳細瞭解如何在 Azure 中使用[Azure 門戶在可用性區域創建 Windows 虛擬機器時](../virtual-machines/windows/create-portal-availability-zone.md)在 Azure 中部署 VM。

>[!NOTE]
>本文適用於 Windows 10 企業多會話 VM。

## <a name="install-a-language-pack"></a>安裝語言套件

要使用語言包創建 VM 映射,首先需要將語言包安裝到電腦上並捕獲其映射。

要安裝語言套件:

1. 以管理員身份登錄。
2. 確保已安裝所有最新的 Windows 和 Windows 應用商店更新。
3. 跳到**設定** > **時間&語言** > **區域**。
4. 在**國家或地區**下,從下拉菜單中選擇您首選的國家或地區。
    在此示例中,我們將選擇**法國**,如以下屏幕截圖所示:

    ![區域頁面的螢幕截圖。 當前選定的區域是法國。](media/region-page-france.png)

5. 之後,選擇 **「語言**」,然後選擇 **「添加語言**」。 從清單中選擇要安裝的語言,然後選擇 **「下一步**」 。。
6. 打開 **「安裝語言功能」** 視窗時,選擇標記為 **「安裝語言包」的複選框,並將其設置為「Windows 顯示語言**」 。。
7. 選取 [安裝]  。
8. 要同時添加多種語言,請選擇 **「添加語言**」,然後重複此過程以在步驟 5 和 6 中添加語言。 對要安裝的每種語言重複此過程。 但是,您一次只能將一種語言設置為顯示語言。

    讓我們通過快速的可視化演示來運行。 下圖演示如何安裝法語和荷蘭語包,然後將法語設置為顯示語言。

    ![過程開頭的語言頁面的屏幕截圖。 所選的 Windows 顯示語言為英語。](media/language-page-default.png)

    ![語言選擇視窗的螢幕截圖。 使用者已在搜索欄中輸入「法語」以查找法語包。](media/select-language-french.png)

    ![「安裝語言功能」頁的螢幕截圖。 法語被選為首選語言。 所選選項包括"設置我的顯示語言"、"安裝語言包"、"語音辨識"和"手寫"。](media/install-language-features.png)

    安裝語言包後,您應該會看到語言包的名稱顯示在語言清單中。

    ![安裝了新語言包的語言頁面的屏幕截圖。 法語和荷蘭語言包列在「首選語言」下。](media/language-page-complete.png)

9. 如果出現一個視窗,要求您登出作業階段。 註銷,然後再次登錄。 您的顯示語言現在應該是您選擇的語言。

10.  跳到**控制面板** > **時鐘與地區** > **。**

11.  開啟 **「區域」** 視窗時, 選擇 **「管理**」選項卡,然後選擇 **「複製」設定**。

12.  選擇標記為 **「歡迎螢幕」和「歡迎」螢幕和系統帳戶**以及 **「新使用者帳戶」的**複選框。

13.  選取 [確定]  。

14.  將開啟一個視窗,並告訴您重新啟動作業階段。 選擇**立即重新啟動**。

15.  重新登入後,傳回**控制面板** > **時鐘和地區** > **區域**。

16.  選擇 **「管理」** 選項卡。

17.  選擇 **「更改系統區域設定**」 。

18. 在 **「當前系統區域設定**」下的下拉菜單中,選擇要使用區域設置的語言。 之後,選擇 **"確定**」。。

19. 選擇 **「立即重新啟動**」以再次重新啟動作業階段。

恭喜您,您已安裝語言包!

在繼續之前,請確保系統安裝了最新版本的 Windows 和 Windows 應用商店。

## <a name="sysprep"></a>Sysprep

接下來,您需要對機器進行系統準備,以便為圖像捕獲過程做好準備。

要對機器進行系統準備:

1. 以系統管理員身分開啟 PowerShell。
2. 執行以下 cmdlet 以轉到正確的目錄:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. 接下來,執行以下 cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. 打開"系統準備工具"視窗時,選擇標記為 **"通用"** 的複選框,然後轉到 **「關機選項**」,然後從下拉菜單**中選擇"關閉**"。

>[!NOTE]
>syprep 過程需要幾分鐘才能完成。 當 VM 關閉時,遠端作業階段將斷開連接。

### <a name="resolve-sysprep-errors"></a>解決系統準備錯誤

如果您在 sysprep 時看到錯誤訊息,您應該執行以下操作:

1. 開啟**驅動器 C**並轉到**Windows** > **System32 Sysprep** > **黑豹**,然後開啟**設定檔**。

   錯誤檔中的文本將告訴您需要卸載特定的語言包,如下圖所示。 複製下一步的語言包名稱。

   ![設置文件截圖。 帶有包名稱的文本以深藍色突出顯示。](media/setuperr-package-name.png)

2. 開啟新的 PowerShell 視窗,使用步驟 2 中複製的套件名稱執行以下 cmdlet 以移除語言套件:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. 檢查以確保您再次執行`Remove-AppxPackage`cmdlet 從而刪除了套件。 如果已成功刪除包,則應看到一條消息,指出您嘗試刪除的包不存在。

4. 再次運行`sysprep.exe`cmdlet。

## <a name="capture-the-image"></a>擷取映像

現在,您的系統已準備就緒,您可以捕獲映射,以便其他用戶可以基於您的系統開始使用 VM,而無需重複配置過程。

要捕捉影像:

1. 轉到 Azure 門戶,然後選擇在[安裝語言包](#install-a-language-pack)和[sysprep](#sysprep)中配置的電腦的名稱。

2. 選擇**擷取**。

3. 在 **「名稱」** 欄位中輸入影像的名稱,並使用 **「資源群組**下拉選單」將其分配給資源組,如下圖所示。

   ![創建圖像視窗的螢幕截圖。 使用者給此測試映射的名稱為"vmwvd-image-fr",並且已將其分配給"testwvdimagerg"資源組。](media/create-image.png)

4. 選取 [建立]  。

5. 等待幾分鐘,等待捕獲過程完成。 當映射準備就緒時,您應該在通知中心看到一條消息,通知您圖像已捕獲。

現在可以使用新映射部署 VM。 部署 VM 時,請確保[使用 Azure 門戶 在可用性區域中執行](../virtual-machines/windows/create-portal-availability-zone.md)創建 Windows 虛擬機器中的說明。

### <a name="how-to-change-display-language-for-standard-users"></a>如何變更標準使用者的顯示語言

標準用戶可以更改其 VM 上的顯示語言。

要變更顯示語言:

1. 跳到**語言設定**。 如果您不知道它在哪裡,則可以在「開始」選單的搜尋列中輸入**語言**。

2. 在 Windows 顯示語言下拉選單中,選擇要用作顯示語言的語言。

3. 註銷會話,然後重新登錄。 顯示語言現在應該是您在步驟 2 中選擇的語言。
