---
title: 保護 WCF 型服務通訊
description: 針對在 Azure Service Fabric 叢集中執行的可靠服務，了解如何保護其 WCF 通訊。
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ca5eafa4612503a13f80b7f238e4827979c0358b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614157"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>保護服務的 WCF 通訊
安全性是通訊最為重視的其中一個部分。 Reliable Services 應用程式架構會提供可用來改善安全性的一些預先建置通訊堆疊和工具。 本文探討如何改善使用服務遠端作業時的安全性。

我們將使用現有[範例](service-fabric-reliable-services-communication-wcf.md)說明如何設定 Reliable Services 的 WCF 通訊堆疊。 若要協助保護使用 WCF 通訊堆疊時的服務安全，請遵循下列步驟 ︰

1. 對於此服務，您需要協助保護所建立之 WCF 通訊接聽程式的安全 (`WcfCommunicationListener`)。 若要這樣做，請修改 `CreateServiceReplicaListeners` 方法。

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. 在用戶端，於先前[範例](service-fabric-reliable-services-communication-wcf.md)中建立的 `WcfCommunicationClient` 類別會保持不變。 但是，您需要覆寫 `WcfCommunicationClientFactory` 的 `CreateClientAsync` 方法：

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    使用 `SecureWcfCommunicationClientFactory` 來建立 WCF 通訊用戶端 (`WcfCommunicationClient`)。 使用用戶端來叫用服務方法。

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

接著請參閱[在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)。
