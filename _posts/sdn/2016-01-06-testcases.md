---
layout:         post
title:          Weekly Tech Meeting, 2016-01-06
category:       sdn
description:    NFV,Arno,test cases
---

##1. OPNFV平台功能的验证和测试的主要目标

* 功能测试：验证OPNFV平台的功能，包括VIM和NFVI提供的功能
* 性能测量：通过常用的网络指标来衡量（如带宽、延迟、用户数、连接数等）

测试方法可分为两种，即自顶向下（用例驱动，把use case分解为基本的核心功能）和自底向上（测试平台底层的功能）。

参考地址：https://wiki.opnfv.org/vnf_deployment_test_cases

##2. test cases

Use case介绍了很多，像vPE、vIMS、vEPC、vCDN等等，但是实际有介绍的只有vPE和vIMS。并且vPE和vIMS也只是介绍了概念而已，在Arno sr1版本中并不支持，我在后面会将我了解到的内容做一个总结。
目前对于Arno SR1来说，可以执行并进行测试的test case一共有4个：vPing、OpenDaylight、Rally bench、Tempest。

* vPing 

主要用于测试两个vm的连通性。
测试时输入：
          $ python <functest_repo_directory>/vPing/vPing.py -d <Your_functest_directory>
          
* Rally Bench

主要用于测试Openstack不同组件，并获取VIM（Openstack）的信息，如authenticate、cinder、nova、requests、glance、keystone、neutron、quotas
在测试时在相应位置输入模块名称即可。测试时输入：

    $ python <functest_repo_directory>/testcases/VIM/OpenStack/CI/libraries/run_rally.py <functest_repo_directory> <module_to_be_tested>

* Tempest 

主要用于在环境安装好后，检查Openstack的基本功能，通过Rally来运行。对于Arno来说只提供了smoke test。
测试时输入：
         # rally verify start smoke
         
* Opendaylight

主要用于测试OpenDaylight和Neutron创建和删除网络、子网和端口。
测试时输入：
         $ python <functest_repo_directory>testcases/Controllers/ODL/CI/start_tests.sh

##3.测试环境搭建步骤

还需要两个额外的工具：Rally和Robot。Rally用于运行Tempest测试，Robot用于运行OpenDaylight测试。另外，有一个脚本config_test.py用于安装Rally、Robot和Tempest，并使用配置文件config_functest.yaml。

搭建测试环境的步骤归纳如下：

1、登录到jumpserver，执行以下命令

       $ mkdir <Your_functest_directory>
       
       $ cd <Your_functest_directory>
       
       $ git clone https://git.opnfv.org/functest
       
       $ cd testcases/

2、检索Openstack源文件并配置

      $ source Your_OpenRC_file
      
      $ python <functest_repo_directory>/config_functest.py -d <Your_functest_directory> start
      
3、执行脚本fetch_os_creds.sh实现自动检索Openstack的解决方案的认证信息。

      $/home/jenkins-ci/functest/fetch_os_creds.sh -d <destination> -i <installer_type> -a <installer_ip>

##4. OPNFV支持的testcase
下图是目前OPNFV版本对应包含的testcase信息，目前Arno版本只包含了上述4个testcase，Brahmaputra版本目前还没有发布。所以有好多vnf还Arno还没有支持。

![testcase]({{ site.url }}/image/2016-01-06/testcase.png)

##5. vPE--Provider Edge

基本的vPE拓扑结构是由一个vPE instance保证一个tenant中所有suite的连通。下图为拓扑图：

![PE Topology]({{ site.url }}/image/2016-01-06/topology_PE.png)

其中SUT（system underlay test）指OPNFV平台或者一些VNF组件。CE/PE用IXIA来模拟。
对系统的配置要求如下：

![PE configuration requirements]({{ site.url }}/image/2016-01-06/configuration_PE.png)
![PE hardware requirements]({{ site.url }}/image/2016-01-06/hardware_PE.png)

##6. References
[vnf_deployment_test_cases:https://wiki.opnfv.org/vnf_deployment_test_cases](https://wiki.opnfv.org/vnf_deployment_test_cases)

[vIMS:https://wiki.opnfv.org/ip_multimedia_subsystem](https://wiki.opnfv.org/ip_multimedia_subsystem)

[vPE:https://wiki.opnfv.org/virtual_provider_edge](https://wiki.opnfv.org/virtual_provider_edge)

[pharos:https://wiki.opnfv.org/pharos](https://wiki.opnfv.org/pharos)

[functest:https://wiki.opnfv.org/opnfv_functional_testing](https://wiki.opnfv.org/opnfv_functional_testing)

[Rally:https://wiki.openstack.org/wiki/Rally](https://wiki.openstack.org/wiki/Rally)
