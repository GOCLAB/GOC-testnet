# nodeos 启动参数说明

## 正常启动
```bash
./nodeos 
--genesis-json ./genesis.json 
--config-dir ~/GOCtestnet/build/programs/nodeos 
--http-server-address 0.0.0.0:8888 
--p2p-listen-endpoint 0.0.0.0:9876 
--http-validate-host=false 
--producer-name 'yourbpname' 
--signature-provider='your_producer_pub_key'=KEY:'your_producer_private_key' 
--p2p-peer-address BP1:9000 

--p2p-peer-address BP2:9000 
--p2p-peer-address BP2:9000 
--max-clients 15 
--p2p-max-nodes-per-host 15 
```

`--genesis-json ./genesis.json` 节点使用的创世参数文件。

`--config-dir ~/GOCtestnet/build/programs/nodeos ` 节点使用的config.ini位置，如果没有，nodeos启动时，将生成一个默认选项的。

`--http-server-address 0.0.0.0:8888`节点的HTTP服务监听位置，请注意0.0.0.0可能导致某些云服务器的外部IP地址无法访问。

`--p2p-listen-endpoint 0.0.0.0:9876`节点的P2P服务监听位置，请注意0.0.0.0可能导致某些云服务器的外部IP地址无法访问。

`--http-validate-host=false `是否验证http host访问来源，此处设置为否

`--producer-name 'yourbpname'`设置BP名称

`--signature-provider='your_producer_pub_key'=KEY:'your_producer_private_key' `设置启动所需的公私钥对，格式样例为：
`GOC8imf2TDq6FKtLZ8mvXPWcd6EF2rQwo8zKdLNzsbU9EiMSt9Lwz=KEY:5KLGj1HGRWbk5xNmoKfrcrQHXvcVJBPdAckoiJgFftXSJjLPp7b`

`--p2p-peer-address BP1:9000`连接其他BP的P2P服务端口，可以重复多次，将已知BP的P2P端口加入

`--max-clients 15`可连接的最多客户端数量

`--p2p-max-nodes-per-host 15 `可连接的最多p2p节点数量

### 备注

nodeos在源代码中默认初始化了chain, http, net, producer四个plugin，初始化失败无法启动nodeos，因此无需再在启动命令行声明。

```cpp
if(!app().initialize<chain_plugin, http_plugin, net_plugin, producer_plugin>(argc, argv))
	return INITIALIZE_FAIL;
```


