# GOC 代码测试

## 综述

*基于 EOS v1.3.\* and GOC，不同版本的测试数量和名称可能存在区别*

编译成功后，进入build目录，输入make test即可进入测试步骤。完成后会显示测试的结果和消耗时间，以及出错的位置等，正常情况下全部测试内容需要超过一个小时进行。

GOC发布tag版本时，会确保测试均能正常通过，但中间版本可能存在部分测试无法通过。

**启动测试前请关闭所有的nodeos,keosd进程，否则可能造成异常**

```bash
100% tests passed, 0 tests failed out of 48

Total Test time (real) =  XXXX sec
```

*Test 36 依赖于mongodb，需事先准备好环境，并手动启动，测试脚本不会主动启动，在其他测试正常通过时一般可以忽略此项。*



## 分项测试

进入build目录后，即可使用make test命令（或者ctest），进行一系列测试，以下逐个列出测试内容。

```sh
Test project /$EOS/build
  Test  #1: test_cypher_suites
  Test  #2: validate_simple.token_abi
  Test  #3: validate_eosio.token_abi
  Test  #4: validate_eosio.msig_abi
  Test  #5: validate_eosio.sudo_abi
  Test  #6: validate_multi_index_test_abi
  Test  #7: validate_eosio.system_abi
  Test  #8: validate_identity_abi
  Test  #9: validate_identity_test_abi
  Test #10: validate_stltest_abi
  Test #11: validate_test.inline_abi
  Test #12: validate_hello_abi
  Test #13: validate_asserter_abi
  Test #14: validate_infinite_abi
  Test #15: validate_proxy_abi
  Test #16: validate_test_api_abi
  Test #17: validate_test_api_mem_abi
  Test #18: validate_test_api_db_abi
  Test #19: validate_test_api_multi_index_abi
  Test #20: validate_test_ram_limit_abi
  Test #21: validate_eosio.bios_abi
  Test #22: validate_noop_abi
  Test #23: validate_dice_abi
  Test #24: validate_tic_tac_toe_abi
  Test #25: validate_payloadless_abi
  Test #26: validate_integration_test_abi
  Test #27: unit_test_binaryen
  Test #28: unit_test_wavm
  Test #29: unit_test_wabt
  Test #30: validate_deferred_test_abi
  Test #31: plugin_test
  Test #32: nodeos_sanity_test
  Test #33: nodeos_run_test
  Test #34: bnet_nodeos_run_test
  Test #35: p2p_dawn515_test
  Test #36: nodeos_run_test-mongodb
  Test #37: distributed-transactions-test
  Test #38: restart-scenarios-test-resync
  Test #39: restart-scenarios-test-hard_replay
  Test #40: restart-scenarios-test-none
  Test #41: validate_dirty_db_test
  Test #42: launcher_test
  Test #43: nodeos_sanity_lr_test
  Test #44: bnet_nodeos_sanity_lr_test
  Test #45: nodeos_forked_chain_lr_test
  Test #46: nodeos_voting_lr_test
  Test #47: bnet_nodeos_voting_lr_test
  Test #48: nodeos_under_min_avail_ram_lr_test

Total Tests: 48
```


### 配置ctest参数

ctest可以传入指定参数，使用--help可以查看详细参数，以下为常用的参数用法。

因此可以指定选定的测试，简化出现异常后的复测。

```sh
#-I [Start,End,Stride,test#,test#|Test file], --tests-information                               = Run a specific number of tests by number.
#ctest -I 起点,终点,间隔
ctest -I 33,35,1 

#-R <regex>, --tests-regex <regex> = Run tests matching regular expression.
#-E <regex>, --exclude-regex <regex> = Exclude tests matching regular expression.

#ctest -R ABC，包含ABC正则表达式的测试
ctest -R unit_test
#ctest -E ABC，不包含ABC正则表达式的测试
ctest -E api

#-N,--show-only = Disable actual execution of tests.
#仅列出测试
ctest -N
```



### 测试内容详述

### 1 test_cypher_suites

libraries/fc/test/crypto/test_cypher_suites，公私钥部分测试

### 2-26 abi validation

检查abi文件是否符合规范。

### 27-29 unit test

主要是合约相关的单元测试，使用/build/unittests/unit_test方法，分别以binaryen（将于1.4.\*移除），wavm，wabt模式测试。使用了Boost.Test的能力，因此可以通过提供参数的方式进行部分测试。

详细的测试内容请参见源代码中unittests目录中，其中每个文件对应一组完整测试，由BOOST_AUTO_TEST_SUITE命名，分项测试由BOOST_FIXTURE_TEST_CASE命名，EOS原生功能不在此详述。

```cpp
//此处abi_tests即为测试组名称
BOOST_AUTO_TEST_SUITE(abi_tests)

//此处abigen_double_action即为分项测试名称
BOOST_FIXTURE_TEST_CASE(abigen_double_action, abi_gen_helper)
```

使用unittests目录中的unit_test启动测试，**请注意所有的单元测试仍需事先编译完成**

```bash
#完整的一组测试
$GOC/build/unittests/unit_test -t "eosio_system_tests"
#执行某个指定的测试
$GOC/build/unittests/unit_test -t "eosio_system_tests/multiple_producer_pay"
```
测试结果显示如下：

```bash
Random number generator seeded to 1540087023
Running 1 test case...

*** No errors detected
```

GOC相关的合约单元测试部分正在逐步完善中，清单如下所示（在整体测试中均被包含，无需专门进行）：。

| 代码位置 | 测试组 | 单元测试 | GOC新增 |
| -------- | ------ | -------- | ------- |
|eosio.system_tests.cpp|eosio_system_tests|goc_stake_unstake|GOC抵押与赎回测试|


### 32-48 节点运行测试

包括故障模拟等，建议在无负载环境测试，否则出块异常会Fail。

### 36 nodeos_run_test-mongodb

此测试依赖mongodb，需事先安装mongodb，并在默认端口27017启动(可以配置)，否则会报错。



