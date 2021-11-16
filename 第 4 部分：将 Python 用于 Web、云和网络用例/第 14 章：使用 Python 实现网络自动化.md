传统上，网络由网络专家建设和运营，这仍然是电信行业的趋势。然而，这种管理和操作网络的手动方法速度缓慢，有时会因人为错误而导致代价高昂的网络中断。此外，要获得新服务（例如 Internet 服务），客户必须在提出新服务请求后等待数天才能准备好。根据智能手机和移动应用程序的体验，您只需单击一下按钮即可启用新服务和应用程序，客户希望在几分钟甚至几秒钟内即可准备好网络服务。这在当前的网络管理方法中是不可能的。传统方法有时也是电信服务提供商推出新产品和服务的障碍。
网络自动化可以通过提供用于自动化网络管理和操作方面的软件来改善这些情况。网络自动化有助于消除配置网络设备时的人为错误，并通过自动执行重复性任务来显着降低运营成本。网络自动化有助于加速服务交付并使电信服务提供商能够推出新服务。
Python 是网络自动化的流行选择。在本章中，我们将发现用于网络自动化的 Python 功能。 Python 提供了 Paramiko、Netmiko 和 NAPALM 等可用于与网络设备交互的库。如果网络设备由网络管理系统 (NMS) 或网络控制器/编排器管理，Python 可以使用 REST 或 RESTCONF 协议与这些平台进行交互。如果不监听网络中发生的实时事件，就不可能实现端到端的网络自动化。这些实时网络事件或实时流数据通常可通过 Apache Kafka 等系统获得。我们还将探索与使用 Python 的事件驱动系统的交互。
我们将在本章中讨论以下主题：

- 网络自动化简介
- 与网络设备交互
- 与网络管理系统集成
- 使用基于事件的系统

完成本章后，您将了解如何使用 Python 库从网络设备获取数据并将配置数据推送到这些设备。这些是任何网络自动化过程的基本步骤。

## 技术要求

以下是本章的技术要求：

- 您需要在计算机上安装 Python 3.7 或更高版本。
- 您需要在 Python 之上安装 Paramiko、Netmiko、NAPALM、ncclient 和 requests 库。
- 您需要使用 SSH 协议访问一台或多台网络设备。
- 您需要访问诺基亚开发人员实验室才能访问诺基亚的 NMS（称为网络服务平台 (NSP)）。

本章的示例代码可以在 https://github.com/PacktPublishing/Python-for-Geeks/tree/master/Chapter14 找到。

> **提示**
> 在本章中，您将需要访问物理或虚拟网络设备和网络管理系统来执行代码示例。这对每个人来说可能都不可能。您可以使用任何具有类似功能的网络设备。我们将更多地关注实现的 Python 端，并方便将代码重用于任何其他设备或管理系统。

我们将通过介绍网络自动化来开始我们的讨论。

## 网络自动化简介
网络自动化是使用技术和软件来自动化管理和操作网络的过程。网络自动化的关键词是自动化一个过程，这意味着它不仅是关于部署和配置网络，而且是实现网络自动化必须遵循的步骤。例如，有时，自动化步骤涉及在将配置推送到网络之前获得不同利益相关者的批准。自动化此类批准步骤是网络自动化的一部分。因此，基于每个组织遵循的内部流程，网络自动化流程可能因组织而异。这使得构建一个可以为许多客户立即执行自动化的单一平台变得具有挑战性。
有大量的持续努力来提供网络设备供应商提供的必要平台，这些平台可以帮助以最少的努力构建定制的自动化。此类平台的一些示例是 Cisco Network Services Orchestrator (NSO)、瞻博网络的 Paragon Automation 平台和诺基亚的 NSP。
这些自动化平台的挑战之一是它们通常被供应商锁定。这意味着供应商声称他们的平台也可以管理和自动化其他供应商的网络设备，但实现多供应商自动化的过程繁琐且成本高昂。因此，电信服务提供商正在寻求超越供应商平台的自动化。 Python 和 Ansible 是两种流行的编程语言，用于电信行业的自动化。在我们深入探讨 Python 如何实现网络自动化之前，让我们探讨一下网络自动化的一些优点和挑战。

## 网络自动化的优点和挑战

我们已经强调了网络自动化的一些优点。我们可以将主要优点总结如下：
加快服务交付：更快地向新客户交付服务使您能够尽早开始服务计费并获得更多满意的客户。
降低运营成本：通过自动化重复性任务和通过工具和闭环自动化平台监控网络，可以降低网络的运营成本。
消除幽默错误：大多数网络中断是由于人为错误。网络自动化可以通过使用标准模板配置网络来消除这个原因。这些模板在投入生产前都经过了深入的评估和测试。

- 一致的网络设置：当人工配置网络时，不可能遵循一致的模板和命名约定，这对于运营团队管理网络很重要。当我们每次使用相同的脚本或模板配置网络时，网络自动化带来了网络设置的一致性。
- 网络可见性：借助网络自动化工具和平台，我们可以访问性能监控功能，并可以端到端地可视化我们的网络。通过在流量高峰和资源利用率高的情况下检测它们导致网络流量瓶颈，主动网络管理成为可能。

网络自动化是数字化转型的必要条件，但要实现它有一些成本和挑战。这些挑战如下：

- 成本：在构建或定制用于网络自动化的软件时总是需要付出代价的。网络自动化是一个旅程，必须每年为其设定成本预算。
- 人力阻力：在许多组织中，人力资源人员将网络自动化视为对其工作的威胁，因此他们拒绝采用网络自动化，尤其是在运营团队中。
- 组织结构：当跨不同网络层和网络域（例如 IT 和网络域）使用网络自动化时，它会带来真正的投资回报 (ROI)。许多组织面临的挑战是，这些域由不同的部门拥有，每个部门都有自己的自动化策略和关于自动化平台的偏好。
- 选择自动化平台/工具：从 Cisco 或 Nokia 等网络设备供应商处选择自动化平台，或者与 HP 或 Accenture 等第三方自动化平台合作，并不是一个容易的决定。在很多情况下，电信服务提供商最终会与多家供应商合作来构建他们的网络自动化，这给这些供应商之间的合作带来了一系列新的挑战。
- 维护：维护自动化工具和脚本与构建它们一样重要。这需要从自动化供应商那里购买必要的维护合同，或者设置一个内部团队来为此类自动化平台提供维护。

接下来，我们看一下用例。

## 用例

一些关于网络管理的单调任务可以使用 Python 或其他工具自动化。但真正的好处是自动化那些重复的、容易出错的或手动完成的乏味的任务。从电信服务提供商的角度来看，以下是网络自动化的主要应用：

- 我们可以自动化网络设备的日常配置，例如创建新的 IP 接口和网络连接服务。手动执行这些任务非常耗时。
- 我们可以配置防火墙规则和策略以节省时间。创建防火墙规则配置是一项乏味的活动，任何错误都可能导致在解决通信挑战方面浪费时间。
- 当我们在网络中有数千台设备时，升级它们的软件是一个巨大的挑战，有时需要 1 到 2 年才能实现。网络自动化可以加快此活动并方便地执行升级前和升级后检查以实现无缝升级。
- 我们可以使用网络自动化在网络中加入新的网络设备。如果设备要安装在客户的场所，我们可以通过自动化设备的入职流程来节省上门服务。此入职流程也称为零接触配置 (ZTP)。

