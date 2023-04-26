## [PART1](https://itsembedded.com/dhd/verilator_1/)

流程
首先新建`alu.sv`文件，然后新建`tb_alu.sv`文件
因为编写的是ver代码所以要转成C代码使用到

```
verilator --cc alu.sv

The --cc parameter here tells Verilator to convert to C++. Verilator also supports conversion to SystemC, which can be done by using --sc
```

To build the simulation executable, we need to run Verilator again to regenerate the `.mk` files to include the C++ testbench - this is done using `--exe tb_alu.cpp`

```
verilator -Wall --trace -cc alu.sv --exe tb_alu.cpp
```

然后make

`make -C obj_dir -f Valu.mk Valu`

执行生成的可执行文件，然后会生成`waveform.vcd`的文件

`./obj_dir/Valu`

使用`gtkwave`查看`vcd`文件

`gtkwave waveform.vcd`

## PART2

体会到了`Makefile`的作用

```makefile
MODULE=alu		#变量

.PHONY:sim		#伪指令，就是换了一个名字，并且为了防止和其它文件重名
sim: waveform.vcd

.PHONY:verilate
verilate: .stamp.verilate

.PHONY:build
build: obj_dir/Valu

.PHONY:waves	#最顶上的，也是最后的步骤，可以调用前面所有的步骤
waves: waveform.vcd			#生成波形依赖前面的waveform.vcd
	@echo
	@echo "### WAVES ###"
	gtkwave waveform.vcd

waveform.vcd: ./obj_dir/V$(MODULE)   #vcd文件依赖obj文件夹下的Valu
	@echo
	@echo "### SIMULATING ###"
	@./obj_dir/V$(MODULE)			#执行这个

./obj_dir/V$(MODULE): .stamp.verilate	# 依赖于对Valu的make
	@echo
	@echo "### BUILDING SIM ###"
	make -C obj_dir -f V$(MODULE).mk V$(MODULE)

.stamp.verilate: $(MODULE).sv tb_$(MODULE).cpp #依赖两个文件，下面执行代码转换
	@echo
	@echo "### VERILATING ###"
	verilator -Wall --trace -cc $(MODULE).sv --exe tb_$(MODULE).cpp
	@touch .stamp.verilate

.PHONY:lint
lint: $(MODULE).sv
	verilator --lint-only $(MODULE).sv

.PHONY: clean
clean:
	rm -rf .stamp.*;
	rm -rf ./obj_dir
	rm -rf waveform.vcd

```

然后就可以使用

```makefile
make verilator
make build
make sim
make waves
make clean
make lint
```

## Randomized initial values 

