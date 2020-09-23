---
title: 建立資料控制器
description: 在您已部署的一般多節點 Kubernetes 叢集中建立 Azure Arc 資料控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 81c4558bde3ffdbec72a756562b972c2eed4a1ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934622"
---
# <a name="create-the-azure-arc-data-controller"></a>建立 Azure Arc 資料控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>建立 Azure Arc 資料控制器的總覽

您可以使用多種不同的方法，在多個不同類型的 Kubernetes 叢集和受控 Kubernetes 服務上建立 Azure Arc 啟用的資料服務。

目前，支援的 Kubernetes 服務和散發版本清單如下：

- Azure Kubernetes Service (AKS)
- Azure Stack 上的 Azure Kubernetes Service 引擎 (AKS 引擎) 
- Azure Stack HCI 上的 Azure Kubernetes Service
- Azure RedHat OpenShift (ARO)
- OpenShift 容器平臺 (OCP) 
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE) 
- 開放原始碼，上游 Kubernetes 一般使用 kubeadm 部署

> [!IMPORTANT]
> * 最小支援的 Kubernetes 版本為 v 1.14。
> * 若要瞭解您的環境與 Azure 之間的連線能力，請參閱連線 [需求](connectivity.md) 。
> * 如要瞭解如何設定持續性儲存體的詳細資料，請參閱 [存放裝置設定指引](storage-configuration.md) 。
> * 如果您使用 Azure Kubernetes Service，您叢集的背景工作節點 VM 大小至少應為 **Standard_D8s_v3** 並使用 **premium 磁片。** 
> * 如果您使用另一個 Kubernetes 散發或服務，您應該確定您的節點大小下限為 8 GB RAM 和4個核心，以及所有 Kubernetes 節點上可用 32 GB RAM 的總容量總計。 例如，您可以在 32 GB RAM 和4個核心上有1個節點，也可能有2個節點具有 16 GB RAM 和4個核心。

> [!NOTE]
> 如果您在 Azure 上使用 Red Hat OpenShift 容器平臺，建議使用最新的可用版本。

視您選擇的選項而定，將 _需要_某些工具，但建議您在開始建立 Azure Arc 資料控制器之前，先 [安裝所有用戶端工具](install-client-tools.md) 。

無論您選擇哪一個選項，在建立過程中，您都必須提供下列資訊：

- **資料控制器名稱** -資料控制器的描述性名稱，例如「生產資料控制器」、「西雅圖資料控制器」。
- **資料控制器使用者名稱** -資料控制器系統管理員使用者的任何使用者名稱。
- **資料控制器密碼** -資料控制器系統管理員使用者的密碼。
- **Kubernetes 命名空間的名稱** -您想要在其中建立資料控制器的 Kubernetes 命名空間名稱。
- 連線**能力模式**-叢集的連線[模式](connectivity.md)。 目前僅支援「間接」。
- **Azure 訂** 用帳戶識別碼-azure 訂用帳戶 GUID，適用于您想要在 azure 中建立資料控制器資源的位置。
- **Azure 資源組名** -您想要在 Azure 中建立資料控制器資源的資源組名。
- **Azure 位置** -資料控制器資源中繼資料將儲存在 azure 中的 azure 位置。 如需可用區域的清單，請參閱 [Azure 全球基礎結構/產品（依區域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)）。

## <a name="next-steps"></a>下一步

有多個選項可用於建立 Azure Arc 資料控制器：

> **只想試試看嗎？**  
> 快速開始使用 [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 快速入門 AZURE KUBERNETES SERVICE (AKS) 、AWS 彈性 Kubernetes 服務 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或在 Azure VM 中！
> 
- [使用 Azure Data CLI 建立資料控制器 (azdata) ](create-data-controller-using-azdata.md)
- [使用 Azure Data Studio 建立資料控制器](create-data-controller-azure-data-studio.md)
- [透過 Azure Data Studio 中的 Jupyter 筆記本，從 Azure 入口網站建立資料控制器](create-data-controller-resource-in-azure-portal.md)
- [使用 Kubernetes 工具（例如 kubectl 或 oc）來建立資料控制器](create-data-controller-using-k8s-native-tools.md)
- [使用 Azure Arc 快速入門建立資料控制器，以獲得測試部署的加速體驗](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)
