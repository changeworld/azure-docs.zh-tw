---
title: 教學課程 - 在 Azure 上建立 Jenkins 伺服器
description: 在本教學課程中，您會從 Jenkins 方案範本在 Azure Linux 虛擬機器上安裝 Jenkins，並建置範例 Java 應用程式。
keywords: jenkins, azure, devops, 入口網站, 虛擬機器, 解決方案範本
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274527"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>教學課程：在 Azure Linux VM 上建立 Jenkins 伺服器 

本教學課程示範如何在 Ubuntu Linux VM 上安裝 [Jenkins](https://jenkins.io)，以及設定為使用 Azure 的工具和外掛程式。 當您完成時，您可讓在 Azure 中執行的 Jenkins 伺服器從 [GitHub](https://github.com) 建置範例 Java 應用程式。

> [!div class="checklist"]
> * 在 Azure 上安裝及設定 Jenkins 伺服器
> * 使用 SSH 通道存取 Jenkins 主控台
> * 建立自由樣式專案
> * 編譯程式碼及封裝範例應用程式

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]