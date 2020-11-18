---
title: Azure Kubernetes Service (AKS) 上的機密運算節點 (公開預覽)
description: AKS 上的機密運算節點
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564169"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Azure Kubernetes Service 上的機密運算節點 (公開預覽)

[Azure 機密運算](overview.md)可讓您保護使用中的敏感資料。 基礎結構會使用受硬體支援的信任執行容器環境，來保護此資料不為其他應用程式、系統管理員和雲端提供者使用。

## <a name="overview"></a>概觀

Azure Kubernetes Service (AKS) 支援新增由 Intel SGX 提供技術支援的 [DCsv2 的機密運算節點](confidential-computing-enclaves.md)。 這些節點執行可允許使用者層級程式碼配置私人記憶體區域，藉以在硬體型的受信任執行環境 (TEE) 內執行敏感的工作負載。 這些私人記憶體區域稱為記憶體保護區。 記憶體保護區可用來保護程式碼和資料，讓以較高權限執行的程序不會對其造成影響。 SGX 執行模型移除了客體作業系統、主機作業系統和 Hypervisor 的中繼層。 *硬體型每一容器獨立執行* 模型可讓應用程式直接與 CPU 一起執行，同時讓特殊的記憶體區塊保持加密。 機密計算節點可協助 AKS 上容器應用程式的整體安全性狀態，並另外提供深度防禦容器策略。 

![SGX 節點概觀](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS 機密節點功能

- 透過 SGX 受信任執行環境 (TEE) 的硬體型和處理序層級的容器隔離 
- 異質節點集區叢集 (混合機密和非機密節點集區)
- 加密頁面快取 (EPC) 記憶體型 Pod 排程
- 已預先安裝 SGX DCAP 驅動程式
- 已預先安裝 Intel FSGS 修補程式
- 支援以 CPU 耗用量為基礎的水平 Pod 自動調整和叢集自動調整
- 透過 AKS daemonSet 的處理序外證明協助程式
- 透過 Ubuntu 18.04 Gen 2 VM 背景工作節點的 Linux 容器支援

## <a name="aks-provided-daemon-sets-addon"></a>AKS 提供的精靈集 (附加元件)

#### <a name="sgx-device-plugin"></a>SGX 裝置外掛程式 <a id="sgx-plugin"></a>

SGX 裝置外掛程式會實作 EPC 記憶體的 Kubernetes 裝置外掛程式介面。 實際上，此外掛程式會使 EPC 記憶體成為 Kubernetes 中的額外資源類型。 使用者可以指定此資源的限制，就像其他資源一樣。 除了排程函式以外，裝置外掛程式也有助於將 SGX 裝置驅動程式權限指派給機密工作負載容器。 如需 EPC 記憶體型部署 (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) 範例的範例實作，請參閱[這裡](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>SGX 引用協助程式服務 <a id="sgx-quote"></a>

執行遠端證明的記憶體保護區應用程式需要產生引用。 此引用會提供身分識別的密碼編譯證明和應用程式的狀態，以及記憶體保護區執行所在的環境。 要產生引用，須使用 Intel 提供的特定受信任軟體元件，而這些元件屬於 SGX 平台軟體元件 (PSW/DCAP) 的一部分。 此 PSW 會封裝為對個別節點執行的精靈集。 從記憶體保護區應用程式要求證明引用時，即可加以運用。 使用 AKS 提供的服務，有助於更有效地維護 PSW 與主機中的其他軟體元件之間的相容性。 [深入了解](confidential-nodes-out-of-proc-attestation.md)其使用方式和功能的詳細資料。

## <a name="programming--application-models"></a>程式設計和應用程式模型

### <a name="confidential-containers"></a>機密容器

[機密容器](confidential-containers.md)可在未修改或重新編譯任何原始程式碼的情況下，執行現有的程式和最 **常用的程式設計語言** 執行階段 (Python、Node、Java 等)，及其現有的程式庫相依性。 若要透過開放原始碼專案和 Azure 合作夥伴啟用機密性，此模型將是最快速的模型。 準備好要在安全記憶體保護區中執行的容器映像，稱為機密容器。

### <a name="enclave-aware-containers"></a>記憶體保護區感知容器

AKS 支援透過程式設計成在機密節點上執行，並使用透過 SDK 和架構提供的 **特殊指令集** 的應用程式。 此應用程式模型以最低的信賴運算基礎 (TCB) 為應用程式提供最大程度的控制。 [深入了解](enclave-aware-containers.md)記憶體保護區感知容器。

## <a name="next-steps"></a>後續步驟

[部署具有機密運算節點的 AKS 叢集](./confidential-nodes-aks-get-started.md)

[機密容器快速入門範例](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 清單](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions