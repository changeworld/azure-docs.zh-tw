---
title: 使用 Azure Cloud Shell 視窗 | Microsoft Docs
description: 如何使用 Azure Cloud Shell 視窗的概觀。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70860315"
---
# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 視窗

本文件說明如何使用 Cloud Shell 視窗。

## <a name="swap-between-bash-and-powershell-environments"></a>在 Bash 與 PowerShell 環境之間交換

使用 Cloud Shell 工具列中的環境選取器，在 Bash 與 PowerShell 環境之間交換。  
![選取環境](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>重新啟動 Cloud Shell
按一下 Cloud Shell 工具列中的重新啟動圖示，即可重設電腦狀態。  
![重新啟動 Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> 重新啟動 Cloud Shell 將會重設電腦狀態，而所有未由 Azure 檔案共用保存的檔案都將遺失。

## <a name="change-the-text-size"></a>變更文字大小
按一下視窗左上方的設定圖示，並將滑鼠游標移至 [文字大小] 選項上方，然後選取想要的文字大小。 您的選擇將會跨工作階段保存。
![文字大小](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>變更字型
按一下視窗左上角的 [設定] 圖示，將滑鼠停留在 [字型] 選項上，然後選取您想要的字型。  您的選擇將會跨工作階段保存。
![字型](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>上傳及下載檔案
按一下視窗左上角的 [上傳/下載檔案] 圖示，然後選取 [上傳] 或 [下載]。  
![上傳/下載檔案](media/using-the-shell-window/uploaddownload.png)
* 若要上傳檔案，請使用快顯視窗流覽至本機電腦上的檔案、選取所需的檔案，然後按一下 [開啟] 按鈕。  檔案將會上傳到目錄中 `/home/user` 。
* 若要下載檔案，請在快顯視窗中輸入完整的檔案路徑（亦即，基本上是目錄底下預設會 `/home/user` 顯示的路徑），然後選取 [下載] 按鈕。  
> [!NOTE] 
> 檔案和檔案路徑在 Cloud Shell 中區分大小寫。 在檔案路徑中，再次檢查您的大小寫。

## <a name="open-another-cloud-shell-window"></a>開啟另一個 Cloud Shell 視窗
Cloud Shell 允許每個會話以個別的進程形式存在，跨瀏覽器索引標籤來啟用多個並行會話。
如果要結束工作階段，請務必從每個工作階段視窗結束，因為每個處理序雖然是在相同的電腦上執行，但卻是獨立執行的。  
按一下視窗左上角的 [開啟新的會話] 圖示。 新的索引標籤隨即開啟，並連接到現有容器的另一個會話。
![開啟新的會話](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell 編輯器
* 請參閱[使用 Azure Cloud Shell 編輯器](using-cloud-shell-editor.md)頁面。

## <a name="web-preview"></a>Web 預覽
按一下視窗左上方的 [web 預覽] 圖示，選取 [設定]，指定要開啟的所需埠。  選取 [開啟埠] 僅開啟埠，或按一下 [開啟並流覽] 以開啟埠，並在新的索引標籤中預覽埠。  
![Web 預覽](media/using-the-shell-window/preview.png)  
<br>
![設定埠](media/using-the-shell-window/preview-configure.png)  
按一下視窗左上方的 [web 預覽] 圖示，選取 [預覽埠 ...]若要在新的索引標籤中預覽開啟的埠，請按一下視窗左上方的 [web 預覽] 圖示，選取 [關閉埠 ...]以關閉開啟的埠。  
![預覽/關閉埠](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>將 Cloud Shell 視窗最大化和最小化
按一下視窗右上方的 [最小化] 圖示，即可將視窗隱藏。 再按一下 [Cloud Shell] 圖示，即可取消隱藏。
按一下 [最大化] 圖示，即可將視窗設成最大高度。 若要將視窗還原到先前的大小，請按一下 [還原]。  
![將視窗最小化或最大化](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>複製和貼上
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>調整 Cloud Shell 視窗大小
按一下工具列上邊緣並向上或向下拖曳，即可調整 Cloud Shell 視窗大小。

## <a name="scrolling-text-display"></a>捲動文字顯示
使用您的滑鼠或觸控板來捲動終端機文字。

## <a name="exit-command"></a>結束命令
執行 `exit` 可終止作用中的工作階段。 此行為預設會在 20 分鐘後發生，不須進行互動。

## <a name="next-steps"></a>後續步驟

[Cloud Shell 快速入門中的 Bash](quickstart.md) <br>
[Cloud Shell 快速入門中的 PowerShell](quickstart-powershell.md)
