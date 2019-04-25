---
title: 銀行間での分散型信頼
titleSuffix: Azure Example Scenarios
description: 一元化されたデータベースを使用せずに、信頼できる環境を確立して、通信と情報共有を実現します。
author: vitoc
ms.date: 09/09/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: csa-team
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-decentralized-trust.png
ms.openlocfilehash: 3bc75e59a4d391c74a0e606f9670c88509a3375b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640449"
---
# <a name="decentralized-trust-between-banks-on-azure"></a><span data-ttu-id="78e8c-103">Azure における銀行間での分散型信頼</span><span class="sxs-lookup"><span data-stu-id="78e8c-103">Decentralized trust between banks on Azure</span></span>

<span data-ttu-id="78e8c-104">このシナリオ例は、一元的なデータベースに頼らずに、情報共有のための信頼できる環境を確立したい銀行などの機関に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-104">This example scenario is useful for banks or any other institutions that want to establish a trusted environment for information sharing without resorting to a centralized database.</span></span> <span data-ttu-id="78e8c-105">この例の目的として、銀行間のクレジット スコア情報を維持するという状況でシナリオを説明しますが、このアーキテクチャは、組織のコンソーシアムが、単一のパーティが運用する一元的なシステムを利用せずに、検証済みの情報を相互に共有する任意のシナリオに適用できます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-105">For the purpose of this example, we will describe the scenario in the context of maintaining credit score information between banks, but the architecture can be applied to any scenario where a consortium of organizations want to share validated information with one another without resorting to the use of a central system ran by one single party.</span></span>

<span data-ttu-id="78e8c-106">従来の金融システム内の銀行は、個人のクレジット スコアと履歴に関する情報を得るために、信用調査機関などの一元的なソースに依存しています。</span><span class="sxs-lookup"><span data-stu-id="78e8c-106">Traditionally, banks within a financial system rely on centralized sources such as credit bureaus for information on an individual's credit score and history.</span></span> <span data-ttu-id="78e8c-107">一元的なアプローチは運用上のリスクが集中しまた。また、不要なサードパーティが介在することもあります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-107">A centralized approach presents a concentration of operational risk and sometimes an unnecessary third party.</span></span>

<span data-ttu-id="78e8c-108">DLT (分散型台帳テクノロジ) により、銀行のコンソーシアムは、より効率的で、攻撃の影響を受けづらい分散型システムを構築できます。このシステムは、プライバシー、スピード、コストの面で従来の課題を解決する画期的な構造を実装できる新しいプラットフォームとして機能します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-108">With DLTs (distributed ledger technology), a consortium of banks can establish a decentralized system that can be more efficient, less susceptible to attack, and serve as a new platform where innovative structures can be implemented to solve traditional challenges with privacy, speed, and cost.</span></span>

<span data-ttu-id="78e8c-109">この例では、メンバーの銀行が固有のノードを構築できる効率的なプライベートな Ethereum PoA ブロックチェーンをデプロイするために、仮想マシン スケール セット、Virtual Network、Key Vault、Storage、Load Balancer、Monitor などの Azure サービスをすばやくプロビジョニングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-109">This example will show you how Azure services such as virtual machine scale sets, Virtual Network, Key Vault, Storage, Load Balancer, and Monitor can be quickly provisioned for the deployment of an efficient private Ethereum PoA blockchain where member banks can establish their own nodes.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="78e8c-110">関連するユース ケース</span><span class="sxs-lookup"><span data-stu-id="78e8c-110">Relevant use cases</span></span>

<span data-ttu-id="78e8c-111">その他の関連するユース ケース:</span><span class="sxs-lookup"><span data-stu-id="78e8c-111">Other relevant use cases include:</span></span>

- <span data-ttu-id="78e8c-112">多国籍企業の異なる事業部門間での割り当て済み予算の移動</span><span class="sxs-lookup"><span data-stu-id="78e8c-112">Movement of allocated budgets between different business units of a multinational corporation</span></span>
- <span data-ttu-id="78e8c-113">国境を越えた支払い</span><span class="sxs-lookup"><span data-stu-id="78e8c-113">Cross-border payments</span></span>
- <span data-ttu-id="78e8c-114">貿易金融のシナリオ</span><span class="sxs-lookup"><span data-stu-id="78e8c-114">Trade finance scenarios</span></span>
- <span data-ttu-id="78e8c-115">複数の企業が参加するロイヤルティ システム</span><span class="sxs-lookup"><span data-stu-id="78e8c-115">Loyalty systems involving different companies</span></span>
- <span data-ttu-id="78e8c-116">サプライ チェーンのエコシステム</span><span class="sxs-lookup"><span data-stu-id="78e8c-116">Supply chain ecosystems</span></span>

