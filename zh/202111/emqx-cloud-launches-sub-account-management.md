[EMQX Cloud](https://www.emqx.com/zh) 是 EMQ 推出的一款面向物联网领域的全托管云原生 MQTT 消息服务，可以通过可靠、实时的物联网数据移动、处理和集成，连接海量物联网设备，加快企业物联网应用开发，免除基础设施管理维护负担。

对于企业应用软件来说，权限设计是一个重要模块。各个企业千差万别的组织架构会产生不尽相同的业务流程，因此应用软件需要有完善的权限管理功能，可以实现不同用户登陆系统后，能看到不同的模块，操作不同的功能，看到不同范围的数据。否则在用户使用过程中，极易发生类似数据泄露、权限混乱等问题，不仅会影响到企业的管理流程，甚至还会带来无法预估的损失。

在上次的更新中，我们上线了[「多项目部署管理」功能](https://www.emqx.com/zh/blog/emqx-cloud-realizes-multi-project-deployment-management)。通过该功能对不同项目的部署进行区分管理，开启了实现全面权限管理的第一步。

近日，**「子账号管理」功能也正式上线**。该功能可支持企业内不同角色对账号进行相应权限的操作管理，使企业业务流程更加清晰、便于管理。

## **子账号管理功能使用场景**

### **生产和开发测试环境分类管理，互不干扰**

对于大部分企业客户而言，一款 SaaS 服务在正式应用到实际业务之前需要进行测试调通。对此，EMQX Cloud 开放了基础版 30 天和专业版 14 天的免费试用，为用户前期调研和了解产品提供方便，节省测试开发阶段需要的花费。

在部署正式投入生产环境后，企业业务变动仍然难以避免地会带来一些改动。这时如果贸然在运行中的部署上直接修改，很有可能带来不必要的损失。此时就可以采用 EMQX Cloud 的多项目管理及子账号管理功能。

通过**将开发测试环境和生产环境的部署区分开来**，分配至「生产环境」和「测试环境」两个项目中进行管理，可以防止修改错误。同时管理员还可以**赋予企业内部开发测试人员和业务人员不同项目的权限，避免越权操作。**

![创建生产环境项目](https://static.emqx.net/images/508f1324d300457d6480cc3534ef35f6.png)

创建生产环境项目

![关联子账号并赋予权限](https://static.emqx.net/images/6cbdc92352c942229ebb719e38c1f43a.png)

关联子账号并赋予权限

![角色确认](https://static.emqx.net/images/175dba8d3870251b04ff40bd416e9acd.png)

角色确认

 
### **财务审计自助查询，大幅提升工作效率**

财务管理是企业业务中不可缺少的一环，以往财务报销或是对账需要财务告知技术人员所需账单发票信息，再由技术人员登录账号进入财务模块下载财务所需文件。在这个环节中很有可因信息传达不畅而出现错误，进而导致不必要的重复操作，效率低下。

通过子账号管理功能，可以邀请企业财务人员成为账号的财务专员用户，登录进入控制台后，财务专员用户可查看（只读不可编辑）所有项目的部署情况，并且分别对不同项目的部署进行查账对账的操作。在整个过程中不用担心财务因误操作而导致部署状态/设置受到影响，**财务自主查询的效率提高，再也不用劳烦技术人员帮助查账。**

![4.png](https://static.emqx.net/images/a2def5e93e84f9149e67417330069792.png)

![财务审计自助查询](https://static.emqx.net/images/89c36cb84b085efab06913140aac01de.png)
 

## **如何使用子账号管理功能**

### **设定子账号角色**

首先对 EMQX Cloud 目前支持的几种账号角色进行解释，同时需要说明：一种角色可以分配给多个账号，同时一个账号也可以有多个角色，角色与账号是多对多的关系。

- **管理员用户**：拥有平台全部权限。
- **项目管理员用户**：拥有查看、修改指定项目的权限和修改、删除部署的权限。
- **项目使用者用户**：拥有查看指定项目的权限和查看、编辑部署的权限
- **财务专员用户**：可以查看项目和部署，拥有财务管理权限
- **审计专员用户**: 可以查看项目和部署，并可以查看子用户

在 [EMQ 官网](https://www.emqx.com/zh)注册后，您将默认成为该账号的管理员用户，您可以进入控制台，在右上角菜单栏找到「用户管理」，点击「新建用户」，输入您想要授权的子账号邮箱及密码，并赋予其不同角色（可选择多个角色）。

![新建用户](https://static.emqx.net/images/d7e0dd399f3a6371c0b4b64dc1f085ad.png)

之后子账号用户会在邮箱中收到对应的登陆链接，输入邮箱和密码后即可登录进行对应操作。

![邮件登录](https://static.emqx.net/images/5d89d1d9dcd9f4dc232b3b4855298463.png)

> 注：1 用于账号验证及首次登陆，2 用于后续子账号登陆。

### **将子账号与项目关联**

您可以在右上角的菜单中找到「项目中心」，这里您可以找到您创建的所有项目，默认项目对所有子账号开放，但可操作权限跟随角色变化而变化。

您自主创建的项目支持关联子账号。

![关联子账号](https://static.emqx.net/images/625374506e83fb04e72c0c56e0ba4858.png)

点击指定项目右上角 ![添加](https://static.emqx.net/images/287a7f2c82c4888de128ae0cea53e502.png)，并点击「添加」，输入账号关键词，会自动关联相关账号，可授予角色包括「项目管理员」和「项目使用者」。

![管理帐号](https://static.emqx.net/images/7dcd7b8bfe3198aa5c03b22a19ce6cdc.png)

![管理帐号](https://static.emqx.net/images/a3b4e00910798e5facf039e04ab2f397.png)


EMQX Cloud 团队致力于为用户提供轻松便捷的自动化全托管 [MQTT 云服务](https://www.emqx.com/zh/cloud)。我们将继续完善权限管理模块的功能开发，为用户带来更愉快的产品体验。
