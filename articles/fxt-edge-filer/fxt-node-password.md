---
title: 教學課程：初始化硬體 - Azure FXT Edge Filer
description: 了解如何連線到硬體節點，並在 Azure FXT Edge Filer 節點上設定初始密碼。
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218845"
---
# <a name="tutorial-set-hardware-passwords"></a>教學課程：設定硬體密碼

您第一次開啟 Azure FXT Edge Filer 節點的電源時，必須設定根密碼。 硬體節點並未隨附預設密碼。

在設定密碼且根使用者登入前，網路連接埠將會停用。

請在安裝節點並連接其纜線之後、嘗試建立叢集之前執行此步驟。

本教學課程說明如何連線至硬體節點並設定密碼。 其也會說明如何新增 BIOS 設定密碼，協助保護節點的安全。

在本教學課程中，您將學會如何：

> [!div class="checklist"]
>
> * 將鍵盤和監視器連接到節點並開啟其電源
> * 設定 BIOS 設定密碼
> * 為此節點上的 iDRAC 連接埠和根使用者設定密碼
> * 以根使用者身分登入

為您要在叢集中使用的每個節點重複這些步驟。

本教學課程需要約 15 分鐘才能完成。

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，請先完成下列步驟：

* 在設備機架中[安裝](fxt-install.md)每個 Azure FXT Edge Filer 節點，並連結電源線和網路存取，如[先前的教學課程](fxt-network-power.md)所說明。
* 尋找可連結至硬體節點的 USB 連線鍵盤和 VGA 連線監視器。 (節點的序列埠在您設定密碼之前不會有作用。)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>將鍵盤和監視器連接到節點

將監視器和鍵盤實際連接到Azure FXT Edge Filer 節點。

* 將監視器連接到 VGA 連接埠。
* 將鍵盤連接到其中一個 USB 連接埠。

使用此參考圖表找出機箱背面的連接埠。

> [!NOTE]
> 序列埠在密碼設定後才會有作用。

![標示了序列埠、VGA 和 USB 連接埠的 Azure FXT Edge Filer 背面圖](media/fxt-back-serial-vga-usb.png)

如果您想要將多個節點連接到相同的週邊設備，您可以使用 KVM 交換器。

按下正面的電源按鈕開啟節點的電源。

![Azure FXT Edge Filer 正面圖 - 圓形電源按鈕標示於右上方](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>建立 BIOS 設定密碼

BIOS 設定密碼會保護節點的 BIOS 設定，使其免於遭受意外或未經授權的變更。 建立叢集時不需要此密碼，但叢集安全性策略強烈建議使用。

若要建立 BIOS 設定密碼：

1. 開啟或重新啟動節點，然後立即按 F2 以開啟系統設定公用程式。

1. 在 [系統設定主功能表] 畫面上，選擇 [系統 BIOS] > [系統安全性]。

1. 確定 [密碼狀態] 設定為 [已解除鎖定]。

1. 使用 [設定密碼] 欄位來設定密碼。 (如果您想要使用密碼，也可以從這個畫面設定系統 BIOS 密碼。)

1. 按 Esc 鍵返回 [系統 BIOS] 畫面，然後再次按 Esc 鍵。 畫面會出現一則訊息，提示您儲存變更。 如果系統未自動重新啟動，請將其重新啟動，以進入一般啟動畫面。<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>設定初始密碼

Azure FXT Edge Filer 節點在開機時會將多種訊息列印至監視器。 幾分鐘後，它會顯示如下的初始設定畫面：

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

您輸入的密碼會用於兩方面：

* 它是此 Azure FXT Edge Filer 節點的暫時根密碼。

  當您使用此節點建立叢集時，或是您將此節點新增至叢集時，此密碼就會變更。 叢集管理密碼 (與使用者 ``admin`` 相關聯) 也是叢集中所有節點的根密碼。

* 它是 iDRAC/IPMI 硬體管理連接埠的長期密碼。

  請確實記下此密碼，以備您後續需要登入 IPMI 以排解硬體問題時使用。

輸入密碼並加以確認：

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

您輸入密碼後，系統會繼續開機。 開機完成後，系統會顯示 ``login:`` 提示。

## <a name="sign-in-as-root"></a>以根使用者身分登入

使用您剛設定的密碼以 ``root`` 身分登入。

```
login: root
Password:**********
```

以根使用者身分登入後，網路連接埠會開始作用，並聯繫 DHCP 伺服器的 IP 位址。

## <a name="next-steps"></a>後續步驟

節點已就緒，可作為叢集的一部分。 您可以用它來建立 Azure FXT Edge Filer 叢集，或者，您可以[將它新增至現有的叢集](fxt-add-nodes.md)。

> [!div class="nextstepaction"]
> [建立叢集](fxt-cluster-create.md)