现在我们已经介绍了网络自动化，让我们探索如何使用不同的协议与网络设备进行交互。

## 与网络设备交互

Python 是网络自动化的流行选择，因为它易于学习并且可以用于直接与网络设备集成，也可以通过 NMS 集成。事实上，许多供应商，例如诺基亚和思科，都在其网络设备上支持 Python 运行时。设备上 Python 运行时的选项对于在单个设备的上下文中自动化任务和活动非常有用。在本节中，我们将重点介绍设备外 Python 运行时选项。此选项将使我们能够灵活地同时使用多个设备。

> **提示**
> 对于本节提供的所有代码示例，我们将使用 Cisco 的虚拟网络设备（IOS XR 7.1.2 版）。为了与 NMS 集成，我们将使用诺基亚 NSP 系统。

在使用 Python 以便与网络设备进行交互之前，我们将讨论可用于与网络设备进行通信的协议。

## 与网络设备交互的协议

在直接与网络设备通信时，我们可以使用多种协议，例如安全外壳协议 (SSH)、简单网络管理协议 (SNMP) 和网络配置 (NETCONF)。其中一些协议相互叠加。下面将介绍最常用的协议。

### SSH

SSH 是一种网络协议，用于在任何两个设备或计算机之间安全地进行通信。两个实体之间的所有信息在发送到传输通道之前都将被加密。我们通常使用 SSH 客户端通过 ssh 命令连接到网络设备。 SSH 客户端通过 ssh 命令使用已登录操作系统用户的用户名：
ssh <服务器IP或主机名>
要使用登录用户以外的其他用户，我们可以指定用户名，如下所示：
ssh 用户名@<服务器 IP 或主机名>
建立 SSH 连接后，我们可以发送 CLI 命令以从设备检索配置或操作信息或配置设备。 SSH 版本 2 (SSHv2) 是与设备交互以进行网络管理甚至自动化目的的流行选择。
我们将在使用基于 SSH 的协议与网络设备交互部分中讨论如何将 SSH 协议与 Python 库（例如 Paramiko、Netmiko 和 NAPALM）一起使用。 SSH 还是许多高级网络管理协议（例如 NETCONF）的基础传输协议。

### SNMP

30 多年来，该协议一直是网络管理的事实上的标准，并且仍然大量用于网络管理。但是，它正在被更高级和可扩展的协议取代，例如 NETCONF 和 gNMI。 SNMP 既可用于网络配置，也可用于网络监控，但它更流行用于网络监控。在当今世界，它被认为是 1980 年代后期引入的传统协议，纯粹用于网络管理。
SNMP 协议依赖于管理信息库 (MIB)，它是一种设备模型。该模型是使用称为管理信息结构 (SMI) 的数据建模语言构建的。

### 网络配置文件

由 Internet 工程任务组 (IETF) 引入的 NETCONF 协议被认为是 SNMP 的继承者。 NETCONF 主要用于配置网络设备，预计所有新的网络设备都会支持。 NETCONF 基于四层：

- 内容：这是一个依赖于 YANG 建模的数据层。每个设备都为其提供的各种模块提供多种 YANG 模型。这些模型可以在 https://github.com/YangModels/yang 上探索。
- 操作：NETCONF 操作是从 NETCONF 客户端发送到 NETCONF 服务器（也称为 NETCONF 代理）的操作或指令。这些操作包含在请求和回复消息中。 NETCONF 操作的示例是 get、get-config、edit-config 和 delete-config。

- 消息：这些是在 NETCONF 客户端和 NETCONF 代理之间交换的远程过程调用 (RPC) 消息。 NETCONF 操作和编码为 XML 的数据包装在 RPC 消息中。
- 传输层：该层提供客户端和服务器之间的通信路径。 NETCONF 消息可以使用 NETCONF over SSH 或 NETCONF over TLS 和 SSL 证书选项。

NETCONF 协议基于使用端口 830 作为默认端口通过 SSH 协议交换的 XML 消息。通常有两种类型的配置数据库由网络设备管理。第一种称为运行数据库，它代表设备上的活动配置，包括运行数据。这是每个设备的必需数据库。第二种类型称为候选数据库，它代表候选配置可以推送到正在运行的数据库之前。当存在候选数据库时，不允许直接对正在运行的数据库进行配置更改。
我们将在使用 NETCONF 与网络设备交互部分讨论如何使用 Python 使用 NETCONF。

### RESTCONF
RESTCONF 是另一个 IETF 标准，它使用 RESTful 接口提供 NETCONF 功能的子集。 RESTCONF 不使用带有 XML 编码的 NETCONF RPC 调用，而是提供基于 HTTP/HTTPS 的 REST 调用，并可选择使用 XML 或 JSON 消息。如果网络设备提供 RESTCONF 接口，我们可以使用 HTTP 方法（GET、PATCH、PUT、POST 和 DELETE）进行网络管理。当 RESTCONF 用于网络自动化时，我们必须了解它通过 HTTP/HTTPS 提供有限的 NETCONF 功能。 RESTCONF 不支持 NETCONF 操作，例如提交、回滚和配置锁定。
gRPC/gNMI
gNMI 是 gRPC 网络管理接口 (NMI)。 gRPC 是一种远程过程调用，由 Google 开发，用于低延迟和高度可扩展的数据检索。 gRPC 协议最初是为希望与具有严格延迟要求的云服务器通信的移动客户端开发的。 gRPC 协议对于通过协议缓冲区（Protobufs）传输结构化数据非常高效，这是该协议的关键组件。通过使用 Protobufs，数据以二进制格式而不是文本格式（如 JSON 或 XML）打包。与 JSON 或 XML 相比，这种格式不仅减少了数据的大小，而且在序列化和反序列化数据方面非常有效。此外，数据是使用 HTTP/2 而不是 HTTP 1.1 传输的。 HTTP/2 提供了请求-响应模型和双向通信模型。这种双向通信模型使客户端可以打开长期连接，从而显着加快数据传输过程。这两项技术使 gRPC 协议比 REST API 快 7 到 10 倍。
gNMI 是 gRPC 协议的特定实现，用于网络管理目的和遥测应用程序。它也是一种类似于 NETCONF 的 YANG 模型驱动协议，与 NETCONF 相比，它提供的操作很少。这些操作包括获取、设置和订阅。 gNMI 在遥测数据收集方面比在网络管理方面更受欢迎。这样做的主要原因是它在网络配置方面没有 NETCONG 提供那么多的灵活性，但在从远程系统收集数据时，它是一种优化的协议，尤其是实时或近实时的。
接下来，我们将讨论用于与网络设备交互的 Python 库。

## 使用基于 SSH 的 Python 库与网络设备交互

