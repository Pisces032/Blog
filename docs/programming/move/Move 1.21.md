---
date: 2024-01-22
authors:
  - P1sc3s007
---
<a name="yYEU7"></a>
# 泛型
避免重复的模板函数<br />在没有范型的情况下，我们可以定义一个包含 u64 类型的 Box<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705805824371-9eccec6c-d7a3-4ce9-a06d-582722422664.png#averageHue=%23f5f6f5&clientId=ua54f5352-9c69-4&from=paste&height=106&id=ua4e651e1&originHeight=159&originWidth=270&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=7343&status=done&style=none&taskId=uf9980b95-e89f-422a-a054-d010aa167c9&title=&width=180)<br />但是，这种类型只能保存 u64 类型的值，为了能够存储其他类型显然我们不能把所有类型的box都枚举完，所以这个时候就需要使用泛型。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705805852951-f8448a84-c2e8-4ee8-8a60-53e861dfd59e.png#averageHue=%23f5f6f5&clientId=ua54f5352-9c69-4&from=paste&height=101&id=u67a58279&originHeight=152&originWidth=268&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=7190&status=done&style=none&taskId=u4b3f9139-9ba5-42e3-95fc-9df7d305e56&title=&width=178.66666666666666)（加上尖括号）
<a name="j2NCr"></a>
## 能力限制
我们可以添加条件去强制传递给泛型的类型必须具有某些能力。（能多不能少）<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705805990630-a57e789b-50aa-421e-928c-c3badd43a984.png#averageHue=%23f5f6f5&clientId=ua54f5352-9c69-4&from=paste&height=119&id=u3fad2637&originHeight=178&originWidth=648&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=16819&status=done&style=none&taskId=ub5e9c7ba-3922-44c4-beab-72ee5d415da&title=&width=432)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705808544500-41e76f08-cbfe-45ec-bdc9-004dae81f8c4.png#averageHue=%23211e21&clientId=ua54f5352-9c69-4&from=paste&height=433&id=u0a11194e&originHeight=649&originWidth=1260&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=322902&status=done&style=none&taskId=ub6722111-8076-476c-a566-3309a634633&title=&width=840)<br />phantom可以绕过规则
<a name="jplnt"></a>
### 示例：generics.move
先部署智能合约：`sui client publish --gas-budget 100000000 --skip-fetch-latest-git-deps --skip-dependency-verification` <br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705812025131-9348bab5-7a59-4d77-b0ef-fb8ac1adb2ed.png#averageHue=%231c1a1d&clientId=ua897697c-cc05-4&from=paste&height=303&id=ub9107948&originHeight=454&originWidth=1667&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=379168&status=done&style=none&taskId=ue015af88-2dcc-4325-9d9b-f53086d8eac&title=&width=1111.3333333333333)<br />类型参数 type-args<br />`sui client call --package 0x92321dc9b575a77207129d0321bb65041fe455f0e1c0c37b95ad0f363b274028 --module g1 --function "create_box" --args 0x2ffb33f9f1516e890e46d07739fecf38cdb4373920dd186d1977203c6771f84c --type-args "0x2::coin::Coin<0x2::sui::SUI>" --gas-budget 10000`
<a name="z2b6S"></a>
# Witness（见证者模式）
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705824567721-c89fc1c4-b6d6-49bd-b261-7eef048b522c.png#averageHue=%23302e30&clientId=u4e2f32aa-d03e-4&from=paste&height=515&id=ue83b4948&originHeight=772&originWidth=1598&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=581254&status=done&style=none&taskId=u5f751ca6-ee9a-4173-afd3-88d859a8d69&title=&width=1065.3333333333333)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705824665595-8bb3f2b4-bdf6-411b-ba71-3b3bb82b598f.png#averageHue=%23312f32&clientId=u4e2f32aa-d03e-4&from=paste&height=592&id=u63214f67&originHeight=888&originWidth=1568&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=822340&status=done&style=none&taskId=u929bcba8-156e-45da-916b-92df11af92c&title=&width=1045.3333333333333)

- Phantom：我们还想把 witness 资源 PEACE 传入 Guardian，但 PEACE 只有 drop 的能力。回顾我们之前关于能力约束和内部类型的讨论，该规则暗示PEACE也应该有key和storage，因为外部类型Guardian有。但是在这种情况下，我们不想给我们的witness类型添加不必要的能力，因为这样做可能会导致不符合预期的行为和漏洞。我们可以使用关键字`phantom`来解决这种情况。当一个类型参数没有在结构定义中使用，或者它只是作为另一个`phantom`类型参数的参数使用时，我们可以使用phantom关键字来要求Move类型系统放松对内部类型的能力约束规则。

