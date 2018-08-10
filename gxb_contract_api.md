## <a name="index"></a>index
* [获取当前合约id](#current_receiver)
* [获取当前调用发送的资产id](#get_action_asset_id)
* [获取当前调用发送的资产数量](#get_action_asset_amount)
* [从当前合约转账到外部账号](#withdraw_asset)


## <a name="current_receiver"></a>uint64\_t current\_receiver()
desc: 返回当前合约账号的id  
include: \<graphenelib/action.h>  
example: 比如合约账号id是1.2.666，调用current_receiver()将返回666  
[index](#index)



## <a name="get_action_asset_id"></a>uint64\_t get\_action\_asset\_id()
desc: 返回本次调用向合约发送的资产id  
include: \<graphenelib/action.h>  
example: 

```c++
#include <graphenelib/action.h>
#include <graphenelib/contract.hpp>
#include <graphenelib/dispatcher.hpp>
#include <graphenelib/types.h>

using namespace graphene;

class helloworld : public contract
{
  public:
    helloworld(uint64_t id) 
        : contract(id)
    {   
    }   

    //@abi action
    //@abi payable
    void deposit()
    {   
        uint64_t asset_id = get_action_asset_id();
    }   
};

GRAPHENE_ABI(helloworld, (deposit))
```

对这个合约通过钱包客户端调用  
call\_contract nathan helloworld {"amount":10000000,"asset\_id":1.3.1} deposit "{}" GXC true  
调用helloworld的deposit方法，在deposit方法的实现中调用get\_action\_asset\_id()将返回1



## <a name="get_action_asset_amount"></a>uint64\_t get\_action\_asset\_amount()
desc: 返回本次调用向合约发送的资产id  
include: \<graphenelib/action.h>  
example: 

```c++
#include <graphenelib/action.h>
#include <graphenelib/contract.hpp>
#include <graphenelib/dispatcher.hpp>
#include <graphenelib/types.h>

using namespace graphene;

class helloworld : public contract
{
  public:
    helloworld(uint64_t id) 
        : contract(id)
    {   
    }   

    //@abi action
    //@abi payable
    void deposit()
    {   
        uint64_t asset_amount = get_action_asset_amount();
    }   
};

GRAPHENE_ABI(helloworld, (deposit))
```

对这个合约通过钱包客户端调用  
call\_contract nathan helloworld {"amount":10000000,"asset\_id":1.3.1} deposit "{}" GXC true  
调用helloworld的deposit方法，在deposit方法的实现中调用get\_action\_asset\_amount()将返回10000000

## <a name="withdraw_asset"></a>void withdraw_asset(uint64\_t from, uint64\_t to, uint64\_t asset\_id, int64\_t amount)
desc: 将当前合约的资产转移到外部账户  

param @from: 从哪个账号转账，一般是_self  
param @to: 转账到哪个外部账户，必须只传账号的instance\_id，比如外部账户是1.2.33，那么传33即可  
param @asset\_id: 指定转账的资产id，必须只传资产id的instance\_id, 比如资产id是1.3.0， 那么传0即可  
param @amount: 转账金额，这个数字包含了资产的精度，比如想转1个GXC，那么应该写100000  

include: \<graphenelib/asset.h>  
example: 

```c++
#include <graphenelib/action.h>
#include <graphenelib/asset.h>
#include <graphenelib/contract.hpp>
#include <graphenelib/dispatcher.hpp>
#include <graphenelib/types.h>

using namespace graphene;

class helloworld : public contract
{
  public:
    helloworld(uint64_t id) 
        : contract(id)
    {   
    }   

    //@abi action
    //@abi payable
    void deposit()
    {   
        uint64_t asset_amount = get_action_asset_amount();
    }
    
    //@abi action
    void testwithdraw() {
        withdraw_asset(_self, 33, 0, 100000);
    }
};

GRAPHENE_ABI(helloworld, (deposit)(testwithdraw))
``` 
call\_contract nathan helloworld null testwithdraw "{}" GXC true  
调用helloworld合约的testwithdraw方法，在testwithdraw方法中调用withdraw\_asset()从helloworld合约账户转账1个GXC到1.2.33账号
[实际的案例](https://github.com/gxchain/gxb-core/blob/dev_master/contracts/examples/bank2/bank.cpp) ,在bank2合约代码的withdraw方法的最后有调用这个api