有几个 Python 库可用于使用 SSH 与网络设备进行交互。 Paramiko、Netmiko 和 NAPALM 是三个可用的流行库，我们将在下一小节中探讨它们。我们将从Paramiko开始。
帕拉米科
Paramiko 库是 Python 中 SSH v2 协议的抽象，包括服务器端和客户端功能。我们将在这里只关注 Paramiko 库的客户端功能。
当我们与网络设备交互时，我们要么尝试获取配置数据，要么为某些对象推送新配置。前者是通过显示类型的 CLI 命令实现的，根据设备的操作系统，而后者可能需要特殊模式来执行配置 CLI 命令。通过 Python 库工作时，这两种类型的命令的处理方式不同。
获取设备配置
要使用 Paramiko 库连接到网络设备（作为 SSH 服务器侦听），我们必须使用 paramiko.SSHClient 类的实例或直接使用低级 paramiko.Transport 类。 Transport 类提供低级方法，可以直接控制基于套接字的通信。 SSHClient 类是一个包装类，并使用底层的 Transport 类来管理会话，并在网络设备上实现了 SSH 服务器。
我们可以使用 Paramiko 库来建立与网络设备（在我们的例子中是 Cisco IOS XR）的连接并运行 show 命令（在我们的例子中是 show ip int brief），如下所示：

```python
#show_cisco_int_pmk.py
import paramiko
host='HOST_ID'
port=22
username='xxx'
password='xxxxxx'
#cisco ios command to get a list of IP interfaces
cmd= 'show ip int brief \n'
def main():
    try:
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        ssh.connect(host, port, username, password)
        stdin, stdout, stderr = ssh.exec_command(cmd)
        output_lines = stdout.readlines()
        response = ''.join(output_lines)
        print(response)
    finally:
        ssh.close()
if __name__ == '__main__':
    main()
```

此代码示例的要点如下：
我们创建了一个 SSHClient 实例并打开了与 SSH 服务器的连接。
由于我们没有将主机密钥用于 SSH 连接，因此我们应用了 set_missing_host_key_policy 方法来避免任何警告或错误。
建立 SSH 连接后，我们使用 SSH 传输将 show 命令 show ip int brief 发送到主机，并接收该命令的输出作为 SSH 回复。
该程序的输出是 stdin、stdout 和 stderr 对象的元组。如果我们的命令成功执行，我们将从 stdout 对象中检索输出。
该程序在 Cisco IOS XR 设备上执行时的输出如下：
Mon Jul 19 12:03:41.631 UTC
Interface                   IP-Address      Status    Protocol
Loopback0                   10.180.180.10   Up        Up
GigabitEthernet0/0/0/0      10.1.10.2       Up        Up
GigabitEthernet0/0/0/0.100  unassigned      Up        Down
GigabitEthernet0/0/0/1      unassigned      Up        Up
GigabitEthernet0/0/0/1.100  150.150.150.1   Up        Up
GigabitEthernet0/0/0/2      unassigned      Shutdown  Down
如果您在其他设备类型上运行此程序，则必须根据您的设备类型更改已设置为 cmd 变量的命令。
Paramiko 库提供对网络通信的低级控制，但由于许多网络设备对 SSH 协议的非标准或不完整实现，它有时可能很古怪。如果您在将 Paramiko 与某些网络设备一起使用时遇到挑战，那不是您或 Paramiko，而是设备希望您与之通信的方式。低级传输通道可以解决这些问题，但这需要一些复杂的编程。 Netmiko 来拯救这里。

### Netmiko

Netmiko 是一个抽象的网络管理库，它建立在 Paramiko 库之上。它通过区别对待每个网络设备来消除 Paramiko 的挑战。 Netmiko 在幕后使用 Paramiko 并隐藏了许多设备级通信细节。 Netmiko 支持来自不同供应商的多种设备，例如 Cisco、Arista、Juniper 和诺基亚。
获取设备配置
要使用 CLI 命令的 show type 连接到网络设备，我们必须设置用于连接目标网络设备的 device_type 定义。这个 device_type 定义是一个字典，必须包括设备的类型、主机 IP 或设备的完全限定域名 (FQDN)、用户名和密码才能与设备连接。如果目标机器正在侦听 22 以外的端口，我们可以设置 SSH 连接的端口号。 以下代码可用于执行我们使用 Paramiko 库执行的相同 show 命令：

```python
#show_cisco_int_nmk.py
from netmiko import ConnectHandler
cisco_rtr = {
    "device_type": "cisco_ios",
    "host": "HOST_ID",
    "username": "xxx",
    "password": "xxxxxxx",
    #"global_delay_factor": 2,
}
def main():
    command = "show ip int brief"
    with ConnectHandler(**cisco_rtr) as net_connect:
        print(net_connect.find_prompt())
        print(net_connect.enable())
        output = net_connect.send_command(command)
    print(output)
```

本示例代码的要点如下：

- 我们使用上下文管理器使用 ConnectHandler 类创建了一个网络连接。上下文管理器将管理连接的生命周期。
- Netmiko 提供了一个名为 find_prompt 的简单方法来抓取目标设备的提示，这对于解析许多网络设备的输出很有用。 Cisco IOS XR 网络设备不需要这样做，但我们将其用作最佳实践。
- Netmiko 还允许我们使用 enable 方法为 Cisco IOS 设备进入启用模式（它是一个命令行提示符，#）。同样，这在本示例中不是必需的，但使用它是最佳实践，尤其是在我们将 CLI 命令作为同一编程脚本的一部分推送进行配置的情况下。
- 我们使用 send_command 方法执行了 show ip int brief 命令，并获得了与 show_cisco_int_pmk.py 程序相同的输出。

根据我们为同一个 show 命令共享的代码示例，我们可以得出结论，与 Paramiko 相比，使用 Netmiko 更方便。

> **提示**
> 设置正确的设备类型以获得一致的结果非常重要，即使您使用的是来自同一供应商的设备。这在使用命令配置设备时尤其重要。不正确的设备类型会产生不一致的错误。

有时，我们执行的命令比正常的 show 命令需要更多时间来完成。例如，我们可能希望将文件从设备上的一个位置复制到另一个位置，我们知道这对于一个大文件可能需要几百秒。默认情况下，Netmiko 等待近 100 秒来完成命令。我们可以通过添加如下一行来添加全局延迟因子作为设备定义的一部分：
```“global_delay_factor”：2```
这将使该设备的所有命令的等待时间增加 2 倍。或者，我们可以使用 send_command 方法通过传递以下参数来设置单个命令的延迟系数：
延迟因子=2
当我们预计执行时间很长时，我们应该添加一个延迟因子。当我们必须添加延迟因子时，我们还应该添加另一个属性作为 send_command 方法的参数，如果我们看到命令提示符（例如，在 Cisco IOS 设备的情况下为 #），它将提前中断等待周期.这可以通过使用以下属性来设置：

```expect_string=r'#'```

### 配置网络设备
在下面的代码示例中，我们将提供一些用于配置目的的示例代码。 使用 Netmiko 配置设备类似于执行 show 命令，因为 Netmiko 将负责启用配置终端（如果需要，根据设备类型）并优雅地退出配置终端。
对于我们的代码示例，我们将使用 Netmiko 和以下程序设置接口描述：

```python
#config_cisco_int_nmk.py
from netmiko import ConnectHandler
cisco_rtr = {
    "device_type": "cisco_ios",
    "host": "HOST_ID",
    "username": "xxx",
    "password": "xxxxxx",
}
def main():
    commands = ["int Lo0 "description custom_description", "commit"]
    with ConnectHandler(**cisco_rtr) as net_connect:
        output = net_connect.send_config_set(commands)
    print(output)
    print()
```

