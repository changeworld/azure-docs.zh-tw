---
title: 使用搭配 Windows for Linux VM 的 SSH 金鑰
description: 了解如何在 Windows 電腦上產生並使用 SSH 金鑰來連線到 Azure 上的 Linux 虛擬機器。
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4e47b303548707074f51e5f3dd5f8840b3e7b5ed
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372224"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上搭配 Windows 使用 SSH 金鑰

本文描述各種方法讓您能在 Windows 電腦上產生及使用「安全殼層」**(SSH) 金鑰，以在 Azure 中建立並連線到 Linux 虛擬機器 (VM)。 若要從 Linux 或 macOS 用戶端使用 SSH 金鑰，請參閱[快速](mac-create-ssh-keys.md)或[詳細](create-ssh-keys-detailed.md)指南。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows 套件和 SSH 用戶端
您可使用 *SSH 用戶端*來連線及管理 Azure 中的 Linux VM。 執行 Linux 或 macOS 的電腦通常有一組 SSH 命令，可用來產生和管理 SSH 金鑰以及建立 SSH 連線。 

Windows 電腦不一定會安裝類似的 SSH 命令。 最新版本的 Windows 10 提供[OpenSSH 用戶端命令](https://devblogs.microsoft.com/commandline/windows10v1803/)，以建立和管理 ssh 金鑰，並從命令提示字元進行 ssh 連線。 最的 Windows 10 新版本也包含 [Windows Subsystem for Linux](/windows/wsl/about)，可在 Bash 殼層內以原生方式執行和存取公用程式 (例如 SSH 用戶端)。 

您可以在本機安裝的其他常見 Windows SSH 用戶端包含在下列套件中：

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [適用于 Windows 的 Git](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

您也可以使用從 [Azure Cloud Shell](../../cloud-shell/overview.md) Bash 獲得的 SSH 公用程式。 

* 在您的網頁瀏覽器中， [https://shell.azure.com](https://shell.azure.com) 或在[Azure 入口網站](https://portal.azure.com)中存取 Cloud Shell。 
* 安裝 [Azure 帳戶延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)，從 Visual Studio Code 內將 Cloud Shell 當作終端機來存取。

## <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組
下列各節描述在 Windows 上建立 SSH 金鑰組的兩個選項。 您可以使用殼層命令 (`ssh-keygen`) 或 GUI 工具 (PuTTYgen)。 另請注意，使用 Powershell 建立金鑰時，請將公開金鑰上傳為 ssh .com （SECSH）格式。 使用 CLI 時，請在上傳之前，將金鑰轉換為 OpenSSH 格式。 

### <a name="create-ssh-keys-with-ssh-keygen"></a>利用 ssh-keygen 建立 SSH 金鑰

如果您在支援 SSH 用戶端工具的 Windows 上執行命令殼層 (或使用 Azure Cloud Shell)，可使用 `ssh-keygen` 命令來建立 SSH 金鑰組。 輸入下列命令並回答提示。 如果所選位置中存在 SSH 金鑰組，則系統會覆寫這些檔案。 

```bash
ssh-keygen -t rsa -b 2048
```

如需詳細的背景和資訊，請參閱[快速](mac-create-ssh-keys.md)或[詳細](create-ssh-keys-detailed.md)步驟，使用 `ssh-keygen` 來建立 SSH 金鑰。

### <a name="create-ssh-keys-with-puttygen"></a>利用 PuTTYgen 建立 SSH 金鑰

如果您想使用 GUI 工具來建立 SSH 金鑰，則可以使用 [PuTTY 下載套件](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)隨附的 PuTTYgen 金鑰產生器。 

若要使用 PuTTYgen 建立 SSH RSA 金鑰組：

1. 啟動 PuTTYgen。

2. 按一下 [產生]  。 根據預設，PuTTYgen 會產生 2048 位元 SSH-2 RSA 金鑰。

3. 在空白區域中四處移動滑鼠來提供隨機金鑰。

4. 產生公開金鑰之後，選擇性地輸入並確認複雜密碼。 當您使用私密 SSH 金鑰向 VM 進行驗證時，系統會提示您輸入複雜密碼。 若沒有複雜密碼，如果有人取得您的私密金鑰，他們即可登入使用該金鑰的任何 VM 或服務。 我們建議您建立複雜密碼。 不過如果您忘記此複雜密碼，將無法加以復原。

5. 公開金鑰會顯示在視窗的頂端。 當您建立 Linux VM 時，可以複製此整個公開金鑰，然後將它貼入 Azure 入口網站或 Azure Resource Manager 範本中。 您也可以選取 [**儲存公開金鑰**]，將複本儲存到您的電腦。 請注意，儲存至檔案時，PuTTY 會將公開金鑰轉換成不同的格式[RFC4716](https://tools.ietf.org/html/rfc4716)。 RFC4716 格式可能與所有 Api 不相容。 因此，若要在 Azure 入口網站中使用，建議您複製 PuTTY 視窗中顯示的公開金鑰。

    ![儲存 PuTTY 公用金鑰檔案](./media/ssh-from-windows/save-public-key.png)

6. (選擇性) 若要將私密金鑰儲存成 PuTTy 私密金鑰格式 (.ppk 檔案)，請選取 [儲存私密金鑰]****。 稍後您將需要 .ppk 檔案，才能使用 PuTTY 來建立 VM 的 SSH 連線。

    ![儲存 PuTTY 私密金鑰檔案](./media/ssh-from-windows/save-ppk-file.png)

    如果您想要將私密金鑰儲存為 OpenSSH 格式（許多 SSH 用戶端所使用的私密金鑰格式），請選取 [**轉換**] [  >  **匯出 OpenSSH 金鑰**]。

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>部署 VM 時，提供 SSH 公開金鑰

若要建立使用 SSH 金鑰進行驗證的 Linux VM，請在使用 Azure 入口網站或其他方法建立 VM 時，提供您的 SSH 公開金鑰。

下列範例顯示當您建立 Linux VM 時，如何複製此公開金鑰並貼到 Azure 入口網站中。 此公開金鑰通常會接著儲存在新 VM 上的 ~/.ssh/authorized_key 目錄中。

   ![當您在 Azure 入口網站建立 VM 時使用公開金鑰](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>連接到您的 VM

要從 Windows 建立與 Linux VM 的 SSH 連線，有一個方法是使用 SSH 用戶端。 如果您已在 Windows 系統上安裝 SSH 用戶端，或使用 Azure Cloud Shell Bash 的 SSH 工具，則這是慣用的方法。 如果您偏好 GUI 工具，可以使用 PuTTY 來連線。  

### <a name="use-an-ssh-client"></a>使用 SSH 用戶端
公開金鑰已部署到您的 Azure VM 且私密金鑰儲存在本機系統中，此時可以使用 VM 的 IP 位址或 DNS 名稱，透過 SSH 連線到您的 VM。 將下列命令中的 *azureuser* 和 *myvm.westus.cloudapp.azure.com* 換成系統管理員使用者名稱和完整網域名稱 (或 IP 位址)：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果您在建立金鑰組時設定了複雜密碼，請在登入程序期間出現提示時輸入複雜密碼。

如果 VM 使用 Just-In-Time 存取原則，您必須先要求權限，才能連線到 VM。 如需 Just-In-Time 原則的詳細資訊，請參閱[使用 Just-In-Time 原則管理虛擬機器存取](../../security-center/security-center-just-in-time.md)。

### <a name="connect-with-putty"></a>使用 PuTTY 進行連線

如果您已安裝 [PuTTY 下載套件](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)而且之前產生了 PuTTY 私密金鑰 (.ppk) 檔案，便可以使用 PuTTY 連線至 Linux VM。

1. 啟動 PuTTY。

2. 從 Azure 入口網站填入 VM 的主機名稱或 IP 位址：

    ![開啟新的 PuTTY 連線](./media/ssh-from-windows/putty-new-connection.png)

3. 選取 [**連線**  >  **SSH**  >  **驗證**] 類別。 瀏覽至您的 PuTTY 私密金鑰 (.ppk 檔案) 並加以選取︰

    ![選取您的 PuTTY 私密金鑰進行驗證](./media/ssh-from-windows/putty-auth-dialog.png)

4. 按一下 [開啟]**** 來連線到 VM。

## <a name="next-steps"></a>後續步驟

* 如需使用 SSH 金鑰的詳細步驟、選項和 advanced 範例，請參閱[建立 ssh 金鑰組的詳細步驟](create-ssh-keys-detailed.md)。

* 您也可以在 Azure Cloud Shell 使用 PowerShell 來產生 SSH 金鑰，並建立 Linux VM 的 SSH 連線。 請參閱 [PowerShell 快速入門](../../cloud-shell/quickstart-powershell.md#ssh)。

* 如果您無法使用 SSH 連線到 Linux VM，請參閱[針對 Azure Linux VM 的 SSH 連線進行疑難排解](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)。
