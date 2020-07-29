---
title: 設定實驗室以使用 Python 和 Jupyter 筆記本來教授資料科學 |Microsoft Docs
description: 瞭解如何設定實驗室，以使用 Python 和 Jupyter 筆記本來教授資料科學。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 25fd090f76c0aa11617b34503ea18d1b45a0e1ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445010"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>設定實驗室以使用 Python 和 Jupyter 筆記本來教授資料科學
本文概述如何使用教學課程來設定實驗室服務中的範本虛擬機器（VM），以指導學生如何使用[Jupyter 筆記本](http://jupyter-notebook.readthedocs.io/)，以及學生如何連線到其虛擬機器（vm）上的筆記本。

Jupyter Notebook 是開放原始碼專案，可讓您輕鬆地在稱為筆記本的單一畫布上結合 RTF 和可執行的 Python 原始程式碼。 執行筆記本會產生線性的輸入和輸出記錄。 這些輸出可以包含文字、資訊資料表、散佈圖等等。

## <a name="set-up-the-lab"></a>設定實驗室

### <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要有 Azure 訂用帳戶和實驗室帳戶的存取權。 請與貴組織的系統管理員討論，以查看您是否可以存取現有的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

擁有 Azure 訂用帳戶之後，請遵循教學課程：[設定實驗室帳戶](tutorial-setup-lab-account.md)中的指示，在 Azure 實驗室服務中建立新的實驗室帳戶。 您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定
針對實驗室帳戶啟用下表所述的設定。 如需啟用 marketplace 映射的詳細資訊，請參閱[指定可供實驗室建立者使用的 marketplace 映射](specify-marketplace-images.md)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
| Marketplace 映像 | 在您的實驗室帳戶中，根據您的作業系統需求啟用其中一個 Azure Marketplace 映射： <br/><ul><li>資料科學虛擬機器– Windows Server 2019</li><li>資料科學虛擬機器– Ubuntu 18.04</li></ul> |

> [!NOTE]
> 本文使用 Azure marketplace 上提供的資料科學虛擬機器映射，因為它們已預先設定 Jupyter Notebook。 不過，這些映射也包含許多其他適用于資料科學的開發和模型工具。 如果您不想要這些額外的工具，而且想要只使用 Jupyter 筆記本的輕量設定，請建立自訂的 VM 映射。 如需範例，請[在 Azure 上安裝 JupyperHub](http://tljh.jupyter.org/en/latest/install/azure.html)。 建立自訂映射之後，您可以將它上傳至共用映射資源庫，以使用 Azure 實驗室服務內的映射。 深入瞭解如何[在 Azure 實驗室服務中使用共用映射資源庫](how-to-attach-detach-shared-image-gallery.md)。 

### <a name="lab-settings"></a>實驗室設定
設定「**虛擬機器大小**」和「**虛擬機器映射**」設定，如下表所示設定教室實驗室。 如需建立教室實驗室的指示，請參閱[設定教室實驗室](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ | 
| 虛擬機器大小 | <p>您在此選擇的大小取決於您想要執行的工作負載：</p><ul><li>Small 或 Medium –適用于存取 Jupyter 筆記本的基本設定</li><li>小型 GPU （計算）–最適合用於計算密集型和網路密集型應用程式，例如人工智慧和深度學習</li></ul> | 
| 虛擬機器映像 | <p>根據您的作業系統需求選擇下列其中一個映射：</p><ul><li>[資料科學虛擬機器– Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[資料科學虛擬機器– Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>範本虛擬機器
建立實驗室之後，將會根據您選擇的虛擬機器大小和映射來建立範本 VM。 您可以使用您想要為此類別提供給學生的所有專案來設定範本 VM。 若要深入瞭解，請參閱[如何管理範本虛擬機器](how-to-create-manage-template.md)。 

根據預設，資料科學 VM 映射會隨附這類類別所需的許多資料科學架構和工具。 例如，映射包括：

- [Jupyter 筆記本](http://jupyter-notebook.readthedocs.io/)：一種 web 應用程式，可讓資料科學家採用原始資料、執行計算，並在相同的環境中查看結果。 它會在本機範本 VM 中執行。  
- [Visual Studio Code](https://code.visualstudio.com/)：一種整合式開發環境（IDE），可在撰寫和測試筆記本時提供豐富的互動式體驗。 如需詳細資訊，請參閱[在 Visual Studio Code 中使用 Jupyter 筆記本](https://code.visualstudio.com/docs/python/jupyter-support)。

### <a name="provide-notebooks-for-the-class"></a>提供類別的筆記本
下一項工作是要為學生提供您想要使用的筆記本。 若要提供您自己的筆記本，您可以將筆記本儲存在範本 VM 的本機上。 

如果您想要使用 Azure Machine Learning 的範例筆記本，請參閱[如何設定具有 Jupyter 筆記本的環境](../machine-learning/how-to-configure-environment.md#jupyter)。 

### <a name="optional-enable-graphical-desktop-for-linux"></a>選擇性：啟用適用于 Linux 的圖形化桌面 
**資料科學虛擬機器– Ubuntu**映射已布建 X2GO 伺服器，並已準備好接受用戶端連接。 設定範本 VM 時，不需要進一步的步驟。 

### <a name="publish-the-template-machine"></a>發佈範本機器
當您發佈範本時，向實驗室註冊的每個學生都會取得範本 VM 的複本，其中包含您已在其上設定的所有本機工具和筆記本。

## <a name="how-students-connect-to-jupyter-notebooks"></a>學生如何連接到 Jupyter 筆記本？
發佈範本之後，每個學生都可以存取您已為類別預先設定之所有專案的 VM，包括 Jupyter 筆記本。 下列各節說明學生連接到 Jupyter 筆記本的不同方式。 

### <a name="for-windows-vms"></a>針對 Windows VM
如果您已為學生提供 Windows Vm，他們必須連線到其 Vm，並使用在本機上可用的 Jupyter 筆記本。 

若要連接到 Windows VM，學生可以使用遠端桌面連線（RDP）。 如需詳細步驟，請參閱[如何存取教室實驗室](how-to-use-classroom-lab.md)。 

使用 Mac 或 Chromebook 的學生可以遵循下列文章中的指示，連接到資料科學 Windows VM。 

- [在 Mac 上使用 RDP 連線至 VM](connect-virtual-machine-mac-remote-desktop.md)
- [在 Chromebook 上使用 RDP 連線至 VM](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>針對 Linux VM
如果您已為學生提供 Linux Vm，有數個選項可供學生用來連線到其在 Vm 中的 Jupyter 筆記本：

- 連接到 VM 之後，在本機存取 Jupyter 筆記本
    - 透過 SSH 連線到 VM 來進行終端機會話
     - 圖形化會話的 X2Go 連線至 VM
- 使用 SSH 通道，將學生的本機電腦直接連線到 VM 上的 Jupyter 伺服器。 

下列各節提供連接到 Jupyter 筆記本的這些方法的詳細資料。 

#### <a name="ssh-to-virtual-machine"></a>透過 SSH 連線到虛擬機器
學生可以從終端機會話，透過 SSH 連線到其 Linux Vm。 如需詳細步驟，請參閱[如何存取教室實驗室](how-to-use-classroom-lab.md)。 如果他們使用的是 Windows 用戶端電腦，則必須從命令提示字元下載[PuTTY](https://www.putty.org/)或[在 Windows 中啟用 OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse) ，以啟用 ssh 用戶端。 

1.  啟動 VM。
2.  VM 執行之後，請按一下 **[連線]**，這會顯示提供 SSH 命令字串的對話方塊，如下列範例所示：
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  移至命令提示字元或終端機，並貼上此命令，然後按**enter**。
4.  輸入密碼以登入 VM。 

一旦學生連線到 Vm，他們就可以在本機存取並執行 Jupyter 筆記本。

#### <a name="x2go-to-virtual-machine"></a>X2Go 至虛擬機器
**資料科學虛擬機器– Ubuntu**映射已布建 X2GO 伺服器，並已準備好接受用戶端連接。 若要連接到 Linux 機器的圖形化桌面，學生必須遵循這些一次性步驟來設定用戶端電腦上的 X2Go：

1.  下載並安裝用戶端平臺的[X2Go 用戶端](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)。
2.  在[Azure 實驗室服務入口網站](https://labs.azure.com)中，確認您要連線的 Linux VM 已啟動。
3.  VM 執行之後，請按一下 **[連線]**，這會顯示提供 SSH 命令字串的對話方塊，如下列範例所示：

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. 一旦您擁有此資訊，請開啟 X2Go 用戶端應用程式，並建立新的會話。 
5.  在 [**會話喜好**設定] 窗格中填入下列值：
    - **會話名稱**：它可以是您想要的任何內容，但我們建議使用您的實驗室 VM 名稱。
     - **主機**：`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **登**入： student
     - **SSH 埠**：12345
     - **會話類型**： XFCE
6. 選取 [確定]。 

    > [!NOTE]
     > 建立新的 X2Go 會話時，請務必使用 SSH 埠，**而不**是 RDP 埠。

現在，若要連接到 VM，請遵循下列步驟：    

1.  在 X2Go 用戶端中，按兩下您想要連接的 VM。 

    ![X2Go 用戶端](./media/class-type-jupyter-notebook/x2go-client.png)
2. 輸入要連接到 VM 的密碼。 （您可能必須授與 X2Go 許可權，才能略過您的防火牆來完成連線。）
3.  您現在應該會看到 Ubuntu 資料科學 VM 的圖形化介面。


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>VM 上 Jupyter 伺服器的 SSH 通道
有些學生可能想要直接從他們的本機電腦連線到其 Vm 內的 Jupyter 伺服器。 SSH 通訊協定可在本機電腦與遠端伺服器之間（在我們的案例中為學生的實驗室 VM）進行埠轉送，以便在伺服器上的特定埠上執行的應用程式會透過通道**傳送**到本機電腦上的對應埠。 學生應遵循下列步驟，對其實驗室 Vm 上的 Jupyter 伺服器進行 SSH 通道：

1.  在[Azure 實驗室服務入口網站](https://labs.azure.com)中，確認您要連線的 Linux VM 已啟動。
2.  VM 執行之後，請按一下 **[連線]**，這會顯示提供 SSH 命令字串的對話方塊，如下列字串所示：

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. 在您的本機電腦上，啟動終端機或命令提示字元，並將 SSH 連接字串複製到其中。 然後，將新增 `-L 8888:localhost:8888` 至命令字串，這**會在**埠之間建立通道。 最後的字串看起來應該像這樣：

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. 按**enter**鍵以執行命令。 
5.  出現提示時，請提供用來連線到實驗室 VM 的密碼。 
6.  一旦您連線到 VM，請使用下列命令啟動 Jupyter 伺服器： 

    ```bash
     jupyter notebook
     ```
7. 執行命令將會在終端機或命令提示字元中提供您的 URL。 URL 看起來應該像這樣：

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. 將此 URL 貼到您本機電腦上的瀏覽器，以連線並使用您的 Jupyter Notebook。 

    > [!NOTE]
    > Visual Studio Code 也可提供絕佳[Jupyter Notebook 編輯體驗](https://code.visualstudio.com/docs/python/jupyter-support)。 您可以遵循[如何連線到遠端 Jupyter 伺服器](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server)的指示，並使用上一個步驟中的相同 URL，從 VS Code （而不是從瀏覽器）連接。 


## <a name="cost-estimate"></a>成本預估
讓我們來討論這個類別的可能成本預估。 我們將使用25名學生的課程。 已排程的類別時間有20小時。 此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。 我們選擇的 VM 大小為小型 GPU （計算），也就是139實驗室單位。 如果您想要使用「小型」（20個實驗室單位）或「中大小」（42實驗室單位），您可以將下列方程式中的實驗室單位部分取代為正確的數位。  

以下是此類別的可能成本預估範例：25個學生 * （20個已排程的時數 + 10 個配額小時） * 139 實驗室單位 * 0.01 美元/小時 = 1042.5 美元

如需定價的詳細資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
在本文中，我們逐步解說了建立 Jupyter 筆記本類別的實驗室的步驟。 您可以針對其他機器學習類別使用類似的設定。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
