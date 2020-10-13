---
title: 'Azure Kubernetes Service (AKS 上的機密容器) '
description: 深入瞭解機密容器上未修改的容器支援。
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994841"
---
# <a name="confidential-containers"></a>機密容器

## <a name="overview"></a>概觀

讓開發人員將 **現有的 docker 應用程式帶入 (新的或現有的) ** ，並透過機密運算節點支援，安全地在 AZURE KUBERNETES SERVICE (AKS) 上執行。

機密容器可協助保護：

- 資料完整性 
- 資料機密性
- 程式碼完整性
- 透過加密的容器程式碼保護
- 以硬體為基礎的保證
- 允許執行現有的應用程式
- 建立硬體根目錄信任

以硬體為基礎的受信任執行環境 (t) 是一個重要元件，它是用來透過受信任運算基礎 (TCB) 元件的硬體和軟體測量，提供強大的保證。 這些測量的驗證有助於驗證預期的計算，並確認容器應用程式的任何篡改。

機密容器支援使用 **Python、JAVA、NODE JS 等開發的自訂應用程式，或 NGINX、Redis Cache、MemCache 等封裝的軟體應用程式**，在 AKS 上執行修改。

機密容器是容器機密性的最快路徑，包括透過加密的容器保護，讓您的商務邏輯不需要/最少量的變更就能進行增益和轉移。

![機密容器轉換](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>機密容器啟用

若要執行現有的 docker 容器，機密運算節點上的應用程式需要抽象層或 SGX 軟體，才能利用特殊的 CPU 指令集。 SGX 軟體也可讓您的機密應用程式程式碼受到保護，並建立對 CPU 的直接執行，以移除虛擬作業系統、主機 OS 或虛擬程式。 這種保護可減少作業系統或虛擬程式層級的整體介面攻擊區域和弱點。

機密容器完全支援 AKS，並可透過 Azure 合作夥伴和開放原始碼軟體 (OSS) 專案來啟用。 開發人員可以選擇以功能為基礎的軟體提供者、整合至 Azure 服務和工具支援。

## <a name="partner-enablers"></a>合作夥伴啟用
> [!NOTE]
> 以下是透過 Azure 合作夥伴提供的解決方案，可能會產生授權費用。 請單獨驗證合作夥伴軟體條款。 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 為開發人員提供了入口網站和 CLI 體驗的選擇，可將其容器化應用程式轉換成可支援 SGX 的機密容器，而不需要修改或重新編譯應用程式。 Fortanix 可讓您彈性地執行及管理最廣泛的應用程式集合，包括現有的應用程式、新的記憶體保護區原生應用程式，以及預先封裝的應用程式。 使用者可以開始使用 [記憶體保護區 Manager](https://em.fortanix.com/) UI 或 [REST api](https://www.fortanix.com/api/em/) ，並遵循 Azure Kubernetes Service 的 [快速入門](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) 指南來建立機密容器。

![Fortanix 部署流程](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain) 

[SCONE](https://scontain.com/index.html?lang=en) 支援可產生憑證、金鑰和秘密的安全性原則，並確保只有應用程式的證明服務可以看到這些原則。 如此一來，應用程式的服務就會透過 TLS 自動彼此證明，而不需要修改應用程式和 TLS。 這會透過簡單 Flask 應用程式的說明來說明： https://sconedocs.github.io/flask_demo/  

SCONE 可將現有的大部分二進位檔轉換成在記憶體保護區內執行的應用程式，而不需要變更應用程式或重新編譯應用程式。 SCONE 也會將資料檔案和 Python 程式碼檔案加密，藉此保護轉譯的語言，例如 Python。 有了 SCONE 的安全性原則，就可以保護加密的檔案，避免未經授權的存取、修改和復原。 如何「sconify」現有的 Python 應用程式會在[這裡](https://sconedocs.github.io/sconify_image/)說明

![Scontain 流程](./media/confidential-containers/scone-workflow.png)

具有 AKS 的機密運算節點上的 Scone 部署已完全受到支援和整合。 從這裡開始使用範例應用程式 https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 提供 SGX 平臺軟體，可讓您在 AKS 上執行未修改的容器。 深入瞭解**即將推出**的功能和[使用者流程。](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

在[這裡](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)開始使用範例 Redis 快取和 Python 自訂應用程式

![Anjuna 流程](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS 啟用碼 
> [!NOTE]
> 下列解決方案是透過開放原始碼專案提供，且不會直接與 Azure 機密運算 (ACC) 或 Microsoft 關聯。  

### <a name="graphene"></a>石墨 烯

[Graphene](https://grapheneproject.io/) 是輕量的虛擬作業系統，其設計目的是要以基本主機需求來執行單一 Linux 應用程式。 Graphene 可在隔離的環境中執行應用程式，其優點相當於執行完整的 OS，且具有良好的工具支援，可將現有的 docker 容器應用程式轉換為 Graphene 防護的容器， (GSC) 。

[在 AKS 上](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)開始使用範例應用程式和部署

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) 是記憶體安全、多進程程式庫 OS (LibOS) 適用于 Intel SGX。 它可讓繼承應用程式在 SGX 上執行，幾乎不需要修改原始程式碼。 Occlum 會以透明的方式保護使用者工作負載的機密性，同時讓您輕鬆隨即轉移至現有的 docker 應用程式。

Occlum 支援 AKS 部署。 依照[此處](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)的各種範例應用程式進行部署指示


## <a name="confidential-containers-demo"></a>機密容器示範
觀看機密容器的機密醫療保健示範。 您可以從 [這裡](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)取得範例。 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>取得觸控

您是否有任何問題，或想要成為啟用程式？ 傳送電子郵件給 acconaks@microsoft.com

## <a name="reference-links"></a>參考連結

[Microsoft Azure 證明](../attestation/overview.md)

[DCsv2 虛擬機器](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
