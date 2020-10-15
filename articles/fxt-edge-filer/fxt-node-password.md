---
title: 教學課程：初始化硬體 - Azure FXT Edge Filer
description: 了解如何連線到硬體節點，並在 Azure FXT Edge Filer 節點上設定初始密碼。
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4fae7795cdd61e3eb465285163bcd6c0dd9f7db2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184919"
---
# <a name="tutorial-set-hardware-passwords"></a>教學課程：設定硬體密碼

您第一次開啟 Azure FXT Edge Filer 節點的電源時，必須設定根密碼。 硬體節點並未隨附預設密碼。 

在設定密碼且根使用者登入前，網路連接埠將會停用。

請在安裝節點並連接其纜線之後、嘗試建立叢集之前執行此步驟。 

本教學課程說明如何連線至硬體節點並設定密碼。 

在本教學課程中，您將學會如何： 

> [!div class="checklist"]
> * 將鍵盤和監視器連接到節點並開啟其電源
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

## <a name="set-initial-passwords"></a>設定初始密碼 

Azure FXT Edge Filer 節點在開機時會將多種訊息列印至監視器。 幾分鐘後，它會顯示如下的初始設定畫面：

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
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
