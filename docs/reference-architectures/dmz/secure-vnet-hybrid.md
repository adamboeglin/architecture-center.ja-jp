---
title: セキュリティ保護されたハイブリッド ネットワーク アーキテクチャの実装
titleSuffix: Azure Reference Architectures
description: セキュリティ保護されたハイブリッド ネットワーク アーキテクチャを実装します。
author: telmosampaio
ms.date: 10/22/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, networking
ms.openlocfilehash: 82327cca08e614bfe5226c9ca1a414388878a7c2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243513"
---
# <a name="implement-a-dmz-between-azure-and-your-on-premises-datacenter"></a><span data-ttu-id="4eb59-103">Azure とオンプレミス データセンター間に DMZ を実装する</span><span class="sxs-lookup"><span data-stu-id="4eb59-103">Implement a DMZ between Azure and your on-premises datacenter</span></span>

<span data-ttu-id="4eb59-104">次のリファレンス アーキテクチャは、オンプレミスのネットワークを Azure に拡張する、セキュリティ保護されたハイブリッド ネットワークを示しています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure.</span></span> <span data-ttu-id="4eb59-105">このアーキテクチャには、*境界ネットワーク*とも呼ばれる DMZ が、オンプレミス ネットワークと Azure の仮想ネットワーク (VNet) の間に実装されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-105">The architecture implements a DMZ, also called a *perimeter network*, between the on-premises network and an Azure virtual network (VNet).</span></span> <span data-ttu-id="4eb59-106">DMZ には、ファイアウォールやパケット検査などのセキュリティ機能を実装する、ネットワーク仮想アプライアンス (NVA) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-106">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="4eb59-107">VNet からの発信トラフィックはすべてオンプレミス ネットワーク経由でインターネットに強制的にトンネリングされるため、トラフィックの監査が可能です。</span><span class="sxs-lookup"><span data-stu-id="4eb59-107">All outgoing traffic from the VNet is force-tunneled to the Internet through the on-premises network, so that it can be audited.</span></span> <span data-ttu-id="4eb59-108">[**このソリューションをデプロイします**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="4eb59-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

> [!NOTE]
> <span data-ttu-id="4eb59-109">このシナリオは、クラウドベースのネットワーク セキュリティ サービスである [Azure Firewall](/azure/firewall/) を使用して実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-109">This scenario can also be accomplished using [Azure Firewall](/azure/firewall/), a cloud-based network security service.</span></span>

![セキュリティ保護されたハイブリッド ネットワーク アーキテクチャ](./images/dmz-private.png)

<span data-ttu-id="4eb59-111">"*このアーキテクチャの [Visio ファイル][visio-download]をダウンロードします。*"</span><span class="sxs-lookup"><span data-stu-id="4eb59-111">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="4eb59-112">このアーキテクチャでは、[VPN ゲートウェイ][ra-vpn]または [ExpressRoute][ra-expressroute] 接続のいずれかを使用して、オンプレミスのデータセンターに接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-112">This architecture requires a connection to your on-premises datacenter, using either a [VPN gateway][ra-vpn] or an [ExpressRoute][ra-expressroute] connection.</span></span> <span data-ttu-id="4eb59-113">このアーキテクチャの一般的な用途は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4eb59-113">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="4eb59-114">ワークロードの一部がオンプレミスで、一部が Azure で実行されるハイブリッド アプリケーション。</span><span class="sxs-lookup"><span data-stu-id="4eb59-114">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
- <span data-ttu-id="4eb59-115">オンプレミスのデータセンターから Azure VNet に入ってくるトラフィックをきめ細かく制御する必要があるインフラストラクチャ。</span><span class="sxs-lookup"><span data-stu-id="4eb59-115">Infrastructure that requires granular control over traffic entering an Azure VNet from an on-premises datacenter.</span></span>
- <span data-ttu-id="4eb59-116">送信トラフィックを監査する必要があるアプリケーション。</span><span class="sxs-lookup"><span data-stu-id="4eb59-116">Applications that must audit outgoing traffic.</span></span> <span data-ttu-id="4eb59-117">これはしばしば、多くの商用システムで規制上の要件となっていて、プライベートな情報の一般への漏えいを防ぐ助けになります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-117">This is often a regulatory requirement of many commercial systems and can help to prevent public disclosure of private information.</span></span>

## <a name="architecture"></a><span data-ttu-id="4eb59-118">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="4eb59-118">Architecture</span></span>

<span data-ttu-id="4eb59-119">アーキテクチャは、次のコンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-119">The architecture consists of the following components.</span></span>

- <span data-ttu-id="4eb59-120">**オンプレミス ネットワーク**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-120">**On-premises network**.</span></span> <span data-ttu-id="4eb59-121">組織内に実装されているプライベートなローカル エリア ネットワーク。</span><span class="sxs-lookup"><span data-stu-id="4eb59-121">A private local-area network implemented in an organization.</span></span>
- <span data-ttu-id="4eb59-122">**Azure の仮想ネットワーク (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-122">**Azure virtual network (VNet)**.</span></span> <span data-ttu-id="4eb59-123">VNet は、Azure で実行されているアプリケーションとその他のリソースをホストします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-123">The VNet hosts the application and other resources running in Azure.</span></span>
- <span data-ttu-id="4eb59-124">**ゲートウェイ**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-124">**Gateway**.</span></span> <span data-ttu-id="4eb59-125">ゲートウェイは、オンプレミスのネットワーク内のルーターと VNet 間に接続を提供します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-125">The gateway provides connectivity between the routers in the on-premises network and the VNet.</span></span>
- <span data-ttu-id="4eb59-126">**ネットワーク仮想アプライアンス (NVA)**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-126">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="4eb59-127">NVA は、ファイアウォールとしてのアクセスの許可や拒否、ワイド エリア ネットワーク (WAN) 操作の最適化 (ネットワーク圧縮を含む)、カスタム ルーティング、その他のネットワーク機能などのタスクを実行する VM を表す総称です。</span><span class="sxs-lookup"><span data-stu-id="4eb59-127">NVA is a generic term that describes a VM performing tasks such as allowing or denying access as a firewall, optimizing wide area network (WAN) operations (including network compression), custom routing, or other network functionality.</span></span>
- <span data-ttu-id="4eb59-128">**Web 層、ビジネス層、およびデータ層のサブネット**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-128">**Web tier, business tier, and data tier subnets**.</span></span> <span data-ttu-id="4eb59-129">クラウドで実行される、例のような 3 層アプリケーションを実装した VM やサービスをホストしているサブネット。</span><span class="sxs-lookup"><span data-stu-id="4eb59-129">Subnets hosting the VMs and services that implement an example 3-tier application running in the cloud.</span></span> <span data-ttu-id="4eb59-130">詳細については、「[Azure で N 層アーキテクチャの Windows VM を実行する][ra-n-tier]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-130">See [Running Windows VMs for an N-tier architecture on Azure][ra-n-tier] for more information.</span></span>
- <span data-ttu-id="4eb59-131">**ユーザー定義ルート(UDR)**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-131">**User defined routes (UDR)**.</span></span> <span data-ttu-id="4eb59-132">[ユーザー定義ルート][udr-overview]は、Azure VNet 内の IP トラフィックのフローを定義します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-132">[User defined routes][udr-overview] define the flow of IP traffic within Azure VNets.</span></span>

    > [!NOTE]
    > <span data-ttu-id="4eb59-133">VPN 接続の要件に応じて、UDR を使用してオンプレミス ネットワーク経由でトラフィックを戻すよう指示する転送ルールを実装する代わりに、ボーダー ゲートウェイ プロトコル (BGP) のルートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-133">Depending on the requirements of your VPN connection, you can configure Border Gateway Protocol (BGP) routes instead of using UDRs to implement the forwarding rules that direct traffic back through the on-premises network.</span></span>
    >

- <span data-ttu-id="4eb59-134">**管理サブネット**。</span><span class="sxs-lookup"><span data-stu-id="4eb59-134">**Management subnet**.</span></span> <span data-ttu-id="4eb59-135">このサブネットには、VNet で実行されているコンポーネントの管理および監視の機能を実装している VM が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-135">This subnet contains VMs that implement management and monitoring capabilities for the components running in the VNet.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4eb59-136">Recommendations</span><span class="sxs-lookup"><span data-stu-id="4eb59-136">Recommendations</span></span>

<span data-ttu-id="4eb59-137">ほとんどのシナリオには、次の推奨事項が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-137">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="4eb59-138">これらの推奨事項には、オーバーライドする特定の要件がない限り、従ってください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-138">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="access-control-recommendations"></a><span data-ttu-id="4eb59-139">アクセスの制御に関する推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-139">Access control recommendations</span></span>

<span data-ttu-id="4eb59-140">アプリケーション内のリソースは、[ロール ベースのアクセス制御][rbac] (RBAC) を使用して管理します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-140">Use [Role-Based Access Control][rbac] (RBAC) to manage the resources in your application.</span></span> <span data-ttu-id="4eb59-141">次の[カスタム ロール][rbac-custom-roles]の作成を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-141">Consider creating the following [custom roles][rbac-custom-roles]:</span></span>

- <span data-ttu-id="4eb59-142">アプリケーションのインフラストラクチャの管理、アプリケーション コンポーネントのデプロイ、および VM の監視と再起動を行うためのアクセス許可を持つ DevOps ロール。</span><span class="sxs-lookup"><span data-stu-id="4eb59-142">A DevOps role with permissions to administer the infrastructure for the application, deploy the application components, and monitor and restart VMs.</span></span>

- <span data-ttu-id="4eb59-143">ネットワーク リソースの管理と監視を行うための一元的 IT 管理者ロール。</span><span class="sxs-lookup"><span data-stu-id="4eb59-143">A centralized IT administrator role to manage and monitor network resources.</span></span>

- <span data-ttu-id="4eb59-144">NVA などのセキュリティで保護されたネットワーク リソースを管理するためのセキュリティ IT 管理者ロール。</span><span class="sxs-lookup"><span data-stu-id="4eb59-144">A security IT administrator role to manage secure network resources such as the NVAs.</span></span>

<span data-ttu-id="4eb59-145">DevOps ロールと IT 管理者ロールは NVA リソースへのアクセス権を持っているべきではありません。</span><span class="sxs-lookup"><span data-stu-id="4eb59-145">The DevOps and IT administrator roles should not have access to the NVA resources.</span></span> <span data-ttu-id="4eb59-146">このアクセス権はセキュリティ IT 管理者ロールに限定してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-146">This should be restricted to the security IT administrator role.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="4eb59-147">リソース グループの推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-147">Resource group recommendations</span></span>

<span data-ttu-id="4eb59-148">VM、VNet、ロード バランサーなどの Azure リソースは、リソース グループにグループ化することで容易に管理できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-148">Azure resources such as VMs, VNets, and load balancers can be easily managed by grouping them together into resource groups.</span></span> <span data-ttu-id="4eb59-149">RBAC ロールを各リソース グループに割り当てて、アクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-149">Assign RBAC roles to each resource group to restrict access.</span></span>

<span data-ttu-id="4eb59-150">次のリソース グループを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-150">We recommend creating the following resource groups:</span></span>

- <span data-ttu-id="4eb59-151">オンプレミス ネットワークに接続するための VNet (VM を除く)、NSG、およびゲートウェイのリソースを含むリソース グループ。</span><span class="sxs-lookup"><span data-stu-id="4eb59-151">A resource group containing the VNet (excluding the VMs), NSGs, and the gateway resources for connecting to the on-premises network.</span></span> <span data-ttu-id="4eb59-152">このリソース グループに一元的 IT 管理者ロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-152">Assign the centralized IT administrator role to this resource group.</span></span>
- <span data-ttu-id="4eb59-153">NVA (ロード バランサーを含む) 用の VM、jumpbox とその他の管理 VM、およびすべてのトラフィックが NVA を経由するように強制するゲートウェイ サブネット用の UDR を含むリソース グループ。</span><span class="sxs-lookup"><span data-stu-id="4eb59-153">A resource group containing the VMs for the NVAs (including the load balancer), the jumpbox and other management VMs, and the UDR for the gateway subnet that forces all traffic through the NVAs.</span></span> <span data-ttu-id="4eb59-154">このリソース グループにセキュリティ IT 管理者ロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-154">Assign the security IT administrator role to this resource group.</span></span>
- <span data-ttu-id="4eb59-155">ロード バランサーと VM を含む、アプリケーション層ごとの個別のリソース グループ。</span><span class="sxs-lookup"><span data-stu-id="4eb59-155">Separate resource groups for each application tier that contain the load balancer and VMs.</span></span> <span data-ttu-id="4eb59-156">このリソース グループには各層のサブネットを含めてはいけないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-156">Note that this resource group shouldn't include the subnets for each tier.</span></span> <span data-ttu-id="4eb59-157">このリソース グループに DevOps ロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-157">Assign the DevOps role to this resource group.</span></span>

### <a name="virtual-network-gateway-recommendations"></a><span data-ttu-id="4eb59-158">仮想ネットワーク ゲートウェイに関する推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-158">Virtual network gateway recommendations</span></span>

<span data-ttu-id="4eb59-159">オンプレミスのトラフィックは、仮想ネットワーク ゲートウェイ経由で VNet に渡します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-159">On-premises traffic passes to the VNet through a virtual network gateway.</span></span> <span data-ttu-id="4eb59-160">[Azure VPN ゲートウェイ][guidance-vpn-gateway]または [Azure ExpressRoute ゲートウェイ][guidance-expressroute]をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-160">We recommend an [Azure VPN gateway][guidance-vpn-gateway] or an [Azure ExpressRoute gateway][guidance-expressroute].</span></span>

### <a name="nva-recommendations"></a><span data-ttu-id="4eb59-161">NVA の推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-161">NVA recommendations</span></span>

<span data-ttu-id="4eb59-162">NVA は、ネットワーク トラフィックの管理と監視のためのさまざまなサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-162">NVAs provide different services for managing and monitoring network traffic.</span></span> <span data-ttu-id="4eb59-163">[Azure Marketplace][azure-marketplace-nva] では、使用できる複数のサード パーティー ベンダー製 NVA が提供されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-163">The [Azure Marketplace][azure-marketplace-nva] offers several third-party vendor NVAs that you can use.</span></span> <span data-ttu-id="4eb59-164">これらのサード パーティー製 NVA に要件を満たしているものがない場合は、VM を使用してカスタム NVA を作成できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-164">If none of these third-party NVAs meet your requirements, you can create a custom NVA using VMs.</span></span>

<span data-ttu-id="4eb59-165">たとえば、この参照アーキテクチャのソリューションのデプロイでは、VM 上に次の機能を備えた NVA を実装します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-165">For example, the solution deployment for this reference architecture implements an NVA with the following functionality on a VM:</span></span>

- <span data-ttu-id="4eb59-166">トラフィックは、NVA ネットワーク インターフェイス (NIC) 上で [IP 転送][ip-forwarding]を使用してルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-166">Traffic is routed using [IP forwarding][ip-forwarding] on the NVA network interfaces (NICs).</span></span>
- <span data-ttu-id="4eb59-167">トラフィックは、通過するのが適切な場合にのみ、NVA を通過することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-167">Traffic is permitted to pass through the NVA only if it is appropriate to do so.</span></span> <span data-ttu-id="4eb59-168">参照アーキテクチャ内の各 NVA VM は、単純な Linux ルーターです。</span><span class="sxs-lookup"><span data-stu-id="4eb59-168">Each NVA VM in the reference architecture is a simple Linux router.</span></span> <span data-ttu-id="4eb59-169">受信トラフィックはネットワーク インターフェイス *eth0* に到着し、送信トラフィックは、ネットワーク インターフェイス *eth1* 経由で送信されたカスタム スクリプトによって定義されるルールと照合されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-169">Inbound traffic arrives on network interface *eth0*, and outbound traffic matches rules defined by custom scripts dispatched through network interface *eth1*.</span></span>
- <span data-ttu-id="4eb59-170">NVA の構成は、管理サブネットからのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="4eb59-170">The NVAs can only be configured from the management subnet.</span></span>
- <span data-ttu-id="4eb59-171">管理サブネットにルーティングされるトラフィックは NVA を通過しません。</span><span class="sxs-lookup"><span data-stu-id="4eb59-171">Traffic routed to the management subnet does not pass through the NVAs.</span></span> <span data-ttu-id="4eb59-172">そのようにしないと、NVA が停止した場合、それらを修正するための管理サブネットへのルートがなくなります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-172">Otherwise, if the NVAs fail, there would be no route to the management subnet to fix them.</span></span>
- <span data-ttu-id="4eb59-173">NVA 用の VM は、ロード バランサーの背後の[可用性セット][availability-set]内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-173">The VMs for the NVA are placed in an [availability set][availability-set] behind a load balancer.</span></span> <span data-ttu-id="4eb59-174">ゲートウェイ サブネット内の UDR は、NVA 要求をロード バランサーに転送します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-174">The UDR in the gateway subnet directs NVA requests to the load balancer.</span></span>

<span data-ttu-id="4eb59-175">第 7 層の NVA を含めて、アプリケーションの接続を NVA レベルで終了し、バックエンド層とのアフィニティを維持します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-175">Include a layer-7 NVA to terminate application connections at the NVA level and maintain affinity with the backend tiers.</span></span> <span data-ttu-id="4eb59-176">これによって対称接続性が保証され、バックエンド層からの応答トラフィックが NVA を介して返されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-176">This guarantees symmetric connectivity, in which response traffic from the backend tiers returns through the NVA.</span></span>

<span data-ttu-id="4eb59-177">その他には、複数の NVA を連続して接続し、各 NVA で特殊化したセキュリティ タスクを実行するオプションを検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-177">Another option to consider is connecting multiple NVAs in series, with each NVA performing a specialized security task.</span></span> <span data-ttu-id="4eb59-178">これにより、NVA ごとにそれぞれのセキュリティ機能を管理できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-178">This allows each security function to be managed on a per-NVA basis.</span></span> <span data-ttu-id="4eb59-179">たとえば、ファイアウォールを実装する NVA を、ID サービスを実行する NVA と連続的に配置できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-179">For example, an NVA implementing a firewall could be placed in series with an NVA running identity services.</span></span> <span data-ttu-id="4eb59-180">管理が容易なことのトレードオフとして、待機時間を増やす可能性がある余分のネットワーク ホップが追加になるため、これがアプリケーションのパフォーマンスに影響しないことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-180">The tradeoff for ease of management is the addition of extra network hops that may increase latency, so ensure that this doesn't affect your application's performance.</span></span>

### <a name="nsg-recommendations"></a><span data-ttu-id="4eb59-181">NSG の推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-181">NSG recommendations</span></span>

<span data-ttu-id="4eb59-182">VPN ゲートウェイでは、オンプレミス ネットワークへの接続のパブリック IP アドレスを公開します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-182">The VPN gateway exposes a public IP address for the connection to the on-premises network.</span></span> <span data-ttu-id="4eb59-183">受信 NVA サブネットのために、オンプレミス ネットワークが発信元ではないすべてのトラフィックをブロックするルールを使用して、ネットワーク セキュリティ グループ (NSG) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-183">We recommend creating a network security group (NSG) for the inbound NVA subnet, with rules to block all traffic not originating from the on-premises network.</span></span>

<span data-ttu-id="4eb59-184">正しく構成されていない NVA や無効になっている NVA をバイパスする受信トラフィックからの 2 つ目の保護レベルを提供するため、各サブネット用に NSG を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-184">We also recommend NSGs for each subnet to provide a second level of protection against inbound traffic bypassing an incorrectly configured or disabled NVA.</span></span> <span data-ttu-id="4eb59-185">たとえば、参照アーキテクチャの Web 層サブネットでは、オンプレミス ネットワーク (192.168.0.0/16) または VNet から受信したもの以外のすべての要求を無視するルールと、ポート 80 で作成されたのではないすべての要求を無視する別のルールを使用して、NSG を実装しています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-185">For example, the web tier subnet in the reference architecture implements an NSG with a rule to ignore all requests other than those received from the on-premises network (192.168.0.0/16) or the VNet, and another rule that ignores all requests not made on port 80.</span></span>

### <a name="internet-access-recommendations"></a><span data-ttu-id="4eb59-186">インターネットへのアクセスに関する推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-186">Internet access recommendations</span></span>

<span data-ttu-id="4eb59-187">サイト間 VPN トンネルを使用して、オンプレミス ネットワークを経由するすべての送信インターネット トラフィックを[強制的にトンネリング][azure-forced-tunneling]し、ネットワーク アドレス変換 (NAT) を使用してインターネットにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-187">[Force-tunnel][azure-forced-tunneling] all outbound Internet traffic through your on-premises network using the site-to-site VPN tunnel, and route to the Internet using network address translation (NAT).</span></span> <span data-ttu-id="4eb59-188">これにより、データ層に格納されている機密情報の偶発的漏えいが防がれて、すべての発信トラフィックの検査と監査が可能になります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-188">This prevents accidental leakage of any confidential information stored in your data tier and allows inspection and auditing of all outgoing traffic.</span></span>

> [!NOTE]
> <span data-ttu-id="4eb59-189">アプリケーション層からのインターネット トラフィックを完全にブロックしないでください。そのようにすると、VM 診断ログ記録や VM 拡張機能のダウンロードを始めとする機能など、パブリック IP アドレスに依存している Azure の PaaS サービスをアプリケーション層で使用できなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="4eb59-189">Don't completely block Internet traffic from the application tiers, as this will prevent these tiers from using Azure PaaS services that rely on public IP addresses, such as VM diagnostics logging, downloading of VM extensions, and other functionality.</span></span> <span data-ttu-id="4eb59-190">Azure 診断でも、コンポーネントが Azure Storage アカウントに対する読み取りと書き込みが可能な必要があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-190">Azure diagnostics also requires that components can read and write to an Azure Storage account.</span></span>

<span data-ttu-id="4eb59-191">送信インターネット トラフィックが正しく強制的にトンネリングされることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-191">Verify that outbound internet traffic is force-tunneled correctly.</span></span> <span data-ttu-id="4eb59-192">オンプレミス サーバー上の[ルーティングとリモート アクセス サービス][routing-and-remote-access-service]と共に VPN 接続を使用している場合は、[WireShark][wireshark] や [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) などのツールを使用してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-192">If you're using a VPN connection with the [routing and remote access service][routing-and-remote-access-service] on an on-premises server, use a tool such as [WireShark][wireshark] or [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226).</span></span>

### <a name="management-subnet-recommendations"></a><span data-ttu-id="4eb59-193">管理サブネットに関する推奨事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-193">Management subnet recommendations</span></span>

<span data-ttu-id="4eb59-194">管理サブネットには、管理と監視の機能を実行する jumpbox が含まれています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-194">The management subnet contains a jumpbox that performs management and monitoring functionality.</span></span> <span data-ttu-id="4eb59-195">Jumpbox に対するセキュリティで保護された管理タスクの実行は、すべて制限してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-195">Restrict execution of all secure management tasks to the jumpbox.</span></span>

<span data-ttu-id="4eb59-196">jumpbox 用のパブリック IP アドレスは作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-196">Do not create a public IP address for the jumpbox.</span></span> <span data-ttu-id="4eb59-197">代わりに、受信ゲートウェイ経由で jumpbox にアクセスするルートを 1 つ作成します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-197">Instead, create one route to access the jumpbox through the incoming gateway.</span></span> <span data-ttu-id="4eb59-198">管理サブネットが、許可されているルートからの要求にのみ応答するように NSG ルールを作成してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-198">Create NSG rules so the management subnet only responds to requests from the allowed route.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="4eb59-199">スケーラビリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-199">Scalability considerations</span></span>

<span data-ttu-id="4eb59-200">参照アーキテクチャでは、ロード バランサーを使用してオンプレミスのネットワーク トラフィックを NVA デバイスのプールにルーティングし、それらのデバイスがトラフィックをルーティングしています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-200">The reference architecture uses a load balancer to direct on-premises network traffic to a pool of NVA devices, which route the traffic.</span></span> <span data-ttu-id="4eb59-201">NVA は[可用性セット][availability-set]内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-201">The NVAs are placed in an [availability set][availability-set].</span></span> <span data-ttu-id="4eb59-202">この設計により、一定期間にわたる NVA のスループットを監視し、負荷の増加に応じて NVA デバイスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-202">This design allows you to monitor the throughput of the NVAs over time and add NVA devices in response to increases in load.</span></span>

<span data-ttu-id="4eb59-203">Standard SKU の VPN ゲートウェイでは、最大 100 Mbps の持続スループットをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-203">The standard SKU VPN gateway supports sustained throughput of up to 100 Mbps.</span></span> <span data-ttu-id="4eb59-204">High Performance SKU では、最大で 200 Mbps が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-204">The High Performance SKU provides up to 200 Mbps.</span></span> <span data-ttu-id="4eb59-205">帯域幅が広い場合は、ExpressRoute ゲートウェイへのアップグレードをご検討ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-205">For higher bandwidths, consider upgrading to an ExpressRoute gateway.</span></span> <span data-ttu-id="4eb59-206">ExpressRoute では、VPN 接続よりも待機時間が短い最大 10 Gbps の帯域幅が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-206">ExpressRoute provides up to 10 Gbps bandwidth with lower latency than a VPN connection.</span></span>

<span data-ttu-id="4eb59-207">Azure ゲートウェイのスケーラビリティの詳細については、「[Azure とオンプレミスの VPN を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-vpn-gateway-scalability]」と「[Azure ExpressRoute を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-expressroute-scalability]」の、スケーラビリティの考慮事項についてのセクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-207">For more information about the scalability of Azure gateways, see the scalability consideration section in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-scalability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-scalability].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="4eb59-208">可用性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-208">Availability considerations</span></span>

<span data-ttu-id="4eb59-209">前述のように、参照アーキテクチャではロード バランサーの背後にある NVA デバイスのプールを使用しています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-209">As mentioned, the reference architecture uses a pool of NVA devices behind a load balancer.</span></span> <span data-ttu-id="4eb59-210">ロード バランサーは、正常性プローブを使用して各 NVA を監視し、応答していない NVA をプールから削除します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-210">The load balancer uses a health probe to monitor each NVA and will remove any unresponsive NVAs from the pool.</span></span>

<span data-ttu-id="4eb59-211">Azure ExpressRoute を使用して VNet およびオンプレミス ネットワーク間の接続を提供している場合は、ExpressRoute 接続が使用できなくなったときに[フェールオーバーが行われるように VPN ゲートウェイを構成][ra-vpn-failover]します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-211">If you're using Azure ExpressRoute to provide connectivity between the VNet and on-premises network, [configure a VPN gateway to provide failover][ra-vpn-failover] if the ExpressRoute connection becomes unavailable.</span></span>

<span data-ttu-id="4eb59-212">VPN 接続と ExpressRoute 接続の可用性を維持することの具体的説明については、「[Azure とオンプレミスの VPN を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-vpn-gateway-availability]」と「[Azure ExpressRoute を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-expressroute-availability]」の、可用性に関する考慮事項をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-212">For specific information on maintaining availability for VPN and ExpressRoute connections, see the availability considerations in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-availability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-availability].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="4eb59-213">管理容易性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-213">Manageability considerations</span></span>

<span data-ttu-id="4eb59-214">管理サブネットでは、jumpbox によるすべてのアプリケーションとリソースの監視を実行してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-214">All application and resource monitoring should be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="4eb59-215">アプリケーションの要件に応じて、管理サブネット内で追加の監視リソースが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-215">Depending on your application requirements, you may need additional monitoring resources in the management subnet.</span></span> <span data-ttu-id="4eb59-216">その場合、jumpbox を介してこれらのリソースにアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-216">If so, these resources should be accessed through the jumpbox.</span></span>

<span data-ttu-id="4eb59-217">オンプレミスのネットワークから Azure へのゲートウェイ接続がダウンした場合でも、パブリック IP アドレスをデプロイしてジャンプボックスに追加し、インターネットからリモート接続することで、ジャンプボックスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-217">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and remoting in from the internet.</span></span>

<span data-ttu-id="4eb59-218">参照アーキテクチャの各層のサブネットは、NSG ルールによって保護されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-218">Each tier's subnet in the reference architecture is protected by NSG rules.</span></span> <span data-ttu-id="4eb59-219">Windows VM でリモート デスクトップ プロトコル (RDP) アクセスのためにポート 3389 を開くルールや、Linux VM で Secure Shell (SSH) アクセスのためにポート 22 を開くルールを作成することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-219">You may need to create a rule to open port 3389 for remote desktop protocol (RDP) access on Windows VMs or port 22 for secure shell (SSH) access on Linux VMs.</span></span> <span data-ttu-id="4eb59-220">その他の管理ツールや監視ツールのために、追加のポートを開くルールが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-220">Other management and monitoring tools may require rules to open additional ports.</span></span>

<span data-ttu-id="4eb59-221">ExpressRoute を使用してオンプレミスのデータセンターと Azure の間の接続を提供する場合は、[Azure Connectivity Toolkit (AzureCT)][azurect] を使用して接続の問題の監視とトラブルシューティングを行ってください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-221">If you're using ExpressRoute to provide the connectivity between your on-premises datacenter and Azure, use the [Azure Connectivity Toolkit (AzureCT)][azurect] to monitor and troubleshoot connection issues.</span></span>

<span data-ttu-id="4eb59-222">特に VPN 接続と ExpressRoute 接続の監視と管理に的を絞ったその他の説明については、「[Azure とオンプレミスの VPN を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-vpn-gateway-manageability]」および「[Azure ExpressRoute を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-expressroute-manageability]」という記事に記載されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-222">You can find additional information specifically aimed at monitoring and managing VPN and ExpressRoute connections in the articles [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-manageability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-manageability].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4eb59-223">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="4eb59-223">Security considerations</span></span>

<span data-ttu-id="4eb59-224">この参照アーキテクチャには、複数レベルのセキュリティが実装されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-224">This reference architecture implements multiple levels of security.</span></span>

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a><span data-ttu-id="4eb59-225">NVA を経由するすべてのオンプレミス ユーザー要求のルーティング</span><span class="sxs-lookup"><span data-stu-id="4eb59-225">Routing all on-premises user requests through the NVA</span></span>

<span data-ttu-id="4eb59-226">ゲートウェイ サブネット内の UDR は、オンプレミスから受信した要求を除くすべてのユーザー要求をブロックします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-226">The UDR in the gateway subnet blocks all user requests other than those received from on-premises.</span></span> <span data-ttu-id="4eb59-227">UDR は許可された要求をプライベート DMZ 内の NVA に渡し、これらの要求は、NVA ルールによって許可されていればアプリケーションに渡されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-227">The UDR passes allowed requests to the NVAs in the private DMZ subnet, and these requests are passed on to the application if they are allowed by the NVA rules.</span></span> <span data-ttu-id="4eb59-228">UDR にその他のルートを追加できますが、それらのルートで誤って、NVA をバイパスしたり、管理サブネットに向けた管理トラフィックをブロックしたりしないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-228">You can add other routes to the UDR, but make sure they don't inadvertently bypass the NVAs or block administrative traffic intended for the management subnet.</span></span>

<span data-ttu-id="4eb59-229">NVA の前のロード バランサーも、負荷分散規則で開いていないポート上のトラフィックを無視することで、セキュリティ デバイスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-229">The load balancer in front of the NVAs also acts as a security device by ignoring traffic on ports that are not open in the load balancing rules.</span></span> <span data-ttu-id="4eb59-230">参照アーキテクチャ内のロード バランサーは、ポート 80 での HTTP 要求とポート 443 での HTTPS 要求のみをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-230">The load balancers in the reference architecture only listen for HTTP requests on port 80 and HTTPS requests on port 443.</span></span> <span data-ttu-id="4eb59-231">ロード バランサーに追加した規則が他にあれば文書化し、トラフィックを監視してセキュリティ上の問題がないことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-231">Document any additional rules that you add to the load balancers, and monitor traffic to ensure there are no security issues.</span></span>

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a><span data-ttu-id="4eb59-232">NSG を使用したアプリケーション層間でのトラフィックのブロック/通過</span><span class="sxs-lookup"><span data-stu-id="4eb59-232">Using NSGs to block/pass traffic between application tiers</span></span>

<span data-ttu-id="4eb59-233">階層間のトラフィックは NSG を使用して制限します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-233">Traffic between tiers is restricted by using NSGs.</span></span> <span data-ttu-id="4eb59-234">ビジネス層では Web 層で発生したのではないすべてのトラフィックをブロックし、データ層ではビジネス層で発生したのではないすべてのトラフィックをブロックします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-234">The business tier blocks all traffic that doesn't originate in the web tier, and the data tier blocks all traffic that doesn't originate in the business tier.</span></span> <span data-ttu-id="4eb59-235">NSG ルールを拡張し、これらの層に対してより広範囲のアクセスを許可する必要がある場合は、その必要性をセキュリティ リスクと比較検討してください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-235">If you have a requirement to expand the NSG rules to allow broader access to these tiers, weigh these requirements against the security risks.</span></span> <span data-ttu-id="4eb59-236">新しい受信経路が増えるたびに、偶発的または意図的に、データ漏えいやアプリケーションの破損が起きる可能性が高まります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-236">Each new inbound pathway represents an opportunity for accidental or purposeful data leakage or application damage.</span></span>

### <a name="devops-access"></a><span data-ttu-id="4eb59-237">DevOps アクセス</span><span class="sxs-lookup"><span data-stu-id="4eb59-237">DevOps access</span></span>

<span data-ttu-id="4eb59-238">[RBAC][rbac] を使用して、DevOps が各階層で実行できる操作を制限します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-238">Use [RBAC][rbac] to restrict the operations that DevOps can perform on each tier.</span></span> <span data-ttu-id="4eb59-239">アクセス許可を付与する場合は、[最小限の特権の原則][security-principle-of-least-privilege]に従ってください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-239">When granting permissions, use the [principle of least privilege][security-principle-of-least-privilege].</span></span> <span data-ttu-id="4eb59-240">構成の変更がすべて計画されたものであることを確認するため、すべての管理操作をログに記録し、定期的な監査を実行します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-240">Log all administrative operations and perform regular audits to ensure any configuration changes were planned.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="4eb59-241">ソリューションのデプロイ方法</span><span class="sxs-lookup"><span data-stu-id="4eb59-241">Deploy the solution</span></span>

<span data-ttu-id="4eb59-242">これらの推奨事項を実装する参照アーキテクチャのデプロイは、[GitHub][github-folder] で入手できます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-242">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span>

### <a name="prerequisites"></a><span data-ttu-id="4eb59-243">前提条件</span><span class="sxs-lookup"><span data-stu-id="4eb59-243">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-resources"></a><span data-ttu-id="4eb59-244">リソースのデプロイ</span><span class="sxs-lookup"><span data-stu-id="4eb59-244">Deploy resources</span></span>

1. <span data-ttu-id="4eb59-245">参照アーキテクチャ GitHub リポジトリの `/dmz/secure-vnet-hybrid` フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-245">Navigate to the `/dmz/secure-vnet-hybrid` folder of the reference architectures GitHub repository.</span></span>

2. <span data-ttu-id="4eb59-246">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-246">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.json --deploy
    ```

3. <span data-ttu-id="4eb59-247">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-247">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p secure-vnet-hybrid.json --deploy
    ```

### <a name="connect-the-on-premises-and-azure-gateways"></a><span data-ttu-id="4eb59-248">オンプレミスと Azure ゲートウェイに接続する</span><span class="sxs-lookup"><span data-stu-id="4eb59-248">Connect the on-premises and Azure gateways</span></span>

<span data-ttu-id="4eb59-249">この手順では、2 つのローカル ネットワーク ゲートウェイを接続します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-249">In this step, you will connect the two local network gateways.</span></span>

1. <span data-ttu-id="4eb59-250">Azure portal で、作成したリソース グループに移動します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-250">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="4eb59-251">`ra-vpn-vgw-pip` という名前のリソースを探し、**[概要]** ブレードに表示されている IP アドレスをコピーします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-251">Find the resource named `ra-vpn-vgw-pip` and copy the IP address shown in the **Overview** blade.</span></span>

3. <span data-ttu-id="4eb59-252">`onprem-vpn-lgw` という名前のリソースを探します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-252">Find the resource named `onprem-vpn-lgw`.</span></span>

4. <span data-ttu-id="4eb59-253">**[構成]** ブレードをクリックします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-253">Click the **Configuration** blade.</span></span> <span data-ttu-id="4eb59-254">**[IP アドレス]** に手順 2 の IP アドレスを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-254">Under **IP address**, paste in the IP address from step 2.</span></span>

    ![[IP アドレス] フィールドのスクリーンショット](./images/local-net-gw.png)

5. <span data-ttu-id="4eb59-256">**[保存]** をクリックし、処理が完了するまで待ちます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-256">Click **Save** and wait for the operation to complete.</span></span> <span data-ttu-id="4eb59-257">完了までに約 5 分かかります。</span><span class="sxs-lookup"><span data-stu-id="4eb59-257">It can take about 5 minutes.</span></span>

6. <span data-ttu-id="4eb59-258">`onprem-vpn-gateway1-pip` という名前のリソースを探します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-258">Find the resource named `onprem-vpn-gateway1-pip`.</span></span> <span data-ttu-id="4eb59-259">**[概要]** ブレードに表示されている IP アドレスをコピーします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-259">Copy the IP address shown in the **Overview** blade.</span></span>

7. <span data-ttu-id="4eb59-260">`ra-vpn-lgw` という名前のリソースを探します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-260">Find the resource named `ra-vpn-lgw`.</span></span>

8. <span data-ttu-id="4eb59-261">**[構成]** ブレードをクリックします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-261">Click the **Configuration** blade.</span></span> <span data-ttu-id="4eb59-262">**[IP アドレス]** に手順 6 の IP アドレスを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-262">Under **IP address**, paste in the IP address from step 6.</span></span>

9. <span data-ttu-id="4eb59-263">**[保存]** をクリックし、処理が完了するまで待ちます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-263">Click **Save** and wait for the operation to complete.</span></span>

10. <span data-ttu-id="4eb59-264">接続を確認するには、各ゲートウェイの **[接続]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-264">To verify the connection, go to the **Connections** blade for each gateway.</span></span> <span data-ttu-id="4eb59-265">状態が **[接続済み]** であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-265">The status should be **Connected**.</span></span>

### <a name="verify-that-network-traffic-reaches-the-web-tier"></a><span data-ttu-id="4eb59-266">ネットワーク トラフィックが Web 階層に到達していることを確認する</span><span class="sxs-lookup"><span data-stu-id="4eb59-266">Verify that network traffic reaches the web tier</span></span>

1. <span data-ttu-id="4eb59-267">Azure portal で、作成したリソース グループに移動します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-267">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="4eb59-268">プライベート DMZ の前面にあるロード バランサーである `int-dmz-lb` という名前のリソースを探します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-268">Find the resource named `int-dmz-lb`, which is the load balancer in front of the private DMZ.</span></span> <span data-ttu-id="4eb59-269">**[概要]** ブレードからプライベート IP アドレスをコピーします。</span><span class="sxs-lookup"><span data-stu-id="4eb59-269">Copy the private IP address from the **Overview** blade.</span></span>

3. <span data-ttu-id="4eb59-270">`jb-vm1` という名前の VM を見つけます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-270">Find the VM named `jb-vm1`.</span></span> <span data-ttu-id="4eb59-271">**[接続]** をクリックし、リモート デスクトップを使用して VM に接続します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-271">Click **Connect** and use Remote Desktop to connect to the VM.</span></span> <span data-ttu-id="4eb59-272">ユーザー名とパスワードは、onprem.json ファイルに指定されています。</span><span class="sxs-lookup"><span data-stu-id="4eb59-272">The user name and password are specified in the onprem.json file.</span></span>

4. <span data-ttu-id="4eb59-273">リモート デスクトップ セッションから Web ブラウザーを開き、手順 2 の IP アドレスに移動します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-273">From the Remote Desktop Session, open a web browser and navigate to the IP address from step 2.</span></span> <span data-ttu-id="4eb59-274">既定の Apache2 サーバーのホーム ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4eb59-274">You should see the default Apache2 server home page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4eb59-275">次の手順</span><span class="sxs-lookup"><span data-stu-id="4eb59-275">Next steps</span></span>

- <span data-ttu-id="4eb59-276">[Azure とインターネットの間の DMZ](./secure-vnet-dmz.md) を実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-276">Learn how to implement a [DMZ between Azure and the Internet](./secure-vnet-dmz.md).</span></span>
- <span data-ttu-id="4eb59-277">[高可用性ハイブリッド ネットワーク アーキテクチャ][ra-vpn-failover]を実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4eb59-277">Learn how to implement a [highly available hybrid network architecture][ra-vpn-failover].</span></span>
- <span data-ttu-id="4eb59-278">Azure でのネットワーク セキュリティの管理の詳細については、「[Microsoft クラウド サービスとネットワーク セキュリティ][cloud-services-network-security]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-278">For more information about managing network security with Azure, see [Microsoft cloud services and network security][cloud-services-network-security].</span></span>
- <span data-ttu-id="4eb59-279">Azure のリソース保護の詳細については、「[Microsoft Azure セキュリティの概要][getting-started-with-azure-security]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-279">For detailed information about protecting resources in Azure, see [Getting started with Microsoft Azure security][getting-started-with-azure-security].</span></span>
- <span data-ttu-id="4eb59-280">Azure ゲートウェイ接続の全体にわたるセキュリティ上の問題に対処することの詳細については、「[Azure とオンプレミスの VPN を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-vpn-gateway-security]」と「[Azure ExpressRoute を使ってハイブリッド ネットワーク アーキテクチャを実装する][guidance-expressroute-security]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4eb59-280">For additional details on addressing security concerns across an Azure gateway connection, see [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-security] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-security].</span></span>
- [<span data-ttu-id="4eb59-281">Azure でのネットワーク仮想アプライアンスに関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="4eb59-281">Troubleshoot network virtual appliance issues in Azure</span></span>](/azure/virtual-network/virtual-network-troubleshoot-nva)

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[azure-marketplace-nva]: https://azuremarketplace.microsoft.com/marketplace/apps/category/networking
[cloud-services-network-security]: /azure/best-practices-network-security
[getting-started-with-azure-security]: /azure/security/azure-security-getting-started
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-hybrid
[guidance-expressroute]: ../hybrid-networking/expressroute.md
[guidance-expressroute-availability]: ../hybrid-networking/expressroute.md#availability-considerations
[guidance-expressroute-manageability]: ../hybrid-networking/expressroute.md#manageability-considerations
[guidance-expressroute-security]: ../hybrid-networking/expressroute.md#security-considerations
[guidance-expressroute-scalability]: ../hybrid-networking/expressroute.md#scalability-considerations
[guidance-vpn-gateway]: ../hybrid-networking/vpn.md
[guidance-vpn-gateway-availability]: ../hybrid-networking/vpn.md#availability-considerations
[guidance-vpn-gateway-manageability]: ../hybrid-networking/vpn.md#manageability-considerations
[guidance-vpn-gateway-scalability]: ../hybrid-networking/vpn.md#scalability-considerations
[guidance-vpn-gateway-security]: ../hybrid-networking/vpn.md#security-considerations
[ip-forwarding]: /azure/virtual-network/virtual-networks-udr-overview#ip-forwarding
[ra-expressroute]: ../hybrid-networking/expressroute.md
[ra-n-tier]: ../virtual-machines-windows/n-tier.md
[ra-vpn]: ../hybrid-networking/vpn.md
[ra-vpn-failover]: ../hybrid-networking/expressroute-vpn-failover.md
[rbac]: /azure/active-directory/role-based-access-control-configure
[rbac-custom-roles]: /azure/active-directory/role-based-access-control-custom-roles
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview
[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx
[wireshark]: https://www.wireshark.org/
