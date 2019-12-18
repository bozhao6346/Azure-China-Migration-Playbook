---
title: CONTOSO公司在Azure China进行区域迁移实例
description: This article describes detailed steps to migrate CONTOSO Corp. resourcesfrom Azure China East1 to China North2 region.
author: siguo
ms.service: china 
ms.topic: migrate
layout: ContentPage 
ms.date: 12/18/2019
ms.author: siguo

---


# <a name="move-azure-vms-to-another-region"></a>CONTOSO公司在Azure China进行区域迁移实例

# 迁移背景

CONTOSO 商业有限公司是一家提倡简约自然的家居品牌，在全球各地均有分公司，其线下门店和网络商城遍布全球。CONTOSO 中国子公司使用 Azure China 的服务创建自己的网络商城解决方案，用于增加销售业绩并将基础结构的管理任务留给云供应商。

CONTOSO 公司计划将部署于 Azure 中国东部1区域的数据中心移动到 Azure 中国北部2区域。 公司要求你作为项目负责人调研迁移可行性，生成评估报告和迁移计划，列出迁移过程各个阶段的注意事项。本指南旨在帮助迁移负责人在Azure China区域间迁移的各个阶段给出指示和建议。以下是你需要了解的一些迁移背景：

## 公司痛点
CONTOSO公司现有的IT架构已经使用了4年，技术框架和服务已经有些过时，IT系统已进入生命周期的晚期。公司管理层近期有计划进行架构调整和升级，以便承载公司业务快速增长和未来的潜在需求。公司的痛点如下：  

* **功能**：随着电商平台的逐渐崛起，CONTOSO需要使用更强大的Azure功能支撑平台。例如分析站点流量和浏览到购买的转化率，以根据客户行为确定特别优惠和新产品；创造具有目标内容和优惠的个性化购物体验，增加满意度等。需使用Azure上的AKS服务/时序见解/自动化等功能。但是目前所在的中国东部1区域暂不支持，限制了CONTOSO公司的客户的粘性和增长。
* **成本**：使用Azure China已经很大限度地降低了CONTOSO的基础架构管理成本，但是随着用量和功能的增加，CONTOSO正在寻求性价比更高的产品和服务来降低运维和使用成本，例如用高性能的虚拟机淘汰型号较老的D系列虚拟机，使用Kubernetes和SQL托管实例等。
* **规模**：随着中国分公司的不断发展，CONTOSO公司预计未来三年其在中国的网络商城规模将会翻倍，需要更大的云空间来承载快速增长，而中国东部1数据中心的容量无法承载其增长。
* **弹性**：在零售行业的热点活动季节，比如“双十一” ，“六一八”等，CONTOSO和各企业对云服务需求的集中释放，导致某些时间段的资源紧缺和弹性不足。CONTOSO需要更加适应业务规模和季节效应的电子商务解决方案。当客户对产品或服务的需求增加时，要准备好自动应对更多的客户和事务。



# 可行性评估
你已经了解CONTOSO公司的背景和痛点，在开始迁移项目之前，你还需要根据 CONTOSO 公司的实际情况选择合适的区域，召集合适的人员，并共同做好以下方面的可行性评估。
CONTOSO在Azure China的使用情况:   

*	CONTOSO在Azure China上使用中国东部1区域的数据中心搭载网络商城。  
*	CONTOSO 在该数据中心上使用的资源为：计算/存储/网络。  
  o	计算：共15台虚拟机，合计每月约使用 40k 小时。虚拟机的主要型号为：D2/D3/D4/D5/F4/F8。  
  o	存储：共11块硬盘，型号主要为 P30/P10/P15/P6/S30/S15 等。另有 GRS 数据存储/LRS Snapshots等存储实例共40个。  
  o	网络：1个 Basic Gateway，1个 Standard Static Public IP，1个Dynamic Public IP。  
*	CONTOSO的网络商城从4年前搭建之后就没有进行过架构改造，IT系统已经进入生命周期后期。  

