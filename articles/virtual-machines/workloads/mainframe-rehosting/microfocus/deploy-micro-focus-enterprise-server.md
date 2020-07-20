---
title: 將微焦點企業伺服器5.0 部署至 AKS |Microsoft Docs
description: 使用 Azure 虛擬機器（Vm）上的微焦點開發和測試環境，重新裝載您的 IBM z/OS 大型主機工作負載。
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 900e76fff3e5ccf88fa1e25ebea97f26e406a358
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610365"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>將微焦點企業伺服器5.0 部署到 AKS

在另一[篇文章](https://docs.microsoft.com/azure/virtual-machines/workloads/mainframe-rehosting/microfocus/run-enterprise-server-container)中，我已概述在 Docker 容器中執行微焦點企業伺服器5.0 的步驟。 接下來，我想要示範如何進一步部署您所建立的 Docker 映射，以 Azure Kubernetes Service （AKS）。

Azure Kubernetes Service 是以 Kubernetes 為基礎的受控協調流程服務。 它可讓您在容器主機的叢集上部署、調整及管理 Docker 容器（和其他容器型應用程式）。

這是三個步驟的程式。 您必須：

1.  建立 Azure Container Registry 來儲存您的 Docker 映射。

2.  建立 Azure Kubernetes 叢集以執行 Docker 映射。

3.  執行應用程式。

這可讓您在 Azure 中向外延展（並相應減少）您的大型主機現代化工作負載，並充分利用雲端平臺。

準備好? 讓我們開始吧！

## <a name="create-the-azure-container-registry"></a>建立 Azure Container Registry

從 [Azure 入口網站中，選取左上角的 [**建立資源**]。 從 Marketplace 儀表板中，依序選取 [**容器] 和**[ **Container Registry**]。 這會帶您前往 [**建立容器**登錄] 窗格，您需要在此填入登錄**名稱**、 **Azure 訂**用帳戶、**資源群組**和**位置**。 登錄**名稱**需要解析，因此它必須是唯一的。 從先前的 blog 文章和相同的對應**位置**選取您使用的**資源群組**。 針對 [**管理使用者**] 和 **[基本**] 選取 [**啟用**] 作為**SKU**。 填入所有內容之後，請選取 [**建立**]。

![建立 container registry 介面](media/deploy-image-1.png)

部署登錄之後，請選取 [**移至資源**]。 這會帶您前往 Azure Container Registry 的主要分頁。 這裡有一個不錯的功能，就是 [**快速入門**] 功能表選項。 選取它，您就會看到在登錄中推送和提取映射所需完成的指示。 讓我們來看一下：

1.  **安裝 Docker** –您不需要擔心這種情況，因為它已經完成。

2.  **執行 "hello – world" 基底映射**-同樣地，這並非必要，但您可以隨意試用。

3.  **登入您的 container registry** –您必須從虛擬機器（VM）執行此動作。 將命令複製到剪貼簿或記事本。

    針對我所建立的登錄，命令為： **docker login acrmf50.azurecr.io**

4.  **推送至您**的登錄–您必須在登入 VM 之後，針對微焦點映射執行此動作。

5.  **從您**的登錄提取–這與此逐步解說無關，但如果您需要執行另一個 Docker 映射，請務必瞭解。

在您離開入口網站之前，您必須先取得登錄的認證，以便您可以登入。 結束 [**快速啟動**] 分頁，然後從 [登錄] 功能表中選取 [**存取金鑰**]。 將 [使用者**名稱**] 和其中一個**密碼**（有兩個）複製到剪貼簿或 [記事本]。 您稍後將需要用到它們來登入。

現在您已經知道需要完成的動作，請登入 VM。

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>透過 RDP 連線到您用來建立 Docker 映射的虛擬機器

因為您已經在 Windows 2016 伺服器上建立 Docker 映射，所以您必須登入該 VM。 從這個 VM，您可以將映射推送至您剛才建立的 Azure Container Registry。 流覽至 Azure 入口網站中的 VM，然後依序選取 [**總覽** **] 和 [連線]**。 這會透過遠端桌面通訊協定（RDP）將您連接至 VM。 當您建立 VM 時，您需要使用中的認證。

## <a name="log-in-and-push-the-image-to-the-registry"></a>登入並將映射推送至登錄

登入之後，請開啟命令提示字元，並起始下列 Docker 命令：

-   **docker 映射**–這會顯示 VM 上所有目前已安裝的映射清單。 請記下**microfocus/es acctdemo** ，因為這是您將使用的帳戶。

-   **docker 登入 acrmf50.azurecr.io** –正確的格式是*docker 登 \<registry name\> *入。 取代您在建立登錄時所使用的任何名稱。

    -   您將需要從 Azure 入口網站複製的使用者**名稱**和**密碼**。 您應該會看到類似下圖的畫面。

    ![系統管理員命令提示字元的螢幕擷取畫面](media/deploy-image-2.png)

-   **docker tag microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** –這會使用存放庫的名稱來標記適當的映射。 **注意**：如果名稱 \<microfocus/es-acctdemo\> 無法運作，請嘗試使用完整的映射識別碼。 執行命令之後，請輸入**docker images – no-trunc**。 您應該會看到類似下圖的內容。 請注意，影像已正確加上標籤。

    ![[選取系統管理員命令提示字元] 畫面](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** –這會啟動實際推送至您的存放庫。 因為大小為 15 GB，所以需要幾分鐘的時間來執行。 如果一切都正確，您應該會看到類似下圖的內容。

    ![系統管理員命令提示字元畫面](media/deploy-image-4.png)

現在請回到 Azure 入口網站，特別是針對存放**庫**。 在**儲存**機制的功能表中，選取 [**存放庫**]，您應該會看到**acctdemo**列出。 現在，請建立 AKS 叢集。

## <a name="create-the-azure-kubernetes-aks-cluster"></a>建立 Azure Kubernetes （AKS）叢集

從 [Azure 入口網站] 中，選取 [**建立資源**]，然後從 [ **Marketplace** ] 功能表選取 [**容器/Kubernetes 服務**]。 接下來，您需要填寫 [**建立 Kubernetes**叢集] 分頁。 請務必將叢集保留在您已使用的相同區域和資源群組中。 除了**節點計數以外，** 您可以接受其餘預設值，這只需要為1。 當您完成時，選取 [檢閱 + 建立]。

![建立 Kubernetes 叢集畫面](media/deploy-image-5.png)

完成時，部署會將**Kubernetes 服務**成品放在您在分頁上選取的**資源群組**中。 不過，實際的叢集在部署期間會建立自己的資源群組。 如果您從左側功能表中選取 [**資源群組**]，您可以根據命名慣例來尋找它。 以下是「我的映射」–這是清單中的最後一個影像。

![資源群組的螢幕擷取畫面](media/deploy-image-6.png)

**執行映像**

現在可以提取映射，並在 AKS 中執行它。 從 Azure 入口網站執行此動作的最簡單方式是使用 Cloud Shell。 您會在 Azure 入口網站右上方找到圖示。 請注意，在此逐步解說中，我使用 Bash Shell。

![Cloud Shell 圖示的螢幕擷取畫面](media/deploy-image-7.png)

載入 shell 之後，輸入下列命令：

**kubectl 執行 es-acctdemo--image acrmf50.azurecr.io/es-acctdemo--port = 9040**

這會從**acrmf50.azurecr.io**存放庫提取映射，並將其載入 AKS。 然後，它會執行已開啟埠9040的映射。 您可能還記得這是您為 Docker 映射開啟的埠。 您需要該許可權才能存取企業伺服器。

Kubernetes 應該會回應已建立部署的訊息。

![部署訊息的螢幕擷取畫面](media/deploy-image-8.jpg)

若要查看容器是否確實正在執行，請輸入： **kubectl get**pod。

您應該會看到 acctdemo 做為執行中的 pod，如下圖所示。

![螢幕擷取畫面： acctdemo 為執行中的 pod](media/deploy-image-9.png)

恭喜！ 您現在正在 Azure Kubernetes Service 中執行企業伺服器。 在下一篇文章中，我將示範如何存取企業伺服器系統管理主控台，以及如何運用 Kubernetes 來相應放大您的部署。