## <a name="architecture"></a><span data-ttu-id="78e8c-117">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="78e8c-117">Architecture</span></span>

![分散型の銀行信頼アーキテクチャ図](./media/architecture-decentralized-trust.png)

<span data-ttu-id="78e8c-119">このシナリオでは、複数のメンバーから構成されるコンソーシアム内に、スケーラブルで、セキュリティで保護され、監視されているプライベート エンタープライズ ブロックチェーン ネットワークを構築するために必要なバックエンド コンポーネントについて説明します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-119">This scenario covers the back-end components that are necessary to create a scalable, secure, and monitored private, enterprise blockchain network within a consortium of two or more members.</span></span> <span data-ttu-id="78e8c-120">このようなコンポーネント (つまり、さまざまなサブスクリプションとリソース グループ内のコンポーネント) をプロビジョニングする方法と、接続要件 (つまり、VPN または ExpressRoute) の詳細は、組織のポリシー要件に基づいてお客様が考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-120">Details of how these components are provisioned (that is, within different subscriptions and resource groups) as well as the connectivity requirements (that is, VPN or ExpressRoute) are left for your consideration based on your organization's policy requirements.</span></span> <span data-ttu-id="78e8c-121">データ フローは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="78e8c-121">Here's how data flows:</span></span>

1. <span data-ttu-id="78e8c-122">銀行 A は、JSON-RPC 経由でブロックチェーン ネットワークにトランザクションを送信することによって個人のクレジット記録を作成/更新します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-122">Bank A creates/updates an individual's credit record by sending a transaction to the blockchain network via JSON-RPC.</span></span>
2. <span data-ttu-id="78e8c-123">データは、銀行 A のプライベート アプリケーション サーバーから Azure ロード バランサーに流れ、次に仮想マシン スケール セット上の検証ノード VM へと流れます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-123">Data flows from Bank A's private application server to the Azure load balancer and subsequently to a validating node VM on the virtual machine scale set.</span></span>
3. <span data-ttu-id="78e8c-124">事前に設定された時間 (このシナリオでは 2 秒) で、Ethereum PoA ネットワークによってブロックが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-124">The Ethereum PoA network creates a block at a preset time (2 seconds for this scenario).</span></span>
4. <span data-ttu-id="78e8c-125">トランザクションは、作成されたブロックにバンドルされ、ブロックチェーン ネットワーク全体で検証されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-125">The transaction is bundled into the created block and validated across the blockchain network.</span></span>
5. <span data-ttu-id="78e8c-126">銀行 B は、JSON-RPC を介して同様に自身のノードと通信することで、銀行 A によって作成されたクレジット記録を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-126">Bank B can read the credit record created by bank A by communicating with its own node similarly via JSON-RPC.</span></span>

### <a name="components"></a><span data-ttu-id="78e8c-127">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="78e8c-127">Components</span></span>

- <span data-ttu-id="78e8c-128">仮想マシン スケール セット内の仮想マシンには、ブロックチェーン用の検証コントロール プロセスをホストするオンデマンドの計算機能があります</span><span class="sxs-lookup"><span data-stu-id="78e8c-128">Virtual machines within virtual machine scale sets provides the on-demand compute facility to host the validator processes for the blockchain</span></span>
- <span data-ttu-id="78e8c-129">Key Vault は、各検証コントロールの秘密キーのセキュリティで保護された記憶域として使用されます</span><span class="sxs-lookup"><span data-stu-id="78e8c-129">Key Vault is used as the secure storage facility for the private keys of each validator</span></span>
- <span data-ttu-id="78e8c-130">Load Balancer は、RPC、ピアリング、およびガバナンスの DApp 要求を分散します</span><span class="sxs-lookup"><span data-stu-id="78e8c-130">Load Balancer spreads the RPC, peering, and Governance DApp requests</span></span>
- <span data-ttu-id="78e8c-131">Storage は、永続的なネットワーク情報をホストし、リースを調整します</span><span class="sxs-lookup"><span data-stu-id="78e8c-131">Storage hosting persistent network information and coordinating leasing</span></span>
- <span data-ttu-id="78e8c-132">Operations Management Suite (複数の Azure サービスのバンドル) では、使用できるノード、1 分あたりのトランザクション、およびコンソーシアムのメンバーを分析できます</span><span class="sxs-lookup"><span data-stu-id="78e8c-132">Operations Management Suite (a bundling of a few Azure services) provides insight into available nodes, transactions per minute and consortium members</span></span>