![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705824700450-e9e9931f-8f06-419d-bb44-208378589fee.png#averageHue=%23201e21&clientId=u4e2f32aa-d03e-4&from=paste&height=596&id=Ow97A&originHeight=894&originWidth=1522&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=618912&status=done&style=none&taskId=u18f678cb-4e9e-4c6e-b111-82fd647cedb&title=&width=1014.6666666666666)

- One Time Witness：一次性见证One Time Witness（OTW）是Witness模式的一个子模式，我们利用模块init函数来确保只创建一个witness资源的实例（所以A类型被保证是唯一的）。
<a name="KcyoY"></a>
# 发行代币
<a name="bKdh5"></a>
## Coin资源
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705825089941-98283993-9686-4072-864c-8d4a44830a55.png#averageHue=%23b9bebd&clientId=u4e2f32aa-d03e-4&from=paste&height=333&id=u6a7be891&originHeight=499&originWidth=857&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=182609&status=done&style=none&taskId=ueaf8c6bc-94aa-44b0-9971-36e1f602ddb&title=&width=571.3333333333334)<br />Balance没有Key能力，不能进行全局查找和transfer。而Coin可以，因此把Balance包在Coin里面。
<a name="PAWxP"></a>
## create_currency 方法
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705826833850-7c1c65ef-5154-4a6a-9c58-414b42db792d.png#averageHue=%23f5f6f5&clientId=u4e2f32aa-d03e-4&from=paste&height=581&id=u639c5fc0&originHeight=871&originWidth=891&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=100000&status=done&style=none&taskId=ufdad4ee8-2ed2-499f-a97b-dce00443d4c&title=&width=594)<br />使用Sui框架中的sui::types::is_one_time_witness 方法检查传入的witness资源是否是一次性见证。<br />该方法创建并返回两个对象，一个是TreasuryCap资源，另一个是CoinMetadata资源。
<a name="Bgowj"></a>
### TreasuryCap
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705826890839-6143d208-fb6a-497d-a47c-8c221ad48268.png#averageHue=%23f4f4f2&clientId=u4e2f32aa-d03e-4&from=paste&height=409&id=uab912c8b&originHeight=613&originWidth=1147&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=111821&status=done&style=none&taskId=u1d796c29-d743-48df-8968-fa89de6d960&title=&width=764.6666666666666)
<a name="kozzK"></a>
### CoinMetadata
这是一个存储已创建的可替换代币的元数据的资源。它包括以下字段。

- `decimals`: 这个自定义可替换代币的精度
- `name`：这个自定义可替换标记的名称
- `symbol`：这个自定义可替换标记的标记符号
- `description`: 这个自定义可替换标记的描述
- ``icon_url'`: 这个自定义可替换代币的图标文件的网址。
<a name="rsScX"></a>
## Manage Coin案例
合约部署后，创建的两个不可变对象分别是 `package` 本身和 `Managed Coin` 的 `CoinMetadata object`，交易发送人收到并拥有的对象就是 `Managed Coin` 的 `TreasuryCap` 对象。
<a name="H3xPB"></a>
### package
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828006275-3f095bb4-74de-40e7-912c-43565b3390d2.png#averageHue=%231c1b1b&clientId=u4e2f32aa-d03e-4&from=paste&height=221&id=ub0980c44&originHeight=331&originWidth=931&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=22218&status=done&style=none&taskId=u6556a1d7-f99f-423f-b6e2-c7a0b91ffb8&title=&width=620.6666666666666)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828020201-44b108c1-d7d3-45da-bfe9-f2ed05340a7f.png#averageHue=%23fdfdfd&clientId=u4e2f32aa-d03e-4&from=paste&height=593&id=u0e070bd8&originHeight=890&originWidth=2145&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=146922&status=done&style=none&taskId=ue4bd05fa-f2d2-4827-a0de-56ecb3f0794&title=&width=1430)

- <br />

<a name="RAQLZ"></a>
### CoinMetadata
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828100578-0885cc00-77ed-4308-8cdc-9cf2f6e9d793.png#averageHue=%231e1d1c&clientId=u4e2f32aa-d03e-4&from=paste&height=281&id=u426124c3&originHeight=421&originWidth=1133&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=41107&status=done&style=none&taskId=u8d6d331f-465e-448d-872a-67454592465&title=&width=755.3333333333334)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828089714-9cfdbc44-8e7d-4dd2-bfd8-da98c3f97292.png#averageHue=%238fc38e&clientId=u4e2f32aa-d03e-4&from=paste&height=783&id=u3bcf40c6&originHeight=1174&originWidth=2110&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=211314&status=done&style=none&taskId=u445c01f9-cea8-4985-8532-a2bb3c8527c&title=&width=1406.6666666666667)
<a name="N8S8A"></a>
### TreasuryCap
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828145403-975e680c-73bf-44fa-9402-47f9c6cce816.png#averageHue=%2321201f&clientId=u4e2f32aa-d03e-4&from=paste&height=213&id=ufd9a4f47&originHeight=319&originWidth=1126&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=43608&status=done&style=none&taskId=u1554dc22-f601-4ca4-a600-d3c34c89198&title=&width=750.6666666666666)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828154166-06e740a6-de9c-455c-8ed4-e042c028e120.png#averageHue=%2390c390&clientId=u4e2f32aa-d03e-4&from=paste&height=700&id=u661defd2&originHeight=1050&originWidth=2083&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=217006&status=done&style=none&taskId=u203bb18c-30c1-4c49-ae5a-2af771a20ae&title=&width=1388.6666666666667)
<a name="UKvyj"></a>
## 铸造代币（mint函数）

- `sui client call --function mint --module managed --package $PACKAGE_ID --args $TREASURYCAP_ID \"<amount to mint>\" <recipient address> --gas-budget 3000`

可以看到三个参数分别是 TreasuryCapID、铸造代币数量、接受者地址<br />实际代码 `sui client call --function mint --module managed --package 0x97a0b24e24d5103bbad0b47688356814c7e8e6b1f94b7907344a505459e677dc --args 0xb805040af4c66a770b9daa1c4646b5f8aac9f257fcd4535c7a45cd717ded29be 100 0xf1a3394e4cfbc855ffcad1774b7505eff2d87659ad135a9fecf2755b1097bb8f --gas-budget 10000000`<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828425762-dc60cf3e-c512-43c2-9428-4aa585b16b05.png#averageHue=%231f1e1e&clientId=u4e2f32aa-d03e-4&from=paste&height=326&id=u5eb5d360&originHeight=489&originWidth=1961&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=85733&status=done&style=none&taskId=u3570b8a2-2a36-4259-835a-8d30f407de8&title=&width=1307.3333333333333)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828460084-ae5283cb-23bb-422a-aa2f-827537394f17.png#averageHue=%23efcfb0&clientId=u4e2f32aa-d03e-4&from=paste&height=817&id=ua04da394&originHeight=1226&originWidth=2178&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=248777&status=done&style=none&taskId=u6e0e87f7-48d0-47ba-bcdf-93bc836a434&title=&width=1452)<br />运行成功

<a name="HSuBV"></a>
## 销毁代币（burn函数）

- `sui client call --function burn --module managed --package $PACKAGE_ID --args $TREASURYCAP_ID $COIN_ID --gas-budget 3000`

需要的参数有两个：`TreasuryCapID`和`CoinID`

- Coin ID

![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828603309-2802048e-c9e4-4141-8eaf-283be3a789bd.png#averageHue=%2390c390&clientId=u4e2f32aa-d03e-4&from=paste&height=561&id=ub9ba66e4&originHeight=842&originWidth=2100&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=164640&status=done&style=none&taskId=u160b6ff0-9665-4d5b-ae79-f89b1a803df&title=&width=1400)<br />实际代码 `sui client call --function burn --module managed --package 0x97a0b24e24d5103bbad0b47688356814c7e8e6b1f94b7907344a505459e677dc --args 0xb805040af4c66a770b9daa1c4646b5f8aac9f257fcd4535c7a45cd717ded29be 0x6ede12fb9c98d6bca757689bca94b8cf6a4aac8b0453e49e791fb9cc071890dc --gas-budget 10000000`<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828827422-54d6f0a6-12b0-4a15-bb24-1c178c31c8c2.png#averageHue=%231f1e1e&clientId=u4e2f32aa-d03e-4&from=paste&height=348&id=ucb91bc2a&originHeight=522&originWidth=1914&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=82136&status=done&style=none&taskId=uf3201f56-1268-432d-b710-a9a54713148&title=&width=1276)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705828856653-446de22c-ae4a-4c30-80b0-0dc689395618.png#averageHue=%23eee3c4&clientId=u4e2f32aa-d03e-4&from=paste&height=660&id=u0042a38a&originHeight=990&originWidth=2148&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=192911&status=done&style=none&taskId=uee1f07ce-5de3-4629-ae59-7239688fa09&title=&width=1432)
