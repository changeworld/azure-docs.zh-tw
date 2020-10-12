---
title: 在 Windows 虛擬桌面中的 Windows 10 Vm 上安裝語言套件-Azure
description: 如何在 Windows 虛擬桌面中安裝 Windows 10 多會話 Vm 的語言套件。
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fbc2aba21212a83bd73d5664f4fe288017954c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084204"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>將語言套件新增至 Windows 10 多會話映射

Windows 虛擬桌面是您的使用者可以隨時隨地部署的服務。 這就是為什麼您的使用者必須能夠自訂其 Windows 10 企業版多會話影像所顯示的語言很重要的原因。

您可以透過兩種方式來容納使用者的語言需求：

- 使用每種語言的自訂映射來建立專用主機集區。
- 在相同的主機集區中，使用者具有不同的語言和當地語系化需求，但自訂其映射以確保他們可以選取所需的任何語言。

第二種方法更有效率且符合成本效益。 不過，您決定哪一種方法最適合您的需求。 本文將示範如何為您的影像自訂語言。

## <a name="prerequisites"></a>必要條件

您需要下列專案來自訂您的 Windows 10 企業版多會話映射以新增多種語言：

- Azure 虛擬機器 (VM) Windows 10 企業版多會話1903版或更新版本

- Language ISO、Feature 隨選 (FOD) Disk 1，以及映射使用的 OS 版本的收件匣應用程式 ISO。 您可以在此下載：
     
     - 語言 ISO：
        - [Windows 10，版本1903或1909語言套件 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10，2004版語言套件 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO：
        - [Windows 10，版本1903或 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10，2004版 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 收件匣應用程式 ISO：
        - [Windows 10，版本1903或1909收件匣應用程式 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10，2004版收件匣應用程式 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)

- Windows 檔案伺服器虛擬機器上的 Azure 檔案儲存體共用或檔案共用

>[!NOTE]
>您要用來建立自訂映射的 Azure VM 必須可以存取 (存放庫) 的檔案共用。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>視需要建立語言套件和功能的內容儲存機制

若要建立語言套件和 Fod 的內容存放庫，以及收件匣應用程式套件的存放庫：

1. 在 Azure VM 上，從 [必要條件](#prerequisites)中的連結下載 Windows 10 多語言 ISO、Fod 和收件匣應用程式，以 Windows 10 企業版多會話、1903/1909 版和2004映射。

2. 在 VM 上開啟並掛接 ISO 檔案。

3. 移至語言套件 ISO 並從 **LocalExperiencePacks** 和 **x64 \\ langpacks** 資料夾複製內容，然後將內容貼到檔案共用中。

4. 移至 **FOD ISO**檔案，複製其所有內容，然後貼到檔案共用中。
5. 移至收件匣應用程式 ISO 上的 **amd64fre** 資料夾，並複製您已備妥的收件匣應用程式之存放庫中的內容。

     >[!NOTE]
     > 如果您使用的是有限的儲存空間，請只複製您知道使用者需要的語言檔案。 您可以藉由查看檔案名中的語言代碼來區分檔案。 例如，法文檔案的名稱中會有 "fr" 程式碼。 如需所有可用語言的完整語言代碼清單，請參閱適用 [于 Windows 的可用語言套件](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)。

     >[!IMPORTANT]
     > 某些語言需要附屬套件中包含的額外字型，並遵循不同的命名慣例。 例如，日文字型的檔案名包含 "Jpan"。
     >
     > [!div class="mx-imgBorder"]
     > ![在其檔案名中使用 "Jpan" 語言標記的日文語言套件範例。](media/language-pack-example.png)

6. 設定語言內容存放庫共用的許可權，讓您可以從您將用來建立自訂映射的 VM 讀取存取權。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>手動建立自訂 Windows 10 企業版多會話映射

若要手動建立自訂 Windows 10 企業版多會話映射：

1. 部署 Azure VM，然後移至 Azure 資源庫，然後選取您目前使用 Windows 10 企業版多個會話的目前版本。
2. 在您部署 VM 之後，請使用 RDP 作為本機系統管理員來連接到 VM。
3. 請確定您的 VM 擁有所有最新的 Windows 更新。 視需要下載更新並重新啟動 VM。
4. 連接到語言套件、FOD 和收件匣應用程式檔案共用存放庫，並將它掛接到字母磁片磁碟機 (例如，磁片磁碟機 E) 。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>自動建立自訂 Windows 10 企業版多會話映射

如果您想要透過自動化程式安裝語言，您可以在 PowerShell 中設定腳本。 您可以使用下列腳本範例，安裝西班牙文 (西班牙) 、法文 (法國) 和中文 (PRC) 語言套件，以及適用于 Windows 10 企業版多會話2004版的附屬套件。 此腳本會將語言介面套件和所有必要的附屬套件整合至映射中。 不過，您也可以修改此腳本來安裝其他語言。 只需確定從提高許可權的 PowerShell 會話執行腳本，否則它將無法運作。

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>Windows 10 企業版版本1903和1909不需要 `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` 封裝檔案。

腳本可能需要一些時間，視您需要安裝的語言數目而定。

腳本完成執行之後，請檢查以確定語言套件已正確安裝，方法是前往 [**開始**  >  **設定**  >  **時間] & 語言**  >  **語言**。 如果有語言檔案，您就可以全部設定。

在 Windows 映像中新增其他語言之後，也需要更新收件匣應用程式以支援新增的語言。 這可以藉由使用收件匣應用程式 ISO 的內容重新整理預先安裝的應用程式來完成。 若要在中斷連線的環境中執行此重新整理 (無法從 VM 進行網際網路存取) ，您可以使用下列 PowerShell 腳本範例將程式自動化。

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>ISO 中包含的收件匣應用程式不是最新版本的預先安裝 Windows 應用程式。 若要取得所有應用程式的最新版本，您必須使用 Windows Store 應用程式更新應用程式，並在安裝其他語言之後，執行手動搜尋更新。

當您完成時，請務必中斷共用的連線。

## <a name="finish-customizing-your-image"></a>完成自訂您的映射

安裝語言套件之後，您可以安裝想要新增至自訂映射的任何其他軟體。

當您完成自訂映射之後，您必須執行「系統準備工具」 (sysprep) 。

若要執行 sysprep：

1. 開啟提升許可權的命令提示字元，然後執行下列命令來將映射一般化：  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 停止 VM，然後遵循在 [Azure 中建立一般化 VM 的受控映射](../virtual-machines/windows/capture-image-resource.md)中的指示，在受控映射中加以捕捉。

3. 您現在可以使用自訂映射來部署 Windows 虛擬桌面主機集區。 若要瞭解如何部署主機集區，請參閱 [教學課程：使用 Azure 入口網站建立主機集](create-host-pools-azure-marketplace.md)區。

## <a name="enable-languages-in-windows-settings-app"></a>在 Windows 設定應用程式中啟用語言

最後，在您部署主機集區之後，您必須將語言新增到每個使用者的語言清單，讓他們可以在 [設定] 功能表中選取其慣用的語言。

為確保您的使用者可以選取您所安裝的語言，請以使用者的身份登入，然後執行下列 PowerShell Cmdlet，將已安裝的語言套件新增至 [語言] 功能表。 您也可以將此腳本設定為自動工作或登入腳本，以便在使用者登入其會話時啟動。

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

當使用者變更其語言設定之後，他們必須登出其 Windows 虛擬桌面會話，並再次登入，變更才會生效。 

## <a name="next-steps"></a>後續步驟

如果您對語言套件的已知問題感到好奇，請參閱 [在 Windows 10 1803 版和更新版本中新增語言套件：已知問題](/windows-hardware/manufacture/desktop/language-packs-known-issue)。

如果您對 Windows 10 企業版多個會話有任何其他問題，請參閱我們的 [常見問題](windows-10-multisession-faq.md)。
