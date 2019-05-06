# 如何加入GOC测试网

测试网Chain ID：c393daba1664b8fece45d4af778051412db3bcbb8c699e78ccfc6079aa11ff39

## 一、获取代码

```sh
cd ~    # 退出当前目录，进入主目录
git clone https://github.com/GOCLAB/goc.git
cd goc
git submodule update --init --recursive
sudo ./eosio_build.sh -s 'GOC'
sudo ./eosio_install.sh
```


## 二、导入BP账户

测试网已根据GOC主网2019年5月5日所有BP账户快照，在测试网上创建了对应的BP账户，各位BP只需解锁主网钱包或导入相应私钥到钱包即可操作测试网BP账户。具体BP账户名单如下：

```sh
[u'goclabgoclab', u'fudangocbp11', u'halowalletbp', u'lichang11111', u'gravitypooll', u'palletone111', u'gocbeijingbp', u'gbacbpforgoc', u'chainwavegoc', u'gocsecbookbp', u'blockgogreat', u'gogocbpgogoc', u'bpyottachain', u'shanghainode', u'starthallobp', u'gocwaterdrip', u'governancebp', u'consultingbp', u'blockedencom', u'gocchaingeek', u'interchainbp', u'eosouwallet1', u'eosrealbpcsg', u'goclegalnode', u'sursengroupt', u'zmaozmaozmao', u'goc111111111', u'goc121goc121']
```

导入相应私钥到钱包的具体步骤如下：

```sh
~/goc/build/programs/keosd/keosd &  # 后台启动钱包服务
cd ~/goc/build/programs/cleos   # 进入cleos目录
./cleos wallet create --to-console    # 默认创建名为default的钱包，记录打印出来的钱包密码
./cleos wallet import       # 运行该命令后会提示输入私钥，即导入私钥到default钱包
```

## 三、注册出块BP

**账户创建完成后**，将BP账户注册为出块BP：

```shell
./cleos wallet create_key    # 创建一对公私钥作为producer key
./cleos -u http://api.goclab.io:7777 system regproducer <yourbpname> <your_producer_pub_key>
# yourbpname为你的BP账户名，your_producer_pub_key为上一条命令创建的公钥
```

注：若钱包15分钟未使用，会提示钱包被锁，需要用以下命令解锁钱包：
```shell
./cleos wallet unlock   # 根据提示输入钱包密码即可
```


## 四、准备配置文件

1、genesis.json

在~/GOCtestnet/build/programs/nodeos文件夹下创建 *genesis.json* 文件，填入以下内容：

```json
{
  "initial_timestamp": "2019-05-01T00:00:00.000",
  "initial_key": "GOC7ansp4mGVtCoWbWv9zpkspZYV3YFJRVRxTLBQXJkR64fQF7rjo",
  "initial_configuration": {
    "max_block_net_usage": 1048576,
    "target_block_net_usage_pct": 1000,
    "max_transaction_net_usage": 524288,
    "base_per_transaction_net_usage": 12,
    "net_usage_leeway": 500,
    "context_free_discount_net_usage_num": 20,
    "context_free_discount_net_usage_den": 100,
    "max_block_cpu_usage": 300000,
    "target_block_cpu_usage_pct": 1000,
    "max_transaction_cpu_usage": 250000,
    "min_transaction_cpu_usage": 100,
    "max_transaction_lifetime": 3600,
    "deferred_trx_expiration_window": 600,
    "max_transaction_delay": 3888000,
    "max_inline_action_size": 4096,
    "max_inline_action_depth": 4,
    "max_authority_depth": 6
  }
}
```

genesis.json文件定义了初始链状态，所有节点必须从相同的初始状态开始

2、config.ini

将[config.ini](https://github.com/GOCLAB/GOC-testnet/blob/master/config.ini)复制到~/goc/build/programs/nodeos文件夹下



## 五、启动出块节点

准备好一切之后，便可启动出块节点，连接测试网：

```shell
cd ~/goc/build/programs/nodeos

./nodeos --genesis-json ./genesis.json --config-dir ~/GOCtestnet/build/programs/nodeos --http-server-address 0.0.0.0:8888 --p2p-listen-endpoint 0.0.0.0:9876 --http-validate-host=false --producer-name 'yourbpname' --signature-provider='your_producer_pub_key'=KEY:'your_producer_private_key' --plugin eosio::http_plugin --plugin eosio::chain_api_plugin --plugin eosio::producer_plugin --plugin eosio::history_api_plugin
# yourbpname填入BP账户名; your_producer_pub_key、your_producer_private_key分别填入创建的producer key的公钥和私钥。
```

连接测试网，会先同步测试网中已生产的块，等待一段时间同步完成后，每0.5s会收到出块节点产出的块，终端显示如下示例信息：
```
2018-09-29T10:47:23.478 thread-0   producer_plugin.cpp:332       on_incoming_block    ] Received block 9838cc2c992c2725... #406196 @ 2018-09-29T10:47:23.500 signed by producer111h [trxs: 0, lib: 406028, conf: 0, latency: -21 ms]
2018-09-29T10:47:24.072 thread-0   producer_plugin.cpp:332       on_incoming_block    ] Received block 3624e2ab8697a1e1... #406197 @ 2018-09-29T10:47:24.000 signed by producer111i [trxs: 0, lib: 406040, conf: 120, latency: 72 ms]
```



## 六、投票

再打开另一个命令行终端窗口，输入以下命令：

```shell
cd ~/goc/build/programs/cleos

./cleos system voteproducer prods 'yourbpname' 'yourbpname'
# yourbpname填入BP账户名

./cleos get schedule 
# 查看当前测试网出块节点
```
当nodeos同步到最新块，且得票数足够多BP账户出现在schedule中时，便可观察自己的节点是否正常出块