One of the observations from [Part 1](https://www.itsembedded.com/dhd/verilator_1/) was that **Verilator is a [two state simulator](https://www.veripool.org/ftp/verilator_doc.pdf)**, meaning that it only supports logic values of `1` and `0`, and **there is no support for `X`** (and only limited support for `Z`). Verilator therefore initializes all signals to `0` by default

To make our testbench initialize signals to random values, 首先要在tb_alu.cpp文件里面例化语句之前加上`Verilated::commandArgs(argc, argv);`

并且在对应的build方式上面也要添加上一些语句，如`--x-assign unique`和`--x-initial unique`

最后，我们需要将 +verilator+rand+reset+2 传递给我们的模拟可执行文件，以将运行时信号初始化技术设置为随机。

```makefile
@./obj_dir/V$(MODULE) +verilator+rand+reset+2
```

## DUT reset 

To reset our DUT and it’s input signals, we update the main loop of our testbench to look like



## X和Z状态的生成

正常来说我们生成的仿真文件里面就只有1和0，不存在x，z，就算你赋值x给wire或reg最后得到的都会是0，一步一步来，我们现在是既想要我们的过程中有x值，有这个的话我们就可以仿真开始时候的未知状态。所以我们希望我们仿真开始的时候的状态也是随机的。

1.先从生成初始化随机状态开始
为了让我们testbench初始化信号是随机值，我们需要在例化实体之前加上

```cpp
Verilated::commandArgs(argc, argv);

int main(int argc, char** argv, char** env) {
    Verilated::commandArgs(argc, argv);
    Valu *dut = new Valu;		//例化
<...>
```

2.我们需要通过添加` --x-assign unique` 和 `--x-initial unique` 来更新我们的验证目标构建命令。 因此，我们的 Makefile 的第 31 行现在应该如下所示：

```makefile
verilator -Wall --trace --x-assign unique --x-initial unique -cc $(MODULE).sv --exe tb_$(MODULE).cpp
```

3. 最后需要将`+verilator+rand+reset+2`传递给我们的模拟可执行文件，也就是在make的时候添加

```makefile
@./obj_dir/V$(MODULE) +verilator+rand+reset+2
```











使用一个简单案例来对生成的C++代码进行一个研究，这样才能知道怎么去写workbench

```verilog
//mux21.sv

module m_mux21(a,b,s,y);
  input   a,b,s;        // 声明3个wire型输入变量a,b,和s，其宽度为1位。
  output  y;           // 声明1个wire型输出变量y，其宽度为1位。

  assign  y = (~s&a)|(s&b);  // 实现电路的逻辑功能。

endmodule
```

生成的cpp代码

```cpp
//Vmux21.h

// Verilated -*- C++ -*-
// DESCRIPTION: Verilator output: Primary model header
//
// This header should be included by all source files instantiating the design.
// The class here is then constructed to instantiate the design.
// See the Verilator manual for examples.

#ifndef VERILATED_VMUX21_H_
#define VERILATED_VMUX21_H_  // guard

#include "verilated_heavy.h"

class Vmux21__Syms;
class Vmux21___024root;

// This class is the main interface to the Verilated model
class Vmux21 VL_NOT_FINAL {
  private:
    // Symbol table holding complete model state (owned by this class)
    Vmux21__Syms* const vlSymsp;

  public:

    // PORTS
    // The application code writes and reads these signals to
    // propagate new values into/out from the Verilated model.
    VL_IN8(&a,0,0);
    VL_IN8(&b,0,0);
    VL_IN8(&s,0,0);
    VL_OUT8(&y,0,0);

    // CELLS
    // Public to allow access to /* verilator public */ items.
    // Otherwise the application code can consider these internals.

    // Root instance pointer to allow access to model internals,
    // including inlined /* verilator public_flat_* */ items.
    Vmux21___024root* const rootp;

    // CONSTRUCTORS
    /// Construct the model; called by application code
    /// If contextp is null, then the model will use the default global context
    /// If name is "", then makes a wrapper with a
    /// single model invisible with respect to DPI scope names.
    explicit Vmux21(VerilatedContext* contextp, const char* name = "TOP");
    explicit Vmux21(const char* name = "TOP");
    /// Destroy the model; called (often implicitly) by application code
    virtual ~Vmux21();
  private:
    VL_UNCOPYABLE(Vmux21);  ///< Copying not allowed

  public:
    // API METHODS
    /// Evaluate the model.  Application must call when inputs change.
    void eval() { eval_step(); }
    /// Evaluate when calling multiple units/models per time step.
    void eval_step();
    /// Evaluate at end of a timestep for tracing, when using eval_step().
    /// Application must call after all eval() and before time changes.
    void eval_end_step() {}
    /// Simulation complete, run final blocks.  Application must call on completion.
    void final();
    /// Return current simulation context for this model.
    /// Used to get to e.g. simulation time via contextp()->time()
    VerilatedContext* contextp() const;
    /// Retrieve name of this model instance (as passed to constructor).
    const char* name() const;
} VL_ATTR_ALIGNED(VL_CACHE_LINE_BYTES);

#endif  // guard
```

```verilog
//Vmux.c
// Verilated -*- C++ -*-
// DESCRIPTION: Verilator output: Model implementation (design independent parts)

#include "Vmux21.h"
#include "Vmux21__Syms.h"

//============================================================
// Constructors

Vmux21::Vmux21(VerilatedContext* _vcontextp__, const char* _vcname__)
    : vlSymsp{new Vmux21__Syms(_vcontextp__, _vcname__, this)}
    , a{vlSymsp->TOP.a}
    , b{vlSymsp->TOP.b}
    , s{vlSymsp->TOP.s}
    , y{vlSymsp->TOP.y}
    , rootp{&(vlSymsp->TOP)}
{
}

Vmux21::Vmux21(const char* _vcname__)
    : Vmux21(nullptr, _vcname__)
{
}

//============================================================
// Destructor

Vmux21::~Vmux21() {
    delete vlSymsp;
}

//============================================================
// Evaluation loop

void Vmux21___024root___eval_initial(Vmux21___024root* vlSelf);
void Vmux21___024root___eval_settle(Vmux21___024root* vlSelf);
void Vmux21___024root___eval(Vmux21___024root* vlSelf);
QData Vmux21___024root___change_request(Vmux21___024root* vlSelf);
#ifdef VL_DEBUG
void Vmux21___024root___eval_debug_assertions(Vmux21___024root* vlSelf);
#endif  // VL_DEBUG
void Vmux21___024root___final(Vmux21___024root* vlSelf);

static void _eval_initial_loop(Vmux21__Syms* __restrict vlSymsp) {
    vlSymsp->__Vm_didInit = true;
    Vmux21___024root___eval_initial(&(vlSymsp->TOP));
    // Evaluate till stable
    int __VclockLoop = 0;
    QData __Vchange = 1;
    do {
        VL_DEBUG_IF(VL_DBG_MSGF("+ Initial loop\n"););
        Vmux21___024root___eval_settle(&(vlSymsp->TOP));
        Vmux21___024root___eval(&(vlSymsp->TOP));
        if (VL_UNLIKELY(++__VclockLoop > 100)) {
            // About to fail, so enable debug to see what's not settling.
            // Note you must run make with OPT=-DVL_DEBUG for debug prints.
            int __Vsaved_debug = Verilated::debug();
            Verilated::debug(1);
            __Vchange = Vmux21___024root___change_request(&(vlSymsp->TOP));
            Verilated::debug(__Vsaved_debug);
            VL_FATAL_MT("mux21.sv", 1, "",
                "Verilated model didn't DC converge\n"
                "- See https://verilator.org/warn/DIDNOTCONVERGE");
        } else {
            __Vchange = Vmux21___024root___change_request(&(vlSymsp->TOP));
        }
    } while (VL_UNLIKELY(__Vchange));
}

void Vmux21::eval_step() {
    VL_DEBUG_IF(VL_DBG_MSGF("+++++TOP Evaluate Vmux21::eval_step\n"); );
#ifdef VL_DEBUG
    // Debug assertions
    Vmux21___024root___eval_debug_assertions(&(vlSymsp->TOP));
#endif  // VL_DEBUG
    // Initialize
    if (VL_UNLIKELY(!vlSymsp->__Vm_didInit)) _eval_initial_loop(vlSymsp);
    // Evaluate till stable
    int __VclockLoop = 0;
    QData __Vchange = 1;
    do {
        VL_DEBUG_IF(VL_DBG_MSGF("+ Clock loop\n"););
        Vmux21___024root___eval(&(vlSymsp->TOP));
        if (VL_UNLIKELY(++__VclockLoop > 100)) {
            // About to fail, so enable debug to see what's not settling.
            // Note you must run make with OPT=-DVL_DEBUG for debug prints.
            int __Vsaved_debug = Verilated::debug();
            Verilated::debug(1);
            __Vchange = Vmux21___024root___change_request(&(vlSymsp->TOP));
            Verilated::debug(__Vsaved_debug);
            VL_FATAL_MT("mux21.sv", 1, "",
                "Verilated model didn't converge\n"
                "- See https://verilator.org/warn/DIDNOTCONVERGE");
        } else {
            __Vchange = Vmux21___024root___change_request(&(vlSymsp->TOP));
        }
    } while (VL_UNLIKELY(__Vchange));
}

//============================================================
// Invoke final blocks

void Vmux21::final() {
    Vmux21___024root___final(&(vlSymsp->TOP));
}

//============================================================
// Utilities

VerilatedContext* Vmux21::contextp() const {
    return vlSymsp->_vm_contextp__;
}

const char* Vmux21::name() const {
    return vlSymsp->name();
}
```







文件说明

.sv文件转换后

![image-20230310144954627](C:\Users\24811\Pictures\md图片\verilator\image-20230310144954627.png)

`Valu.h`这是包含转换后的“ALU”类定义的主要设计标头 - 这是我们将在 C++ 测试台中“实例化”为 DUT 的内容。

`Valu___024unit.h`这是“ALU”类的内部标头，它包含我们的` operation_t` 类型定义。





























