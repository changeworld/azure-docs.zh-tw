---
title: 存取 Avere vFXT 控制台 - Azure
description: 如何連線到 vFXT 叢集和瀏覽器型 Avere 控制台以設定 Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342428"
---
# <a name="access-the-vfxt-cluster"></a>存取 vFXT 叢集

若要調整叢集設定並監視叢集，請使用 Avere 主控台。 Avere 控制台是叢集的瀏覽器型圖形化介面。

由於 vFXT 叢集位於私人虛擬網路內，因此您必須建立 SSH 通道，或使用其他方法來連接叢集的管理 IP 位址。

有兩個基本步驟：

1. 在您的工作站與私人虛擬網路之間建立連線
1. 在網頁瀏覽器中載入叢集的控制台

> [!NOTE]
> 本文假設您已經在叢集中控制器上，或在叢集虛擬網路內的另一個 VM 上，設定公用 IP 位址。 本文說明如何使用該 VM 作為主機來存取叢集。 如果您使用 VPN 或 ExpressRoute 來存取虛擬網路，請跳到連線 [到 Avere 主控台](#connect-to-the-avere-control-panel-in-a-browser)。

連線之前，請確定本機電腦上已安裝建立叢集控制器時所使用的 SSH 公開/私密金鑰組。 如果您需要協助，請閱讀 [Windows](../virtual-machines/linux/ssh-from-windows.md) 或 [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 適用的 SSH 金鑰文件。 如果您使用的是密碼而非公開金鑰，則當您連接時，系統會提示您輸入密碼。

## <a name="create-an-ssh-tunnel"></a>建立 SSH 通道

您可以從以 Linux 為基礎或 Windows 10 用戶端系統的命令列建立 SSH 通道。

使用具有下列格式的 SSH 通道命令：

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

此命令會透過叢集控制器的 IP 位址，連線到叢集的管理 IP 位址。

範例：

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

如果您使用 SSH 公開金鑰建立叢集，而且用戶端系統上已安裝相符的金鑰，便會自動驗證。 如果您使用密碼，系統將會提示您輸入密碼。

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>在瀏覽器中，連線到 Avere 控制台

此步驟會使用網頁瀏覽器連接到 vFXT 叢集上的設定公用程式。

* 針對 SSH 通道連線，開啟網頁瀏覽器並巡覽至 `https://127.0.0.1:8443` 。

  您在建立通道時連線到叢集 IP 位址，因此您只需要在瀏覽器中使用 localhost IP 位址。 如果您使用 8443 以外的本機連接埠，請改為使用連接埠號碼。

* 如果您使用 VPN 或 ExpressRoute 連至叢集，請在瀏覽器中瀏覽至叢集管理 IP 位址。 範例： ``https://203.0.113.51``

根據您的瀏覽器，您可能需要按一下 [進階]****，並確認否安全地前往到該頁面。

輸入您在建立叢集時所提供的使用者名稱 `admin` 和系統管理密碼。

![填入使用者名稱 'admin' 和密碼的 Avere 登入頁面螢幕擷取畫面](media/avere-vfxt-gui-login.png)

按一下 [登入]****，或按鍵盤上的 Enter 鍵。

## <a name="next-steps"></a>後續步驟

登入叢集的 [控制台] 之後，請啟用 [支援](avere-vfxt-enable-support.md)。