此代码示例的要点如下：
对于这个程序，我们创建了一个包含三个命令的列表（int <interface id>、description <new description> 和 commit）。前两个命令也可以作为单个命令发送，但为了便于说明，我们将它们分开。 commit 命令用于保存更改。
当我们向设备发送命令进行配置时，我们使用 Netmiko 库中的 send_config_set 方法来建立连接以进行配置。成功执行此步骤取决于设备类型的正确设置。这是因为配置命令的设备行为因设备而异。
这三个命令集将添加或更新指定接口的描述属性。
除了设备配置提示使用我们的命令外，该程序不会有任何特殊输出。控制台输出将如下所示：
Mon Jul 19 13:21:16.904 UTC
RP/0/RP0/CPU0:cisco(config)#int Lo0
RP/0/RP0/CPU0:cisco(config-if)#description custom_description
RP/0/RP0/CPU0:cisco(config-if)#commit
Mon Jul 19 13:21:17.332 UTC
RP/0/RP0/CPU0:cisco(config-if)#
Netmiko 提供了更多功能，但我们将通过阅读其官方文档 (https://pypi.org/project/netmiko/) 将其留给您探索。我们在本节中讨论的代码示例已经在 Cisco 网络设备上进行了测试，但是如果 Netmiko 支持该设备，则可以通过更改设备类型和任何其他设备的命令来使用相同的程序。
Netmiko 简化了我们用于网络设备交互的代码，但我们仍在运行 CLI 命令来获取设备配置或将配置推送到设备。 Netmiko 不容易实现可编程性，但另一个名为 NAPALM 的库可以提供帮助。

### NAPALM

NAPALM 是 Network Automation and Programmability Abstraction Layer with Multivendor 的首字母缩写词。该库通过提供一组函数作为与多个网络设备交互的统一 API，在 Netmiko 之上提供了下一个抽象级别。它不支持与 Netmiko 一样多的设备。对于 NAPALM 的第 3 版，核心驱动程序可用于 Arista EOS、Cisco IOS、Cisco IOS-XR、Cisco NX-OS 和 Juniper JunOS 网络设备。但是，有几个社区构建的驱动程序可用于与许多其他设备进行通信，例如诺基亚 SROS、Aruba AOS-CX 和 Ciena SAOS。
正如我们为 Netmiko 所做的那样，我们将构建与网络设备交互的 NAPALM 示例。在第一个示例中，我们将获得 IP 接口列表，而对于第二个示例，我们将添加或更新 IP 接口的描述属性。这两个代码示例将执行我们使用 Paramiko 和 Netmiko 库执行的相同操作。

### 获取设备配置

要获取设备配置，我们必须建立与网络设备的连接。我们将在两个代码示例中执行此操作。建立连接是一个三步过程，如下所述：
要建立连接，我们必须根据支持的设备类型获取设备驱动程序类。这可以使用 NAPALM 库的 get_network_driver 函数来实现。
一旦我们有了设备驱动程序类，我们就可以通过向驱动程序类构造函数提供诸如主机 ID、用户名和密码之类的参数来创建设备对象。
下一步是使用设备对象的 open 方法连接到设备。所有这些步骤都可以用 Python 代码实现，如下所示：

```python
from napalm import get_network_driver
driver = get_network_driver('iosxr')
device = driver('HOST_ID', 'xxxx', 'xxxx')
device.open()
```

一旦设备的连接可用，我们就可以调用 get_interfaces_ip（相当于 show interfaces CLI 命令）或 get_facts（相当于 show version CLI 命令）等方法。 使用这两种方法的完整代码如下：

```python
#show_cisco_int_npm.py
from napalm import get_network_driver
import json
def main():
    driver = get_network_driver('iosxr')
    device = driver('HOST_ID', 'root', 'rootroot')
    try:
        device.open()
        print(json.dumps(device.get_interfaces_ip(), indent=2))
        #print(json.dumps(device.get_facts(), indent=2))
    finally:
        device.close()
```


最有趣的事实是，该程序的输出默认为 JSON 格式。 默认情况下，NAPALM 将 CLI 命令的输出转换为易于在 Python 中使用的字典。 此处显示了上一代码示例的输出摘录：

```json
{
  "Loopback0": {
    "ipv4": {
      "10.180.180.180": {
        "prefix_length": 32
      }
    }
  },
  "MgmtEth0/RP0/CPU0/0": {
    "ipv4": {
      "172.16.2.12": {
        "prefix_length": 24
      }
    }
  }
}
```

### 配置网络设备
在以下代码示例中，我们使用 NAPALM 库来添加或更新现有 IP 接口的描述属性：

```python
#config_cisco_int_npm.py
from napalm import get_network_driver
import json
def main():
    driver = get_network_driver('iosxr')
    device = driver('HOST_ID', 'xxx', 'xxxx')
    try:
        device.open()
        device.load_merge_candidate(config='interface Lo0 \n            description napalm_desc \n end\n')
        print(device.compare_config())
        device.commit_config()
    finally:
        device.close()
```

此代码示例的要点如下：
要配置 IP 接口，我们必须使用 load_merge_candidate 方法并将相同的 CLI 命令集传递给该方法，就像我们使用 Netmiko 配置接口时所做的那样。
接下来，我们使用 compare_config 方法比较了命令之前和命令之后的配置。 这表明添加了哪些新配置以及删除了哪些配置。
我们使用 commit_config 方法将提交应用于所有更改。
对于此示例代码，输出将显示更改的增量，如下所示：

```
---
+++
@@ -47,7 +47,7 @@
  !
!
interface Loopback0
- description my custom description
+ description napalm added new desc
  ipv4 address 10.180.180.180 255.255.255.255
!
interface MgmtEth0/RP0/CPU0/0
```

这里，以 - 开头的行是要删除的配置；任何以 + 开头的行都是要添加的新配置。
通过这两个代码示例，我们向您展示了一种设备类型的一组基本 NAPALM 功能。该库可用于一次配置多个设备，并可以使用不同的配置集。
在下一节中，我们将讨论使用 NETCONF 协议与网络设备进行交互。

### 使用 NETCONF 与网络设备交互

NETCONF 是为模型（对象）驱动的网络管理而创建的，特别是用于网络配置。在使用 NETCONF 处理网络设备时，了解设备的两个功能很重要，如下所示：
您可以了解您拥有的设备的 YANG 模型。如果您希望以正确的格式发送消息，拥有这些知识很重要。这是来自各个供应商的 YANG 模型的绝佳来源：https://github.com/YangModels/yang。
您可以为您的网络设备在网络设备上为 NETCONF 协议启用 NETCONF 和 SSH 端口。在我们的例子中，我们将使用 Cisco IOS XR 的虚拟设备，就像我们在前面的代码示例中所做的那样。
在开始任何与网络管理相关的活动之前，我们必须检查设备的 NETCONF 功能和 NETCONF 数据源配置的详细信息。对于本节中的所有代码示例，我们将使用 Python 的 NETCONF 客户端库，称为 ncclient。该库提供了发送 NETCONF RPC 请求的便捷方法。我们可以使用 ncclient 库编写示例 Python 程序来获取设备的功能和设备的完整配置，如下所示：

```python
#check_cisco_device.py
from ncclient import manager
with manager.connect(host='device_ip, username=xxxx, password=xxxxxx, hostkey_verify=False) as conn:
   capabilities = []
   for capability in conn.server_capabilities:
      capabilities.append(capability)
   capabilities = sorted(capabilities)
   for cap in capabilities:
     print(cap)
   result = conn.get_config(source="running")
   print (result)
```

来自 ncclient 库的管理器对象用于使用 SSH 连接到设备，但用于 NETCONF 端口 830（默认）。首先，我们通过连接实例得到一个服务器能力列表，然后为了方便阅读，以排序的格式打印出来。在此代码示例的下一部分中，我们使用管理器类库的 get_config 方法启动了 get-config NETCONF 操作。该程序的输出很长，显示了所有功能和设备配置。我们让您来探索输出并熟悉您设备的功能。
重要的是要理解本节的范围不是解释 NETCONF，而是学习如何使用 Python 和 ncclient 与 NETCONF 一起工作。为了实现这个目标，我们将编写两个代码示例：一个用于获取设备接口的配置，另一个用于更新接口的描述，这与我们为之前的 Python 库所做的相同。

### 通过 NETCONF 获取接口

在上一节中，我们了解到我们的设备 (Cisco IOS XR) 通过使用 OpenConfig 实现支持接口，该实现可从 http://openconfig.net/yang/interfaces?module=openconfig-interfaces 获得。
我们还可以检查接口配置的 XML 格式，这是我们作为 get_config 方法的输出收到的。在此代码示例中，我们将简单地将带有接口配置的 XML 过滤器作为参数传递给 get_config 方法，如下所示：

```python
#show_all_interfaces.py
from ncclient import manager
with manager.connect(host='device_ip', username=xxx,                password='xxxx', hostkey_verify=False) as conn:
    result = conn.get_config("running", filter=('subtree',
    '<interfaces xmlns= "http://openconfig.net/yang/      interfaces"/>'))
    print (result)
The output of this program is a list of interfaces. We will only show an excerpt of the output here for illustration purposes:
<rpc-reply message-id="urn:uuid:f4553429-ede6-4c79-aeea-5739993cacf4" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
<data>
  <interfaces xmlns="http://openconfig.net/yang/interfaces">
   <interface>
    <name>Loopback0</name>
    <config>
     <name>Loopback0</name>
     <description>Configured by NETCONF</description>
    </config>
<!—rest of the output is skipped -->
```

为了获得一组有选择的接口，我们将使用基于接口 YANG 模型的 XML 过滤器的扩展版本。 对于下面的代码示例，我们将定义一个 XML 过滤器，并将接口的名称属性作为我们的过滤条件。 由于这个 XML 过滤器不止一行，我们将它单独定义

为一个字符串对象。 这是带有 XML 过滤器的示例代码：

```xml
#show_int_config.py
from ncclient import manager
# Create filter template for an interface
filter_temp = """
<filter>
    <interfaces xmlns="http://openconfig.net/yang/interfaces">
        <interface>
            <name>{int_name}</name>
        </interface>
    </interfaces>
</filter>"""
with manager.connect(host='device_ip', username=xxx,                password='xxxx', hostkey_verify=False) as conn:
    filter = filter_temp.format(int_name = "MgmtEth0/RP0/      CPU0/0")
    result = m.get_config("running", filter)
    print (result)
The output of this program will be a single interface (as per the configuration in our device) and look as follows:
<?xml version="1.0"?>
<rpc-reply message-id="urn:uuid:c61588b3-1bfb-4aa4-a9de-2a98727e1e15" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
<data>
  <interfaces xmlns="http://openconfig.net/yang/interfaces">
   <interface>
    <name>MgmtEth0/RP0/CPU0/0</name>
    <config>
     <name>MgmtEth0/RP0/CPU0/0</name>
    </config>
    <ethernet xmlns="http://openconfig.net/yang/interfaces/      ethernet">
     <config>
      <auto-negotiate>false</auto-negotiate>
     </config>
    </ethernet>
    <subinterfaces>
     <@!— ommitted sub interfaces details to save space -->
    </subinterfaces>
   </interface>
  </interfaces>
</data>
</rpc-reply>
```

我们也可以在 XML 文件中定义 XML 过滤器，然后将文件的内容读入 Python 程序中的字符串对象。 如果我们计划广泛使用过滤器，另一种选择是使用 Jinja 模板。
接下来，我们将讨论如何更新接口的描述。
更新界面描述
要配置描述等接口属性，我们必须使用 http://cisco.com/ns/yang/Cisco-IOS-XR-ifmgr-cfg 上提供的 YANG 模型。
此外，用于配置接口的 XML 块与我们用于获取接口配置的 XML 块不同。 为了更新接口，我们必须使用以下模板，我们在单独的文件中定义了它：

```xml
<!--config-template.xml-->
<config xmlns:xc="urn:ietf:params:xml:ns:netconf:base:1.0">
<interface-configurations xmlns="http://cisco.com/ns/yang/  Cisco-IOS-XR-ifmgr-cfg">
   <interface-configuration>
    <active>act</active>
    <interface-name>{int_name}</interface-name>
    <description>{int_desc}</description>
   </interface-configuration>
</interface-configurations>
</config>
```

在此模板中，我们为界面的名称和描述属性设置占位符。 接下来，我们将编写一个 Python 程序，该程序将读取此模板并使用 ncclient 库的 edit_config 方法调用 edit-config NETCONF 操作。 这会将模板推送到设备的候选数据库：

```python
#config_cisco_int_desc.py
from ncclient import manager
nc_template = open("config-template.xml").read()
nc_payload = nc_template.format(int_name='Loopback0',                          int_desc="Configured by NETCONF")
with manager.connect(host='device_ip, username=xxxx,                     password=xxx, hostkey_verify=False) as nc:
    netconf_reply = nc.edit_config(nc_payload,       target="candidate")
    print(netconf_reply)
    reply = nc.commit()
    print(reply)
```


在这里强调两件事很重要。 首先，Cisco IOS XR 设备已配置为仅通过候选数据库接受新配置。 如果我们尝试将目标属性设置为运行，它将失败。 其次，我们必须在同一会话中的 edit-config 操作之后调用 commit 方法，以使新配置可操作。 这个程序的输出将是来自NETCONF服务器的两个OK回复，如下：

```xml
<?xml version="1.0"?>
<rpc-reply message-id="urn:uuid:6d70d758-6a8e-407d-8cb8-10f500e9f297" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
<ok/>
</rpc-reply>
<?xml version="1.0"?>
<rpc-reply message-id="urn:uuid:2a97916b-db5f-427d-9553-de1b56417d89" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
<ok/>
</rpc-reply>
```

我们对使用 Python 进行 NETCONF 操作的讨论到此结束。我们使用 ncclient 库介绍了 NETCONF 的两个主要操作（get-config 和 edit-config）。
在下一节中，我们将研究使用 Python 与网络管理系统集成。

## 与网络管理系统集成

网络管理系统或网络控制器是提供具有图形用户界面 (GUI) 的网络管理应用程序的系统。这些系统包括网络库存、网络供应、故障管理和网络设备调解等应用程序。这些系统使用通信协议的组合与网络设备进行通信，例如用于网络配置的 SSH/NETCONF、用于警报和设备监控的 SNMP 以及用于遥测数据收集的 gRPC。这些系统还通过其脚本和工作流引擎### 提供自动化功能。

这些系统最有价值的方面是它们将网络设备的功能聚合到一个系统（本身）中，然后通过其北向接口 (NBI) 提供，这些接口通常是 REST 或 RESTCONF 接口。这些系统还通过基于事件的系统（例如 Apache Kafka）提供实时事件通知，例如警报。在本节中，我们将讨论使用 NMS 的 REST API 的几个示例。我们将在与事件驱动系统集成部分探讨如何使用 Python 与 Apache Kafka 集成。
为了使用 NMS，我们将使用诺基亚在线开发人员门户 (https://network.developer.nokia.com/) 提供的共享实验室。这个实验室有几个诺基亚 IP 路由器和一个 NSP。这个共享实验室在有限的时间内免费提供（在编写本书时每天 3 小时）。您将需要在开发者门户中免费创建一个帐户。当您预订实验室使用时，您将收到一封电子邮件，其中包含有关如何连接到实验室的说明以及必要的 VPN 详细信息。如果您是一名网络工程师并且您可以访问任何其他 NMS 或控制器，则可以通过进行适当的调整来将该系统用于本节中的练习。
要使用诺基亚 NSP 的 REST API，我们需要与 REST API 网关交互，该网关管理诺基亚 NSP 的多个 API 端点。我们可以通过使用位置服务开始使用 REST API 网关，如下所述。

### 使用位置服务端点

为了了解哪些 API 端点可用，诺基亚 NSP 提供了一个位置服务端点，该端点提供了所有 API 端点的列表。为了使用本节中的任何 REST API，我们将使用 Python 中的请求库。 requests 库以使用 HTTP 协议向服务器发送 HTML 请求而闻名，我们在前面的章节中已经使用过它。要从诺基亚 NSP 系统获取 API 端点列表，我们将使用以下 Python 代码来调用位置服务 API：

```python
#location_services1.py
import requests
payload = {}
headers = {}
url = "https://<NSP URL>/rest-gateway/rest/api/v1/location/  services"
resp = requests.request("GET", url, headers=headers,   data=payload)
print(resp.text)
```

此 API 响应将为您提供几十个 JSON 格式的 API 端点。 您可以在 https://network.developer.nokia.com/api-documentation/ 查看诺基亚 NSP 的在线文档，以了解每个 API 的工作原理。 如果我们正在寻找特定的 API 端点，我们可以更改上述代码示例中 url 变量的值，如下所示：
url = "https://<NSP URL>/rest-gateway/rest/api/v1/location/services/endpoints?endPoint=/v1/auth/token
通过使用这个新的 API URL，我们试图为授权令牌 (/v1/auth/token) 找到一个 API 端点。 带有这个新 URL 的代码示例的输出如下：

```json
{
 "response": {
  "status": 0,
  "startRow": 0,
  "endRow": 0,
  "totalRows": 1,
  "data": {
   "endpoints": [
    {
    "docUrl":"https://<NSP_URL>/rest-gateway/api-docs#!/      authent..",
    "effectiveUrl": "https://<NSP_URL>/rest-gateway/rest/api",
    "operation": "[POST]"
    }
   ]
  },
  "errors": null
 }
}
```

请注意，使用位置服务 API 不需要身份验证。 但是，我们需要一个身份验证令牌来调用任何其他 API。 在下一节中，我们将学习如何获取身份验证令牌。

### 获取身份验证令牌

作为下一步，我们将使用上一代码示例输出中的 EffectiveUrl 来获取身份验证令牌。 这个 API 要求我们将用户名和密码的 base64 编码作为 HTTP 头的 Authorization 属性传递。 调用这个认证API的Python代码如下：

```python
#get_token.py
import requests
from base64 import b64encode
import json
#getting base64 encoding
message = 'username'+ ':' +'password'
message_bytes = message.encode('UTF-8')
basic_token = b64encode(message_bytes)
payload = json.dumps({
  "grant_type": "client_credentials"
})
headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Basic {}'.format(str(basic_token,'UTF-8'))
}
url = "https://<NSP SERVER URL>/rest-gateway/rest/api/v1/auth/  token"
resp = requests.request("POST", url, headers=headers,   data=payload)
token = resp.json()["access_token"]
print(resp)
When executing this Python code, we will get a token for one   hour to be used for any NSP API.
{
  "access_token": "VEtOLVNBTXFhZDQ3MzE5ZjQtNWUxZjQ0YjNl",
  "refresh_token": "UkVUS04tU0FNcWF5ZlMTmQ0ZTA5MDNlOTY=",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

还有一个刷新令牌可用，可用于在令牌过期之前刷新令牌。 最佳做法是每 30 分钟刷新一次您的令牌。 我们可以使用相同的身份验证令牌 API 刷新我们的令牌，但在 HTTP 请求的正文中发送以下属性：

```python
payload = json.dumps({
  "grant_type": "refresh_token",
  "refresh_token": "UkVUS04tU0FNcWF5ZlMTmQ0ZTA5MDNlOTY="
})
```

另一个好的做法是在不再需要令牌时撤销令牌。 这可以通过使用以下 API 端点来实现：
url = "https://<NSP URL>rest-gateway/rest/api/v1/auth/ 撤销"

### 获取网络设备和接口清单

收到身份验证令牌后，我们可以使用 REST API 获取配置数据，以及添加新配置。 我们将从一个简单的代码示例开始，该示例将获取由 NSP 管理的网络中所有网络设备的列表。 在此代码示例中，我们将使用已通过令牌 API 检索到的令牌：

```python
#get_network_devices.py
import requests
pload={}
headers = {
  'Authorization': 'Bearer {token}'.format(token)
}
url = "https://{{NSP_URL}}:8544/NetworkSupervision/rest/api/v1/  networkElements"
response = requests.request("GET", url, headers=headers,   data=pload)
print(response.text)
```

该程序的输出将是具有网络设备属性的网络设备列表。由于这是一个大数据集，我们跳过了显示输出。
在下面的代码示例中，我们将展示如何基于过滤器获取设备端口（接口）列表。请注意，我们也可以将过滤器应用于网络设备。对于此代码示例，我们将要求 NSP API 根据端口名称（在我们的示例中为端口 1/1/1）为我们提供端口列表：

```python
#get_ports_filter.py
import requests
payload={}
headers = {
  'Authorization': 'Bearer {token}'.format(token)
}
url = "https://{{server}}:8544/NetworkSupervision/rest/api/v1/  ports?filter=(name='Port 1/1/1')
response = requests.request("GET", url, headers=headers,   data=payload)
print(response.text)
```

该程序的输出将是来自所有网络设备的称为端口 1/1/1 的设备端口列表。使用单个 API 获取跨多个网络设备的端口是使用 NMS 的真正价值
接下来，我们将讨论如何使用 NMS API 更新网络资源。

### 更新网络设备端口

使用 NMS API 时，创建新对象或更新现有对象也很方便。我们将实现一个更新端口描述的案例，就像我们在前面的代码示例中所做的那样，使用 Netmiko、NAPALM 和 ncclient。要更新端口或接口，我们将使用可从数据包网络功能管理器 (NFMP) 模块获得的不同 API 端点。 NFMP 是诺基亚 NSP 平台下诺基亚网络设备的 NMS 模块。让我们看看更新端口描述或对网络资源进行任何更改的步骤：
要更新对象或在现有对象下创建新对象，我们需要任何现有对象（用于更新对象）或父对象的对象全名 (OFN)，也称为完全可分辨名称 (FDN) （用于创建新对象）。此 OFN 或 FDN 充当唯一标识对象的主键。对于 NSP 模块下可用的诺基亚网络对象，每个对象都有一个 OFN 或 FDN 属性。要获取要更新的端口的 OFN，我们将使用 v1/managedobjects/searchWithFilter API 和以下过滤条件：

```python
#update_port_desc.py (part 1)
import requests
import json
token = <token obtain earlier>
headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer {}'.format(token)
}
url1 = "https://NFMP_URL:8443/nfm-p/rest/api/v1/  managedobjects/searchWithFilter"
payload1 = json.dumps({
  "fullClassName": "equipment.PhysicalPort",
  "filterExpression": "siteId ='<site id>'AND     portName='123",
  "resultFilter":[
    "objectFullName",
    "description"
  ]
})
response = requests.request("POST", url1, headers=headers, data=payload1, verify=False)
port_ofn = response.json()[0]['objectFullName']
```

在此代码示例中，我们将对象的名称设置为 fullClassNames。 对象的完整类名可在诺基亚 NFMP 对象模型文档中找到。 我们设置 filterExpression 以根据设备站点的 ID 和端口名称搜索唯一的端口。 resultFilter 属性用于限制 API 在响应中返回的属性。 我们对这个 API 响应中的 objectFullName 属性感兴趣。
接下来，我们将使用名为 v1/managedobjects/ofn 的不同 API 端点来更新网络对象的属性。 在我们的例子中，我们只更新了 description 属性。 对于更新操作，我们必须在有效负载中设置 fullClassName 属性，并为 description 属性设置一个新值。 对于 API 端点的 URL，我们将连接在上一步中计算的 port_ofn 变量。 这部分程序的示例代码如下：

```python
#update_port_desc.py (part 2)
payload2 = json.dumps({
  "fullClassName": "equipment.PhysicalPort",
  "properties": {
    "description": "description added by a Python       program"
  }
})
url2 = "https:// NFMP_URL:8443/nfm-p/rest/api/v1/  managedobjects/"+port_ofn
response = requests.request("PUT", url2, headers=headers,   data=payload2, verify=False)
print(response.text)
```

网络自动化是按特定顺序创建和更新许多网络对象的过程。例如，我们可以在创建 IP 连接服务之前更新端口以连接两个或多个局域网。这种类型的用例要求我们执行一系列任务来更新所有涉及的端口以及许多其他对象。使用 NMS API，我们可以在程序中编排所有这些任务以实现自动化流程。
在下一节中，我们将探讨如何与诺基亚 NSP 或类似系统集成以进行事件驱动的通信。

## 与事件驱动系统集成

在前面的部分中，我们讨论了如何使用请求-响应模型与网络设备和网络管理系统进行交互。在此模型中，客户端向服务器发送请求，服务器发送响应作为对请求的回复。 HTTP (REST API) 和 SSH 协议基于基于请求-响应的模型。该模型非常适用于配置系统或临时或定期获取网络的运行状态。但是如果网络中发生了需要运营团队注意的事情呢？例如，假设设备上的硬件故障或线路电缆已被切断。在这种情况下，网络设备通常会发出警报，并且这些警报必须立即到达操作员（通过电子邮件、短信或仪表板）。
我们可以使用请求-响应模型每秒（或每隔几秒）轮询网络设备，以检查网络设备的状态是否有任何变化或是否有新的警报。然而，这不是网络设备资源的有效使用，并且会导致网络中不必要的流量。如果网络设备或 NMS 本身在关键资源状态发生变化或发出警报时联系感兴趣的客户端，该怎么办？这种类型的模型称为事件驱动模型，它是一种流行的发送实时事件的通信方式。
事件驱动系统可以使用 webhooks/WebSockets 或使用流方法来实现。 WebSockets 通过 TCP/IP 套接字提供基于 HTTP 1.1 的双向传输通道。由于这种双向连接不使用传统的请求-响应模型，因此当我们想要在两个系统之间建立一对一连接时，WebSockets 是一种有效的方法。当我们需要两个程序之间的实时通信时，这是最好的选择之一。所有标准浏览器都支持 WebSocket，包括 iPhone 和 Android 设备可用的浏览器。它也是许多社交媒体平台、流媒体应用程序和在线游戏的热门选择。
WebSockets 是一种用于获取实时事件的轻量级解决方案。但是，当许多客户端希望从一个系统接收事件时，使用流式方法是可扩展且高效的。基于流事件的模型通常遵循发布者-订阅者设计模式，并具有三个主要组件，如下所述：
主题：所有流消息或事件通知都存储在一个主题下。我们可以将主题视为一个目录。该主题帮助我们订阅感兴趣的主题，以帮助我们避免接收所有事件。
生产者：这是将事件或消息推送到主题的程序或软件。这也称为发布者。在我们的例子中，它将是一个 NSP 应用程序。
消费者：这是一个从主题中获取事件或消息的程序。这也称为订户。在我们的例子中，这将是我们将编写的 Python 程序。
事件驱动系统可用于网络设备以及网络管理系统。 NMS 平台使用 gRPC 或 SNMP 等事件系统从网络设备接收实时事件，并为编排层或操作或监控应用程序提供聚合接口。对于我们的示例，我们将与来自诺基亚 NSP 平台的事件系统进行交互。诺基亚 NSP 系统提供了一个基于 Apache Kafka 的事件系统。 Apache Kafka 是用 Scala 和 Java 开发的开源软件，它提供了基于发布者-订阅者设计模式的软件消息传递总线的实现。在与 Apache Kafka 进行交互之前，我们将枚举通过 Nokia NSP 提供的关键类别列表（用于 Apache Kafka 中的主题的术语），如下所示：

### 为 Apache Kafka 创建订阅

在从 Apache Kafka 接收任何事件或消息之前，我们必须订阅一个主题或一个类别。 请注意，一次订阅仅对一种类别有效。 订阅通常会在 1 小时后到期，因此建议在到期时间前 30 分钟续订。
要创建新订阅，我们将使用 v1/notifications/subscriptions API 和以下示例代码来获取新订阅：

```python
#subscribe.py
import requests
token = <token obtain earlier>
url = "https://NSP_URL:8544/nbi-notification/api/v1/  notifications/subscriptions"
def create_subscription(category):
  headers = {'Authorization': 'Bearer {}'.format(token) }
  payload = {
      "categories": [
        {
          "name": "{}".format(category)
        }
      ]
  }
  response = requests.request("POST", url, json=payload,                               headers=headers, verify=False)
  print(response.text)
if __name__ == '__main__':
      create_subscription("NSP-PACKET-ALL")
```

该程序的输出将包括重要属性，例如 subscriptionId 、 topicId 和 expiresAt 等，如下所示：

```json
{
   "response":{
      "status":0,
      "startRow":0,
      "endRow":0,
      "totalRows":1,
      "data": {
         "subscriptionId":"440e4924-d236-4fba-b590-           a491661aae14",
         "clientId": null,
         "topicId":"ns-eg-440e4924-d236-4fba-b590-           a491661aae14",
         "timeOfSubscription":1627023845731,
         "expiresAt":1627027445731,
         "stage":"ACTIVE",
         "persisted":true
      },
      "errors":null
   }
}
```

subscriptionId 属性用于稍后更新或删除订阅。 Apache Kafka 将专门为此订阅创建一个主题。 它作为 topicId 属性提供给我们。 我们将使用这个 topicId 属性连接到 Apache Kafka 以接收事件。 这解释了为什么我们在 Apache Kafka 中称为通用主题类别。 expiresAt 属性指示此订阅将到期的时间。
一旦订阅准备就绪，我们就可以连接到 Apache Kafka 以接收事件，如下一小节所述。

### 处理来自 Apache Kafka 的事件

使用 kafka-python 库编写一个基本的 Kafka 消费者只需要几行 Python 代码。 要创建 Kafka 客户端，我们将使用 kafka-python 库中的 KafkaConsumer 类。 我们可以使用以下示例代码为我们的订阅主题消费事件：

```python
#basic_consumer.py
topicid = 'ns-eg-ff15a252-f927-48c7-a98f-2965ab6c187d'
consumer = KafkaConsumer(topic_id,
                         group_id='120',
                         bootstrap_servers=[host_id], value_                          deserializer=lambda m: json.loads                          (m.decode('ascii')),
                         api_version=(0, 10, 1))
try:
    for message in consumer:
        if message is None:
            continue
        else:
            print(json.dumps(message.value, indent=4, sort_              keys=True))
except KeyboardInterrupt:
    sys.stderr.write('++++++ Aborted by user ++++++++\n')
finally:
    consumer.close()
```

请务必注意，如果您使用 Python 3.7 或更高版本，则必须使用 kafka-python 库。如果您使用的 Python 版本早于 3.7，则可以使用 kafka 库。如果我们在 Python 3.7 或更高版本中使用 kafka 库，则存在已知问题。例如，有一个已知问题，即 async 在 Python 3.7 或更高版本中已成为关键字，但它已在 kafka 库中用作变量。在 Python 3.7 或更高版本中使用 kafka-python 库时，也存在 API 版本问题。这些可以通过将正确的 API 版本设置为参数来避免（在本例中为 0.10.0 版本）。
在本节中，我们向您展示了一个基本的 Kafka 消费者，但您可以通过访问 https://github.com/nokia/NSP-Integration-Bootstrap/tree/master 在本书提供的源代码中探索更复杂的示例/kafka/kafka_cmd_consumer.
### 续订和删除订阅

我们可以使用与创建订阅相同的 API 端点通过诺基亚 NSP Kafka 系统续订订阅。我们将在 URL 末尾添加 subscriptionId 属性以及续订资源，如下所示：
https://{{server}}:8544/nbi-notification/api/v1/notifications/subscriptions/<subscriptionId>/renewals
我们可以使用相同的 API 端点和 URL 末尾的 subscriptionId 属性删除订阅，但使用 HTTP Delete 方法。对于删除请求，此 API 端点将如下所示：
https://{{server}}:8544/nbi-notification/api/v1/notifications/subscriptions/<subscriptionId>
在这两种情况下，我们都不会在请求正文中发送任何参数。
我们对使用请求-响应模型和事件驱动模型与 NMS 和网络控制器集成的讨论到此结束。在与其他管理系统集成时，这两种方法都将为您提供一个很好的起点。

## 概括
在本章中，我们介绍了网络自动化，以及它给电信服务提供商带来的好处和挑战。我们还讨论了网络自动化的关键用例。在介绍之后，我们讨论了可用于网络自动化与网络设备交互的传输协议。可以通过多种方式采用网络自动化。我们首先研究如何使用 Python 中的 SSH 协议直接与网络设备交互。我们使用 Paramiko、Netmiko 和 NAPALM Python 库从设备获取配置，并详细说明了如何将此配置推送到网络设备。接下来，我们讨论了如何使用 NETCONF 和 Python 与网络设备进行交互。我们提供了使用 NETCONF 的代码示例，并使用 ncclient 库获取 IP 接口配置。我们还使用相同的库来更新网络设备上的 IP 接口。
在本章的最后一部分，我们探讨了如何与诺基亚 NSP 等网络管理系统进行交互。我们使用 Python 作为 REST API 客户端和 Kafka 消费者与诺基亚 NSP 系统进行交互。我们提供了一些关于如何获取身份验证令牌的代码示例，然后将 REST API 发送到 NMS 以检索配置数据并更新设备上的网络配置。
本章包含几个代码示例，让您熟悉使用 Python 与使用 SSH、NETCONF 协议和使用 NMS 级 REST API 的设备进行交互。如果您是一名自动化工程师并希望通过使用 Python 功能在您的领域中脱颖而出，那么这些实践知识至关重要。
本章结束本书。我们不仅涵盖了 Python 的高级概念，还深入了解了在许多高级领域中使用 Python 的情况，例如数据处理、无服务器计算、Web 开发、机器学习和网络自动化。

## 问题

Paramiko 库中用于连接设备的常用类的名称是什么？
NETCONF的四层是什么？
您可以将配置直接推送到 NETCONF 中正在运行的数据库吗？
为什么 gNMI 比网络配置更适合数据收集？
RESTCONF 是否提供与 NETCONF 相同的功能，但通过 REST 接口？
Apache Kafka 中的发布者和消费者是什么？

## 进一步阅读

掌握 Python 网络，作者 Eric Chou。
实用网络自动化，第二版，作者 Abhishek Ratan。
网络可编程性和自动化，作者 Jason Edelman。
Paramiko 官方文档可从 http://docs.paramiko.org/ 获得。
Netmiko 官方文档可在 https://ktbyers.github.io/ 获得。
NAPALM 官方文档可在 https://napalm.readthedocs.io/ 获得。
ncclient 官方文档可在 https://ncclient.readthedocs.io/ 获得。
NETCONF YANG 模型可以在 https://github.com/YangModels/yang 找到。
诺基亚 NSP API 文档可从 https://network.developer.nokia.com/api-documentation/ 获得。

## 答案

paramiko.SSHClient 类。
内容、操作、消息和传输。
如果网络设备不支持候选数据库，它通常允许对正在运行的数据库执行直接更新。
gNMI 基于 gRPC，这是 Google 为移动客户端和云应用程序之间的 RPC 调用引入的协议。该协议针对数据传输进行了优化，与配置网络设备相比，这使得从网络设备收集数据的效率更高。
RESTCONF 通过 REST 接口提供了 NETCONF 的大部分功能，但它没有公开 NETCONF 的所有操作。
发布者是一个客户端程序，它将消息作为事件发送到 Kafka 主题（类别），而消费者是一个客户端应用程序，它读取和处理来自 Kafka 主题的消息。
