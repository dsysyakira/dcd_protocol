## Description
Push a transaction with a single action

## Positionals
  `contract` _Type: Text_ - The account providing the contract to execute
 
  `action` _Type: Text_ - The action to execute on the contract
 
  `data` _Type: Text_ - The arguments to the contract

## Options

 ` -h,--help` - Print this help message and exit
 
 `-x,--expiration` - set the time in seconds before a transaction expires, defaults to 30s
 
 `-f,--force-unique` - force the transaction to be unique. this will consume extra bandwidth and remove any protections against accidently issuing the same transaction multiple times

` -s,--skip-sign` - Specify if unlocked wallet keys should be used to sign transaction

`-j,--json` - print result as json

`-d,--dont-broadcast` - don't broadcast transaction to the network (just print to stdout)

`-p,--permission` _Type: Text_ - An account and permission level to authorize, as in 'account@permission'

`--max-cpu-usage-ms` _UINT_ - set an upper limit on the milliseconds of cpu usage budget, for the execution of the transaction (defaults to 0 which means no limit)

`--max-net-usage` _UINT_ - set an upper limit on the net usage budget, in bytes, for the transaction (defaults to 0 which means no limit)

`--delay-sec` _UINT_ - set the delay_sec seconds, defaults to 0s

## Output

For actions that return a value `dcdcli` has a line which is prefixed with `=>`.

Let's consider for exemplification the three actions defined below. The first two are `rstring` and `ruint`, which return the value passed as input parameter to them, and the third one is `rtmp` returning a default `tmp` object.

```c++
struct tmp {
    uint32_t id = 1;
    std::vector<uint8_t> list{ 1, 2 };
};

[[dcd::action]]
std::string rstring(const std::string & str) { return str;   }

[[dcd::action]]
uint16_t    ruint(uint16_t i)                { return i;     }

[[dcd::action]]
tmp         rtmp()                           { return tmp{}; }
```

The `dcdcli` commands and their respective outputs are.

```shell
> dcdcli push action dcd rstring '{"str":null}'  -p dcd@active

executed transaction: 1e72a3ef1fa01a9c90b870fe86bf31413c6a2f40a2722ca72d9dd707f58851af  96 bytes  116 us
#         dcd <= dcd::rstring               {"str":""}
=>                                return value: ""

> dcdcli push action dcd rstring '{"str":"test"}'  -p dcd@active

executed transaction: 2484ba021683bb3c6daaabe291ba291e2689d1be899a293d0a09cc68cf0967fb  96 bytes  116 us
#         dcd <= dcd::rstring               {"str":"test"}
=>                                return value: "test"

> dcdcli push action dcd ruint '{"i":42}'  -p dcd@active

executed transaction: 12c51fd27fad9bb0fa959037a72093ad0b168f5846e916a0e9a295c8416bdf9f  96 bytes  138 us
#         dcd <= dcd::ruint                 {"i":42}
=>                                return value: 42
```

The `rtmp` action output, returning a default `tmp` object, looks like this:

```shell
> dcdcli push action dcd rtmp '{}'  -p dcd@active

executed transaction: 42386687d94695d77d67cf901cf8d1a4f83ac9f89382f0b01f3fb51267cc21cd  96 bytes  110 us
#         dcd <= dcd::rtmp                  ""
=>                                return value: {"id":1,"list":[1,2]}
```

When it is not possible to decode the return value, for example when ABI doesn’t contain information about an action return value, output will look like this:

```shell
=>                          return value (hex): 1400000001000770656e64696e6700000000
```

If the length of the return value string is more than 100 chars the string will be truncated to the first 100 chars and three dots `...` will be added at the end. It's similar to what dcdcli does to action parameters output.

```shell
> dcdcli push action dcd rstring '{"str":"qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq"}'  -p dcd@active
executed transaction: 9fb35c72dbccadb4ff7b72a3a046543fbe2ae36eea70265f2b8356e8192c3f0e  248 bytes  176 us
#         dcd <= dcd::rstring               {"str":"qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq...
=>                                return value: "qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq...
```