## 区域选择原则
* **更大空间**：Azure 在中国北部2区域的云空间充足，可以满足CONTOSO的快速增长以及由业务规模和季节效应影响的弹性缩放。
* **更多资源**：如果将CONTOSO的资源搬迁到中该国北部2区域，除了原有的资源可以使用，还有很多高级资源可供选择和优化。例如：更高级的虚拟机和云服务，Azure Kubernetes服务，Azure SQL Database托管实例，Azure时序见解，自动化等服务可提供针对电商平台的个性化解决方案。除了预计在2020年上线的语音服务/Azure functions/Azure Databricks之外，中国北部2区域未来也会持续推出更多新服务。
* **更低价格**：Azure在中国北部2区域的的CPP价格比中国东部1区域大约便宜20%-30%左右。CONTOSO对计算和存储资源的用量较大且持续增长，迁移到中国北部2 区域可以在长期获得较大优惠。同时可以使用性价比更高的产品和服务来降低运维和使用成本。
•	为什么去CN2不是CE2？没有一个很solid的理由

## 人员规划 
* 组建云迁移团队V-Team，标识在迁移结果中扮演某个角色或作为利益干系人的关键人员，包括：Azure管理团队，金融和会计团队，财务、IT 基础结构团队等。需要收集各个团队对云迁移的看法和需求，了解业务对变更实施和审批是否有特定要求，并保证所有利益干系人了解情况并达成共识。
* 建立核心云策略团队，可以帮助你的组织准备好使用云，并指导整体云迁移工作。 此团队负责跟进整个迁移过程，确定迁移的业务理由，并确定迁移工作的最佳高级解决方案。 它们还帮助识别特定的应用程序和业务利益干系人并与之合作，以确保迁移成功。
* 确保你的员工准备好管理新数据中心，并接受必要的培训。

## 技术评估 

您应当召集CONTOSO核心云策略团队的所有成员，对云迁移进行完备的技术评估，以了解计划迁移的组织规模，这一点非常重要。需要担任相关职能的工作人员提供CONTOSO对 Azure 使用的完整信息。

在评估阶段，您需要进行以下步骤：
* 架构评估：你需要跟 IT 和技术团队讨论你们计划用何种方式执行云迁移，并设计不同方案
 	 
   **直接迁移**：可将位于当前Azure中国东部1区域的状态转移到中国北部2区域，并且只需对整个体系结构进行极少量的更改。
     -	由于CONTOSO中国分公司打算改优化系统结构，故无法采用直接迁移。  
     
   **创新**：当在云采用工作过程中，数据或业务逻辑发生了重要变化时，它被视为一种创新类型的转换。 在创建新数据或新的业务逻辑时，情况也是如此。 对于任何创新方案，资产的迁移可能表示所需的最少工作量。 对于这些方案，团队应设计一组技术数据输入来衡量相对复杂性。
     - CONTOSO公司目前使用的一款D系列虚拟机型号较老，各方面指标不够强大，无法满足未来的工作负载。技术团队预计要将D系列虚拟机全部更换成DSv2系列，可为企业提供更快的 CPU、更好的本地磁盘性能和更高内存。
     - CONTOSO公司计划使用 Kubernetes 和 SQL 托管实例功能来优化数据存储性能。
     - CONTOSO公司计划使用 Azure 上的 AKS 服务/时序见解/自动化等功能分析站点流量和浏览到购买的转化率，以根据客户行为确定特别优惠和新产品，并创造具有目标内容和优惠的个性化购物体验，增加满意度。

* 资源评估：每个订阅管理员和租户管理员应运行一系列脚本来列出资源组、每个资源组中的资源以及环境中的资源组部署设置。确保应用程序体系结构文档与 Azure 资源列表保持一致。 
* 环境评估：了解源环境和目的环境之间服务与配置的一致性；估算在目的环境中的运营成本和资源成本。   
*	依赖关系评估：您应当记录 Azure 中各应用程序之间以及与外部系统的依赖关系。
*	复杂性评估：评估 Azure 中各资源和总体的复杂性。 
*	性能分析：评估在目标环境中的各个资源能否达到规定的性能要求。  
*	初步测试：执行有限范围的测试迁移来验证迁移计划。  
*	在此阶段结束时，您将拥有：
  *	需要迁移的 Azure 资源的完整列表。
  *	资源之间的依赖关系列表。
  *	有关迁移工作复杂性的信息。
  *	为决策和工作计划生成综合评估报告

