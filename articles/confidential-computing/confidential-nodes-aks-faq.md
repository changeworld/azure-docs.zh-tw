---
title: 'Azure Kubernetes Service (AKS 上支援機密節點的常見問題) '
description: 尋找 Azure Kubernetes Service (AKS) & Azure 機密運算 (ACC) 節點支援的一些常見問題解答。
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: b4b6c04a1e35031387a006b6350ebf6cbe502fee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994873"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS 上有關機密運算節點的常見問題) 

本文說明 Azure Kubernetes Service (AKS) 上 Intel SGX 型機密運算節點的常見問題。 如果您有任何進一步的問題，請傳送電子郵件給您 acconaks@microsoft.com 。

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>預覽期間會提供哪些服務等級協定 (SLA) 和 Azure 支援？ 

產品預覽期間不提供 SLA，如 [這裡](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)所定義。 不過，產品支援是透過 Azure 支援提供。

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>何謂證明，以及我們如何證明在記憶體保護區中執行的應用程式？ 

證明是示範和驗證軟體是否已在特定硬體平臺上正確具現化的程式。 它也可確保其辨識項可以驗證，以保證它是在安全的平臺上執行，而且尚未遭到篡改。 [深入](attestation.md) 瞭解記憶體保護區 apps 的證明如何完成。

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>我可以將現有的容器化應用程式，並使用 Azure 機密運算在 AKS 上執行嗎？ 

是，如需平臺啟用程式的詳細資訊，請參閱 [機密容器頁面](confidential-containers.md) 。

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>AKS 映射中所安裝的 Intel SGX 驅動程式版本為何？ 

目前，Azure 機密計算 DCSv2 Vm 會與 Intel SGX DCAP 1.33 一起安裝。 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>如果我遇到問題，是否可以開啟 Azure 支援票證？ 

可以。 預覽期間會提供 Azure 支援。 因為產品處於預覽階段，所以未附加 SLA。

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>我可以將後置安裝腳本/自訂驅動程式插入 AKS 布建的節點嗎？ 

不可以。 以[AKS 為基礎的機密運算節點](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)支援允許自訂安裝的機密運算節點。

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>我應該使用 Docker 基礎映射來開始記憶體保護區應用程式嗎？ 

 (Isv 和 OSS 專案的各種啟用方式，) 提供啟用機密容器的方式。 查看 [ [機密容器] 頁面](confidential-containers.md) ，以取得更多詳細資料和個別的執行參考。

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>我可以使用其他標準 AKS Sku 來執行 ACC 節點 (建立非叢集節點集區叢集) 嗎？ 

是的，您可以在相同的 AKS 叢集中執行不同的節點集區，包括 ACC 節點。 若要將記憶體保護區應用程式的目標設為特定節點集區，請考慮新增節點選取器或套用 EPC 限制。 如需詳細資訊，請參閱 [此處](confidential-nodes-aks-get-started.md)的機密節點快速入門。

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>我可以使用 ACC 執行 Windows 節點和 windows 容器嗎？ 

目前沒有。 如果您有 Windows 節點或容器需求，請洽詢我們。 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>如果容器大小超過可用的 EPC 記憶體，該怎麼辦？ 

EPC 記憶體會套用至應用程式的一部分，以在記憶體保護區中執行程式設計。 容器的總大小不是比較最大可用 EPC 記憶體的正確方式。 事實上，DCSv2 具有 SGX 的機器，允許最多 32 GB 的 VM 記憶體，讓應用程式不受信任的部分利用。 但是，如果您的容器取用超過可用的 EPC 記憶體，則在記憶體保護區中執行之程式部分的效能可能會受到影響。

若要更妥善地管理背景工作節點中的 EPC 記憶體，請考慮透過 Kubernetes 以 EPC 記憶體為基礎的限制管理。 遵循下列範例作為參考

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>如果我的記憶體保護區耗用超過最多可用的 EPC 記憶體，會發生什麼事？ 

可用的 EPC 記憶體總計可在相同 Vm 或背景工作節點中的記憶體保護區應用程式之間共用。 如果您的應用程式使用 EPC 記憶體超過可用的記憶體，則應用程式效能可能會受到影響。 基於這個理由，建議您在部署 yaml 檔中設定每個應用程式的 >toleration，以更妥善地管理每個背景工作節點的可用 EPC 記憶體，如上述範例所示。 或者，您隨時都可以選擇在背景工作節點集區 VM 大小上移，或新增更多節點。 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>為什麼我無法在記憶體保護區應用程式中執行多個進程， ( # A1 和 exec 的分支？ 

目前，Azure 機密計算 DCsv2 SKU Vm 支援在記憶體保護區中執行之程式的單一位址空間。 單一程式是目前針對高安全性而設計的限制。 不過，機密容器啟用可能會有替代的實作為解決這項限制。

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>您是否會自動安裝任何額外的 daemonset 來公開 SGX 驅動程式？ 

可以。 Daemonset 的名稱為 sgx-裝置外掛程式和 sgx-報價協助程式。 請參閱 [這裡](confidential-nodes-aks-overview.md)的個別用途。  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>我應該為機密運算節點選擇的 VM SKU 為何？ 

DCSv2 Sku。 [DCSv2 sku](../virtual-machines/dcv2-series.md)可在[支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)中使用。

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>我是否仍然可以在機密運算節點上排程和執行非記憶體保護區的容器？ 

可以。 Vm 也有可執行標準容器工作負載的一般記憶體。 在您決定部署模型之前，請先考慮應用程式的安全性與威脅模型。

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>我可以透過 Azure 入口網站透過 DCSv2 節點集區布建 AKS 嗎？ 

可以。 Azure CLI 也可以用來做為 [此處](confidential-nodes-aks-get-started.md)所述的替代方案。

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>支援哪些 Ubuntu 版本和 VM 產生？ 

第2代上的18.04。 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>我們可以在 AKS 上變更目前的 Intel SGX DCAP 驅動程式版本嗎？ 

不可以。 若要執行任何自訂安裝，建議您選擇 [AKS 引擎的機密計算背景工作角色節點](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 部署。 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>您支援和建議的 Kubernetes 版本為何？ 

我們支援並建議 Kubernetes 1.16 版和更新版本 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>預覽中的產品目前有哪些已知的限制或技術限制？ 

- 僅支援 Ubuntu 18.04 Gen 2 VM 節點 
- 沒有任何 Windows 節點支援或 Windows 容器支援
- 不支援以 EPC 記憶體為基礎的水準 Pod 自動調整。 支援 CPU 和一般記憶體型調整。
- 目前不支援機密應用程式的 AKS 上的 Dev Spaces

## <a name="next-steps"></a>後續步驟
如需有關機密容器的詳細資訊，請參閱 [機密容器頁面](confidential-containers.md) 。