### <a name="alternatives"></a><span data-ttu-id="78e8c-133">代替手段</span><span class="sxs-lookup"><span data-stu-id="78e8c-133">Alternatives</span></span>

<span data-ttu-id="78e8c-134">この例では、Ethereum PoA アプローチが選択されています。これは、信頼性が高く、分散型でわかりやすい方法で情報を簡単に交換または共有できる環境を作りたい組織のコンソーシアムのエントリ ポイントに適しているからです。</span><span class="sxs-lookup"><span data-stu-id="78e8c-134">The Ethereum PoA approach is chosen for this example because it is a good entry point for a consortium of organizations that want to create an environment where information can be exchanged and shared with one another easily in a trusted, decentralized, and easy to understand way.</span></span> <span data-ttu-id="78e8c-135">Ethereum PoA ブロックチェーンを開始するコンソーシアムリーダーだけでなく、コンソーシアム内のメンバー組織にとっても、自身のリソース グループとサブスクリプション内にある Azure リソースをスピン アップして既存のネットワークに参加する場合に、Azure ソリューション テンプレートも簡単で便利です。</span><span class="sxs-lookup"><span data-stu-id="78e8c-135">The available Azure solution templates also provide a fast and convenient way not just for a consortium leader to start an Ethereum PoA blockchain, but also for member organizations in the consortium to spin up their own Azure resources within their own resource group and subscription to join an existing network.</span></span>

<span data-ttu-id="78e8c-136">その他の拡張されたシナリオや異なるシナリオでは、トランザクションのプライバシーなどの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-136">For other extended or different scenarios, concerns such as transaction privacy may arise.</span></span> <span data-ttu-id="78e8c-137">たとえば、有価証券取引のシナリオでは、コンソーシアムのメンバーは、他のメンバーにも取引を見せたくない場合があります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-137">For example, in a securities transfer scenario, members in a consortium may not want their transactions to be visible even to other members.</span></span> <span data-ttu-id="78e8c-138">Ethereum PoA の他の代替手段には、このような懸案事項を独自の方法で解決するものがあります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-138">Other alternatives to Ethereum PoA exist that addresses these concerns in their own way:</span></span>

- <span data-ttu-id="78e8c-139">Corda</span><span class="sxs-lookup"><span data-stu-id="78e8c-139">Corda</span></span>
- <span data-ttu-id="78e8c-140">Quorum</span><span class="sxs-lookup"><span data-stu-id="78e8c-140">Quorum</span></span>
- <span data-ttu-id="78e8c-141">Hyperledger</span><span class="sxs-lookup"><span data-stu-id="78e8c-141">Hyperledger</span></span>

## <a name="considerations"></a><span data-ttu-id="78e8c-142">考慮事項</span><span class="sxs-lookup"><span data-stu-id="78e8c-142">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="78e8c-143">可用性</span><span class="sxs-lookup"><span data-stu-id="78e8c-143">Availability</span></span>

<span data-ttu-id="78e8c-144">[Azure Monitor][monitor] は、可用性を確保するために、問題のブロックチェーン ネットワークの継続的な監視に使用されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-144">[Azure Monitor][monitor] is used to continuously monitor the blockchain network for issues to ensure availability.</span></span> <span data-ttu-id="78e8c-145">このシナリオで使用されているブロックチェーン ソリューション テンプレートのデプロイに成功すると、Azure Monitor に基づくカスタム監視ダッシュボードへのリンクが送信されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-145">A link to a custom monitoring dashboard based on Azure Monitor will be sent to you upon successful deployment of the blockchain solution template used in this scenario.</span></span> <span data-ttu-id="78e8c-146">ダッシュボードには、過去 30 分間のハートビートと他の便利な統計情報を報告するノードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-146">The dashboard shows nodes that are reporting heartbeats in the past 30 minutes as well as other useful statistics.</span></span>

### <a name="scalability"></a><span data-ttu-id="78e8c-147">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="78e8c-147">Scalability</span></span>

<span data-ttu-id="78e8c-148">ブロックチェーンの一般的な考慮事項として、事前に設定した時間内にブロックチェーンに含めることができるトランザクションの数があります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-148">A popular concern for blockchain is the number of transactions that a blockchain can include within a preset amount of time.</span></span> <span data-ttu-id="78e8c-149">このシナリオでは、スケーラビリティとして Proof-of-Work よりも管理しやすい Proof-of-Authority を使用します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-149">This scenario uses Proof-of-Authority where such scalability can be better managed than Proof-of-Work.</span></span> <span data-ttu-id="78e8c-150">Proof-of-Authority ベースのネットワークでは、合意の参加者が既知であり、管理されているため、相互に認識している組織のコンソーシアムのプライベート ブロックチェーンに適しています。</span><span class="sxs-lookup"><span data-stu-id="78e8c-150">In Proof-of-Authority&ndash;based networks, consensus participants are known and managed, making it more suitable for private blockchain for a consortium of organization that knows one another.</span></span> <span data-ttu-id="78e8c-151">平均ブロック時間、1 分あたりのトランザクション数、コンピューティング リソースの消費量などのパラメーターは、カスタム ダッシュボードで簡単に監視できます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-151">Parameters such as average block time, transactions per minute and compute resource consumption can be easily monitored via the custom dashboard.</span></span> <span data-ttu-id="78e8c-152">リソースは、スケールの要件に基づいて調整することができます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-152">Resources can then be adjusted accordingly based on scale requirements.</span></span>

<span data-ttu-id="78e8c-153">スケーラブルなソリューションの設計に関する一般的なガイダンスについては、Azure アーキテクチャ センターの[スケーラビリティのチェックリスト][scalability]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78e8c-153">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="78e8c-154">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="78e8c-154">Security</span></span>

<span data-ttu-id="78e8c-155">[Azure Key Vault][vault] は、検証コントロールの秘密キーを簡単に保存および管理するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-155">[Azure Key Vault][vault] is used to easily store and manage the private keys of validators.</span></span> <span data-ttu-id="78e8c-156">この例の既定のデプロイでは、インターネット経由でアクセスできるブロックチェーン ネットワークが作成されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-156">The default deployment in this example creates a blockchain network that is accessible via the internet.</span></span> <span data-ttu-id="78e8c-157">プライベート ネットワークが必要な運用シナリオでは、メンバーは VNet と VNet VPN 間のゲートウェイ接続を介して相互に接続できます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-157">For production scenario where a private network is desired, members can be connected to each other via VNet-to-VNet VPN gateway connections.</span></span> <span data-ttu-id="78e8c-158">VPN の構成手順については、以下の関連リソースのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="78e8c-158">The steps for configuring a VPN are included in the related resources section below.</span></span>

<span data-ttu-id="78e8c-159">セキュリティで保護されたソリューションの設計に関する一般的なガイダンスについては、「[Azure のセキュリティのドキュメント][security]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78e8c-159">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="78e8c-160">回復性</span><span class="sxs-lookup"><span data-stu-id="78e8c-160">Resiliency</span></span>

<span data-ttu-id="78e8c-161">Ethereum PoA ブロックチェーンでは、検証コントロール ノードを異なるリージョンにデプロイできるので、ある程度の回復性を実現することができます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-161">The Ethereum PoA blockchain can itself provide some degree of resilience as the validator nodes can be deployed in different regions.</span></span> <span data-ttu-id="78e8c-162">Azure には、世界中の 54 を超えるリージョンにデプロイするオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="78e8c-162">Azure has options for deployments in over 54 regions worldwide.</span></span> <span data-ttu-id="78e8c-163">このようなシナリオのブロックチェーンで独自で斬新な協力を構築し、回復性を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-163">A blockchain such as the one in this scenario provides unique and refreshing possibilities of cooperation to increase resilience.</span></span> <span data-ttu-id="78e8c-164">ネットワークの回復性は、単一の一元化されたパーティだけでなく、コンソーシアムのすべてのメンバーによって実現されます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-164">The resilience of the network is not just provided for by a single centralized party but all members of the consortium.</span></span> <span data-ttu-id="78e8c-165">Proof-of-Authority ベースのブロックチェーンで、ネットワークの回復性をさらに計画し、検討することができます。</span><span class="sxs-lookup"><span data-stu-id="78e8c-165">A proof-of-authority&ndash;based blockchain allows network resilience to be even more planned and deliberate.</span></span>

<span data-ttu-id="78e8c-166">回復性があるソリューションの設計に関する一般的なガイダンスについては、「[Designing reliable Azure applications](../../reliability/index.md)」 (信頼性の高い Azure アプリケーションの設計) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78e8c-166">For general guidance on designing resilient solutions, see [Designing reliable Azure applications](../../reliability/index.md).</span></span>

