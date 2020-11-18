---
title: 在 Azure 上使用 Intel SGX 引用協助程式 DaemonSet 的處理序外證明支援
description: 在 SGX 應用程式程序外產生引用的 DaemonSet。 本文說明如何對在容器內執行的機密工作負載提供處理序外證明功能。
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b79b3b40f3fbfe7d70550db3aaf7b365aa455e89
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564152"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set"></a>使用 SGX 引用協助程式精靈集的平台軟體管理

執行遠端證明的[記憶體保護區應用程式](confidential-computing-enclaves.md)需要產生的引用。 此引用會提供身分識別的密碼編譯證明和應用程式的狀態，以及記憶體保護區所執行的環境。 要產生引用，必須要有屬於 Intel 平台軟體元件 (PSW) 的受信任軟體元件。

## <a name="overview"></a>概觀
 
Intel 支援以兩種證明模式來執行引用產生作業：
- **同處理序**：在記憶體保護區應用程式程序內裝載受信任的軟體元件

- **處理序外**：在記憶體保護區應用程式外裝載受信任的軟體元件。
 
使用 Open Enclave SDK 建置的 SGX 應用程式，依預設會使用同處理序證明模式。 SGX 型應用程式允許處理序外模式，而需要額外裝載並公開必要的元件，例如應用程式外部的 Architectural Enclave Service Manager (AESM)。

**強烈建議** 使用這項功能，因為此功能可在 Intel 平台更新或 DCAP 驅動程式更新期間，增加記憶體保護區應用程式的運作時間。

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>為何要使用處理序外？它有何優點？

-   每個容器化應用程式的 PSW 引用產生元件都不需要任何更新：使用處理序外時，容器擁有者無須管理其容器內的更新。 容器擁有者會改為依賴提供者所提供的介面，在容器外部叫用集中式服務，而此服務將由提供者進行更新及管理。

-   無須擔心證明因 PSW 元件過時而失敗：引用產生牽涉到受信任的 SW 元件 - Quoting Enclave (QE) 和 Provisioning Certificate Enclave (PCE)，這是受信任運算基礎 (TCB) 的一部分。 這些 SW 元件必須是最新的，以維護證明需求。 由於提供者會管理這些元件的更新，因此，客戶將無須處理因其容器內有過時的受信任軟體元件而導致的證明失敗。

-   妥善運用 EPC 記憶體：在同處理序證明模式中，每個記憶體保護區應用程式都必須具現化 QE 和 PCE 的複本，以進行遠端證明。 使用處理序外時，容器不需要裝載這些記憶體保護區，因此不會耗用容器配額中的記憶體保護區記憶體。

-   防止核心強制執行：當 SGX 驅動程式串流至 Linux 核心時，將會強制記憶體保護區須具有較高的權限。 此權限可讓記憶體保護區叫用 PCE，這會中斷以同處理序模式執行的記憶體保護區應用程式。 根據預設，記憶體保護區不會取得此權限。 要將此權限授與記憶體保護區應用程式，必須變更應用程式安裝程序。 此處理在處理序外模型中可輕易完成，因為處理處理序外要求的服務提供者，會確保服務在安裝時會納入權限。

-   無須檢查與 PSW 和 DCAP 的回溯相容性。 在更新之前，提供者會先驗證 PSW 的引用產生元件更新是否有回溯相容性。 這將有助於提前處理相容性問題，並在部署機密工作負載的更新之前加以解決。

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>處理序外證明模式如何在機密工作負載案例中運作？

高階設計會遵循讓引用要求者和引用產生在相同的實體機器上個別執行的模型。 引用產生會以集中方式完成，並處理所有實體的引用要求。 介面必須正確定義，並且讓任何要求引用的實體都能找到。

![SGX 引用協助程式 AESM](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

上述抽象模型運用了既有的 AESM 服務，適用於機密工作負載案例。 AESM 會容器化並部署為 Kubernetes 叢集內的 daemonSet。 Kubernetes 可確保 AESM 服務容器的單一執行個體 (包裝在 Pod 中) 會部署到每個代理程式節點上。 新的 SGX 引用 daemonSet 會相依於 sgx-device-plugin daemonSet，因為 AESM 服務容器會從 sgx-device-plugin 要求 EPC 記憶體，用以啟動 QE 和 PCE 記憶體保護區。

每個容器都必須在建立期間設定環境變數 **SGX_AESM_ADDR=1**，以選擇使用處理序外引用產生。 容器也應包含套件 libsgx-quote-ex，因為此套件負責將要求導向至預設 Unix 網域通訊端

應用程式仍可像之前一樣使用同處理序證明，但無法同時在應用程式中使用同處理序和處理序外。 處理序外基礎結構預設為可用，且會耗用資源。

## <a name="sample-implementation"></a>範例實作

以下 Docker 檔案是 Open Enclave 型應用程式的範例。 在 Docker 檔案中設定 SGX_AESM_ADDR=1 環境變數，或在部署檔案上加以設定。 請透過下列範例了解 Docker 檔案和部署 yaml 的詳細資料。 

  > [!Note] 
  > Intel 提供的 **libsgx-quote-ex** 必須封裝在應用程式容器中，處理序外證明才能正常運作。
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
或者，您可以在部署 yaml 檔案中設定處理序外證明模式，如下所示

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>後續步驟
[在 AKS 上佈建機密節點 (DCsv2 系列)](./confidential-nodes-aks-get-started.md)

[機密容器快速入門範例](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 清單](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions