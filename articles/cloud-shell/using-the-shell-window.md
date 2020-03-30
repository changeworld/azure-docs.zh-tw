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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
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

## <a name="change-the-text-size"></a>更改文本大小
按一下視窗左上方的設定圖示，並將滑鼠游標移至 [文字大小] 選項上方，然後選取想要的文字大小。 您的選擇將會跨工作階段保存。
![文字大小](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>變更字型
按一下視窗左上角的設置圖示，然後懸停在"Font"選項上並選擇所需的字體。  您的選擇將會跨工作階段保存。
![字型](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>上傳及下載檔案
按一下視窗左上角的上傳/下載檔案圖示，然後選擇上傳或下載。  
![上傳/下載檔案](media/using-the-shell-window/uploaddownload.png)
* 對於上傳檔，請使用快顯視窗流覽到本地電腦上的檔，選擇所需的檔，然後按一下"打開"按鈕。  該檔將上載到`/home/user`目錄中。
* 要下載檔案，請在快顯視窗中輸入完全限定的檔路徑（即，基本上在預設情況下顯示的`/home/user`目錄下的路徑），然後選擇"下載"按鈕。  
> [!NOTE] 
> 檔和檔路徑在雲外殼中區分大小寫。 仔細檢查檔路徑中的大小寫。

## <a name="open-another-cloud-shell-window"></a>打開另一個雲殼視窗
雲外殼允許每個會話作為單獨的進程存在，從而跨瀏覽器選項卡實現多個併發會話。
如果要結束工作階段，請務必從每個工作階段視窗結束，因為每個處理序雖然是在相同的電腦上執行，但卻是獨立執行的。  
按一下視窗左上角打開的新會話圖示。 將打開一個新選項卡，另一個會話將連接到現有容器。
![打開新會話](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell 編輯器
* 請參閱["使用 Azure 雲外殼編輯器"](using-cloud-shell-editor.md)頁。

## <a name="web-preview"></a>網路預覽
按一下視窗左上角的 Web 預覽圖示，選擇"配置"，指定要打開的所需埠。  選擇"打開端口"以僅打開端口，或選擇"打開和流覽"以在新選項卡中打開端口並預覽埠。  
![網路預覽](media/using-the-shell-window/preview.png)  
<br>
![配置埠](media/using-the-shell-window/preview-configure.png)  
按一下視窗左上角的 Web 預覽圖示，選擇"預覽埠..."在新選項卡中預覽打開的埠。按一下視窗左上角的 Web 預覽圖示，選擇"關閉埠..."關閉打開的埠。  
![預覽/關閉埠](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>將 Cloud Shell 視窗最大化和最小化
按一下視窗右上方的 [最小化] 圖示，即可將視窗隱藏。 再按一下 [Cloud Shell] 圖示，即可取消隱藏。
按一下 [最大化] 圖示，即可將視窗設成最大高度。 若要將視窗還原到先前的大小，請按一下 [還原]。  
![最小化或最大化視窗](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>複製和貼上
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>調整 Cloud Shell 視窗大小
按一下工具列上邊緣並向上或向下拖曳，即可調整 Cloud Shell 視窗大小。

## <a name="scrolling-text-display"></a>捲動文字顯示
使用您的滑鼠或觸控板來捲動終端機文字。

## <a name="exit-command"></a>結束命令
執行 `exit` 可終止作用中的工作階段。 此行為預設會在 20 分鐘後發生，不須進行互動。

## <a name="next-steps"></a>後續步驟

[雲殼快速啟動中的 Bash](quickstart.md) <br>
[雲殼中的 PowerShell 快速入門](quickstart-powershell.md)
