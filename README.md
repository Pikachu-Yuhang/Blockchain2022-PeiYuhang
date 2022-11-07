# ZJU-Blockchain-course-2022 组织社团治理平台

裴宇航 3200104179

## 如何运行

1. 在本地启动ganache应用。
2. 在 `./contracts` 中安装需要的依赖，运行如下的命令：

```shell
npm install
```

3. 在`./contracts`中编译合约，运行如下命令：

```shell
npx hardhat compile
```

4. 在`./contracts`中部署合约，运行如下命令：

```shell
 npx hardhat run ./scripts/deploy.ts --network ganache
```

​		该命令会返回三个地址。用这三个地址分别覆盖`./frontend/src/utils/contract-addresses.json`中的三个地址。

5. 把`./contracts/artifacts/contracts/MyERC.sol/MyERC20`、`./contracts/artifacts/contracts/MyERC.sol/MyERC721`和`./contracts/artifacts/contracts/StudentSocialDAO.sol/StudentSocialDAO`这三个文件复制到地址`./frontend/src/utils/abis/`下。
6. 在 `./frontend` 中启动前端程序，运行如下的命令：

```shell
npm start
```

## 功能实现分析

1. 每个学生初始可以领取100点数（ERC20）。

   实现：调用`contract MyERC20`内的`claimPoints()`函数。

2. 每个学生花费10个点数发起一份提案，发起过程中可以输入提案的名称、发起人、具体内容、投票截止时间。

   实现：调用`contract StudentSocialDAO`内的`initiateProposal(string memory _name, string memory _title, uint256 _duration, string memory _content)`函数，在前端中传入各种参数。该函数内部会通过`transferFrom`把用户名下的点数转到合约名下，因此在前端代码中还实现执行了`approve`操作。

3. 提案发起后一定支出时间内，学生可以对提案投支持票或反对票，每票需要花费2个点数。

   实现：调用`contract StudentSocialDAO`内的`supportProposal(uint32 index, uint32 amount)`或`opposeProposal(uint32 index, uint32 amount)`进行支持或反对。

4. 提案投票时间截止后，赞成数大于反对数的提案通过，提案发起者领取15点数作为奖励。

   实现：在前端中定时调用`onRefreshProposals`函数，对所有提案进行检查。对于到达时间限制的函数，调用`contract StudentSocialDAO `内的`passProposal(uint32 index)`或`abortProposal(uint32 index)`，对提案进行通过或废除。

5. 发起提案并通过3次的学生，可以领取纪念品（ERC721）。

   实现：通过`contract MyERC721`内的`ifCanClaimSouviner(address addr)`函数判断用户是否能够领取纪念品，通过`claimSouviner(address addr)`函数实现领取纪念品。

## 项目运行截图

1. 初次登录的页面

![image-20221107142552314](assets/image-20221107142552314.png)

<center>初次登录界面</center>

![image-20221107142707833](assets/image-20221107142707833.png)

<center>点击Connect to Wallet后和钱包交互</center>

2. 主界面

![image-20221107142332562](assets/image-20221107142332562.png)

​		上方显示了用户拥有的点数、用户发起的提案数以及用户被通过的提案数。

​		Proposals列表展现了所有提案的标题、内容概览和状态。

​		右下的三个按钮分别用于领取点数、新建提案和领取纪念品。

3. 发起提案的弹窗

![image-20221107142804939](assets/image-20221107142804939.png)

<center>发起提案的弹窗，可以填写提案信息</center>

![image-20221107142915016](assets/image-20221107142915016.png)

<center>点击Commit后和区块链的交互，包括一次approve和一次TransferFrom</center>

![image-20221107143014951](assets/image-20221107143014951.png)

<center>提案提交成功后的页面</center>

4. 查看提案以及投票的弹窗

![image-20221107143130731](assets/image-20221107143130731.png)

<center>查看提案内容以及投票的弹窗</center>

![image-20221107143201653](assets/image-20221107143201653.png)

<center>投票后和区块链的交互，包括一次approve和一次TransferFrom</center>

5. 领取纪念品的页面以及领取成功的页面

![image-20221107143306676](assets/image-20221107143306676.png)

<center>不可领取纪念品的提示弹窗</center>

![image-20221107143420002](assets/image-20221107143420002.png)

<center>可以领取纪念品的弹窗</center>

![image-20221107143512438](assets/image-20221107143512438.png)

<center>领取纪念品时与区块链的交互</center>

![image-20221107143543321](assets/image-20221107143543321.png)

<center>领取纪念品后，头像右上角有皇冠标识</center>