## <a name="pricing"></a><span data-ttu-id="78e8c-167">価格</span><span class="sxs-lookup"><span data-stu-id="78e8c-167">Pricing</span></span>

<span data-ttu-id="78e8c-168">このシナリオの実行コストを調べることができるように、すべてのサービスがコスト計算ツールで事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="78e8c-168">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="78e8c-169">特定のユース ケースについて価格の変化を確認するには、予想されるパフォーマンスと可用性の要件に合わせて該当する変数を変更します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-169">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected performance and availability requirements.</span></span>

<span data-ttu-id="78e8c-170">ご自身のアプリケーションを実行するスケール セット VM インスタンスの数に基づいて、3 つのサンプル コスト プロファイルが用意されています (インスタンスは異なるリージョンに存在する可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="78e8c-170">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications (the instances can reside in different regions).</span></span>

- <span data-ttu-id="78e8c-171">[S][small-pricing]: この価格設定の例は、監視をオフにした月に 2 個の VM に対応しています</span><span class="sxs-lookup"><span data-stu-id="78e8c-171">[Small][small-pricing]: this pricing example correlates to 2 VMs per month with monitoring turned off</span></span>
- <span data-ttu-id="78e8c-172">[M][medium-pricing]: この価格設定の例は、監視をオンにした月に 7 個の VM に対応しています</span><span class="sxs-lookup"><span data-stu-id="78e8c-172">[Medium][medium-pricing]: this pricing example correlates to 7 VMs per month with monitoring turned on</span></span>
- <span data-ttu-id="78e8c-173">[L][large-pricing]: この価格設定の例は、監視をオンにした月に 15 個の VM に対応しています</span><span class="sxs-lookup"><span data-stu-id="78e8c-173">[Large][large-pricing]: this pricing example correlates to 15 VMs per month with monitoring turned on</span></span>

<span data-ttu-id="78e8c-174">これは、1 つのコンソーシアム メンバーがブロックチェーン ネットワークを開始または参加する場合の価格設定です。</span><span class="sxs-lookup"><span data-stu-id="78e8c-174">The above pricing is for one consortium member to start or join a blockchain network.</span></span> <span data-ttu-id="78e8c-175">通常、複数の企業や組織が参加しているコンソーシアムでは、各メンバーが独自の Azure サブスクリプションを取得します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-175">Typically in a consortium where there are multiple companies or organizations involved, each member will get their own Azure subscription.</span></span>

## <a name="next-steps"></a><span data-ttu-id="78e8c-176">次の手順</span><span class="sxs-lookup"><span data-stu-id="78e8c-176">Next Steps</span></span>

<span data-ttu-id="78e8c-177">このシナリオの例を確認するには、Azure 上に [Ethereum PoA ブロックチェーン デモ アプリケーション][deploy]をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="78e8c-177">To see an example of this scenario, deploy the [Ethereum PoA blockchain demo application][deploy] on Azure.</span></span> <span data-ttu-id="78e8c-178">次に、[シナリオ ソース コードの README][source] を確認します。</span><span class="sxs-lookup"><span data-stu-id="78e8c-178">Then review the [README of the scenario source code][source].</span></span>

## <a name="related-resources"></a><span data-ttu-id="78e8c-179">関連リソース</span><span class="sxs-lookup"><span data-stu-id="78e8c-179">Related resources</span></span>

<span data-ttu-id="78e8c-180">Azure 用の Ethereum Proof-Authority ソリューション テンプレートを使用する方法の詳細については、こちらの[使用ガイド][guide]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78e8c-180">For more information on using the Ethereum Proof-of-Authority solution template for Azure, review this [usage guide][guide].</span></span>

<!-- links -->
[small-pricing]: https://azure.com/e/4e429d721eb54adc9a1558fae3e67990
[medium-pricing]: https://azure.com/e/bb42cd77437744be8ed7064403bfe2ef
[large-pricing]: https://azure.com/e/e205b443de3e4adfadf4e09ffee30c56
[guide]: /azure/blockchain-workbench/ethereum-poa-deployment
[deploy]: https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium
[source]: https://github.com/vitoc/creditscoreblockchain
[monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[scalability]: /azure/architecture/checklist/scalability
[security]: /azure/security/
[vault]: https://azure.microsoft.com/services/key-vault/