## 成本评估
* 你需要与商务和财务人员讨论重新签署 CPP 合同，并根据 CPP 合同中承诺的资源进行架构调整。  
* 估算在目标环境中的日常运维成本及使用成本。  
*	估算整个迁移过程中必需的人员/工具/额外资源及合作伙伴等的成本。  
*	确保具有相应的审批和预算来完成迁移。

## 风险评估
*	整个迁移过程可能会持续几个月，你需要考虑什么时间开展你的迁移项目，迁移窗口是否覆盖旺季或者关键节点。  
*	考虑迁移项目对 CONTOSO 公司的业务有多大影响范围，是否会导致业务中断。
*	考虑遇到风险时是否有足够的预算和人员帮助你快速解决问题。 
*	微软推荐您在迁移之前跟您的微软AE沟通，确认你的个性化需求，针对潜在风险寻求建议，为整个迁移项目提供帮助。


# 迁移过程
当 CONTOSO 中国分公司确定开启其 Azure China 迁移项目，你可以开始协调各个部门开始以下四个阶段的工作。
## 计划阶段
### 关键任务
在计划阶段，您应当完成以下任务：
*	使用在评估阶段完成的依赖关系分析的输出结果来定义相关组件。请考虑通过迁移包迁移相关组件并为每个迁移包创建用户验收测试计划。 
* （可选）利用迁移过程来应用 Gartner 5-R 标准和优化工作负载。  
*	确定目标 Azure 区域中的目标环境和要求。
*	识别目标 Azure 租户（如有必要，创建一个）并创建订阅。 
*	设计详细的迁移计划报告。 
*	确定合适的迁移时间线和计划表。 
*	在你评估环境时，将为你提供一个包含成本分析步骤的完美机会。 使用评估活动收集的数据，你应该能够分析和预测成本。 除了任何一次性成本之外，此成本预测还应考虑消耗量服务成本（例如增加的数据入口）。
### 关键产出
在此阶段结束时，您将拥有： 
* 迁移计划报告：部署体系结构的具体设计文档。
* 成本分析报告：包括迁移成本和目标环境的资源消耗等。  
*	新的CPP合同：根据预测CONTOSO在中国北部2区域的资源用量，重新签订CPP合同。这需要对 Azure 做出前期货币承诺，同时可让 CONTOSO 获得众多权益，包括灵活的计费选项和最优惠价格。  
*	时间线和计划表：根据迁移计划表，为迁移项目建立大致时间线和里程碑。

## 迁移阶段
### 关键任务
1.	在Azure中国北部区域2 中创建新的资源组CONTOSORG2。
2.	在目标区域中创建新的虚拟网络https://docs.azure.cn/zh-cn/articles/azure-china-migration-playbook/china-migration-guidance-networking
3.	在新区域中创建和配置 VPN 网关的新实例
4.	使用ASR迁移虚拟机到中国北部2 的资源组CONTOSORG2 https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-tutorial-migrate
5.	迁移存储资源：https://docs.azure.cn/zh-cn/articles/azure-china-migration-playbook/china-migration-guidance-storage
6.	配置CONTOSORG2新建的资源。
7.	为迁移后的资源配置灾难恢复和备份https://docs.azure.cn/zh-cn/site-recovery/azure-to-azure-quickstart。
### 关键产出
* 在新的区域部署的资源及配置的完整列表。 
*	新区域资源的灾难恢复和备份。


## 验收阶段
### 关键任务
在验证阶段，完成以下任务：
•	完成用户验收测试。
•	如果适用，将最新数据同步到目标环境。
•	确保应用程序按预期工作。
•	切换到目标区域中的新应用程序实例。
•	验证生产环境是否按预期工作。
•	取消配置源区域中的资源。
•	Infra Testing
•	Go Live decision
•	Roll back
•	Early life support
•	Performing Quality checks， UAT and Cutover collaboration with End-Customer

### 关键产出
	迅速过度
	决定上线
	交接
	月度/周度的运营报告



