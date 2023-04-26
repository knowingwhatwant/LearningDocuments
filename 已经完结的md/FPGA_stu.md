Verliog HDL

大小写敏感，所有关键字为小写，分号声明结束符，单行注释，时须规范用于仿真

```verilog
//基本结构模型
module module_name(port_list);
    //端口声明
    //数据类型声明
    //电路功能
    //时须规范
endmodule
```

数据类型

nets数据类型和寄存器数据类型

| 类型                 | 定义             |
| -------------------- | ---------------- |
| wire                 | 结点或者连接     |
| tri                  | 三态节点         |
| supply0<br />supply1 | 逻辑0<br />逻辑1 |

```verilog
wire[7:0] out;
tri enable;
```

寄存器是——寄存器、整数、实数、时间、实时
只能在**进程声明**、任务或者功能中赋值reg类型变量
不能是逻辑门输出，或者assign语句的输出  

```verilog
reg[7:0] out;
integer count;
```

输入输出规则表

| Variable type | input | output | inout |
| ------------- | ----- | ------ | ----- |
| net           | Y     | Y      | Y     |
| register      | N     | Y      | N     |

赋值——数字

```verilog
sized或者unsized

<size>'<base format><number>
3'b010   //三维宽二进制数字
123		//默认的32位宽度十进制数字

没有指定<size>默认32位宽度数字
没有指定的<base format>默认为decimal十进制

//基本格式
//十进制
16'd255
//十六进制
8'h9a
//二进制
'b1010		//三十二位宽度二进制数字
//八进制
'o21		//三十二位宽度八进制数字
```

负数——在<size>前面加上负号表示

`-8'd3  //8位负数，存储为2'的补码3`

特殊字符

**下划线**用于可读性

’x'或'X'(unknown)

> 12'h12x   =  12位十六进制数字；LSB未知

'z' or 'Z'(高阻抗值)

> 1'bz = 位高阻抗数字

运算

任一操作数为Z或者X，那么结果未知
如果操作结果和操作数大小相同，则进位丢失
负数以2的补码格式存储。但是当用在表达式中时，解释为无符号值

位运算

| 算子     | 运算 | 例子 |
| -------- | ---- | ---- |
| ~        | 取反 |      |
| &        | AND  |      |
| \|       | OR   |      |
| ^        | Xor  |      |
| ^~ or ~^ | Xnor |      |

结果是最大操作数的大小
如果位宽不同，以零想左扩展

**缩位算子**

将矢量减为单比特

'X' or 'Z'被认为未知，但是结果可能是确定的

**关系算子**

返回1位标量值，bool
如果任意操作数是Z或X，则结果未知

**相等算子**

=== 条件相等



**逻辑算子**

！	非真
&&  所有表达式真
||	一个或所有表达式真



**移位算子**

bin=4’b10X0
bin>>1  =  4'b010X

填充0，移出的比特丢失

**其它算子**

`?:` 条件 

 `(condition)? true_val:fakse_val;`
`sig_out=sel==2'b01)?A:B`

`{}`连接

`{ain,bin}result `

`{3{2'b10}} =6'b101010`



**连续赋值**

`wire addr_out = mult_out + out`

等价于
`wire adder_out;`
`assign adder_out = mult_out + out`



**进程赋值模块**

**initial模块**

> 初始化仿真行为声明

**always**

> 使用行为声明，用于描述电路功能

每个模块代表不同进程
进程并行运行
进程中声明顺序执行
`always`和`initial`不能嵌套



**进程赋值**

**阻塞赋值(=)**

```verilog
initial
    befin
    	#5 a=b;
		#10 c=d;
end
```

**非阻塞赋值(<=)**

```verilog
initial
    befin
    	#5 a<=b;
		#10 c<=d;
end
//同时赋值
```



**使用进程模块的电路**

**组合电路**

> 对组合逻辑中使用的所有输入敏感

`always @(a or b or sel)`

**时钟电路**

> 对时钟以及控制信号敏感

`always @(posedge clk or negedge clr )`

> 时钟信号和干净信号，不知道干净信号是什么

 

**行为声明**

**IF-ELSE**

```verilog
if(<condition>)
    sequence of statement(s);
else
    if(<condition2>)
        sequence of statement(s)
            
 else
     .....
     
 //例子,多输入选择器
     always@(sela or selb or a or b or c)
         begin
             if(sela)
                 q=a;
             else
                 if(selb)
                     q=b;
             else 
                 q=c;
         end
            
```

 ![image-20220902142635601](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220902142635601.png)

**CASE**

```verilog
case(expression)
    <condition1>:
        sequence of statement(s)
    <condition2>:
            sequence of statement(s)
              //  ..
            //    ..
       default:
                sequence of statement(s)
endcase
```

```verilog
//例子，选择器
always@(sel or a or b or c or d)
    begin
        case(sel)
            2'b00:
                q=a;
            2'b01:
                q=b;
            2'b10:
                q=c;
            default:
                q=d;
        endcase
    end
```

![image-20220902143129439](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220902143129439.png)

**其它的case声明**

**casez**

> 认为'z'不重要不是逻辑值，可以用'?'表示

```verilog
casez(encoder)
    4'b1???:high_lvl=3;
    4'b01??:high_lvl=2;
    4'b001?:high_lvl=1;
    4'b0001:high_lvl=0;
    default:high_lvl=0;
endcase
```

**casex**

> 认为case条件下的所有'x'和'z'值不重要，不是逻辑值

```verilog
casez(encoder)
    4'b1xxx:high_lvl=3;
    4'b01xx:high_lvl=2;
    4'b001x:high_lvl=1;
    4'b0001:high_lvl=0;
    default:high_lvl=0;
endcase
```

**永远和重复循环**

**Forever循环**——不断执行

```verilog
//50个时间单位的时钟周期
initial
    begin
        clk=0;
        forever #25 clk=~clk;
    end
```

**Repeat循环**——执行一定次数

```verilog
if(rotate == 1)
    repeat(8)
        begin
            tmp = data[15];
            data = {data << 1,temp};
        end
```

**While循环**——表达式为真则执行

```verilog
initial
    begin
        count = 0;
        while(count < 101)
            begin 
                $display("Count = %d",count);
                count=count+1;
            end
    end
```

**For循环**

```verilog
//四位向左移位
integer i;	//declare the index for the FOR LOOP
always@(inp or cnt)
    begin
        result[7:4]=0;
        result[3:0]=inp;
        if(cnt == 1)
            begin
                for(i=4;i<=7;i=i+1)
                    begin
                        result[i] = result[i-4];
                        result[3:0]=0;
                    end
```

![image-20220902150746266](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220902150746266.png)

**D触发器例子**

**同步预置位和清零**

![image-20220902151847559](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220902151847559.png)

无预置和清零信号，但是有时钟使能信号

![image-20220902151936756](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220902151936756.png)



**功能计数器**

```verilog
module cntr(q,aclr,clk,func,d);
    input aclr,clk;
    input[7:0]d;
    input[1:0]func;//Controls the functionnality
    output[7:0]q;
    reg[7:0]q;
    always@(poseedge clk or poseedge aclr)begin
        if(aclr)			//进入计数器，检查异步清零信号
            q<=8'h00;		//高电平复位
        else				
            case(func)		//函数信号，控制实现什么信号
                2'b00:q<=d;
                2'b01:q<=q+1;
                2'b10:q<=q-1;
                2'b11:q<=q;
            endcase
    end
endmodule       
```

**Verilog HDL函数和任务**

函数：根据输入返回一个值，产生组合逻辑，用在表达式中
`assign mult_out=mult(ina,inb);`

任务：与其他语言的程序相似，可以是组合或者寄存，以声明的形式调用任务
`stm_out(nxt,first,sel,filter);`



**函数定义——乘法器**

```verilog
//乘法器，两个八位输入
function[15:0]mult;
    input[7:0]a,b;
    reg[15:0]r;
    integer i;
    begin 
        if(a[0]==1)
            r=b;
        else
            r=0;
        for(i=1;i<=7;i=i+1)
            begin
                if(a[i]==1)
                    r=r+(b<<i);
            end
        mult =r;//函数的最后操作一般是将函数值赋给函数名称
    end
endfunction
```

**乘法累加器**



| 函数                             | 任务                                     |
| -------------------------------- | ---------------------------------------- |
| 只能调用其它函数                 | 可以调用和函数和任务                     |
| 不能有延时、事件或者时序控制声明 | 可以含有延迟、事件或者时序控制声明       |
| 至少一个输入变量                 | 可以有0或者更多的输入、输出或者inout变量 |
| 总有一个返回值                   | 返回0或者更多的数值                      |



## 自动(递归)函数









**常用的RTL语法结构**

 模块声明
端口声明
信号类型
参数定义
参数定义
运算操作符
比较判断
连续赋值
always模块：(敏感表可以是电平、沿信号)
begin...end，就相当于大括号
任务定义：task...endtask
循环语句：for（使用较少

赋值符号：=和<=阻塞和非阻塞赋值，讲究



**实例1**

**与门逻辑**

```verilog
//name same with proj
module example_and(a,b,c);
//下面默认是wire型数据
input b; 
input c;
output a;

assign a = b&c;


endmodule
```



四位计数器，时钟控制，16进制计数，输出

```verilog
//name same with proj
module example_and(clk,rst_n,cnt);

input clk; 
input rst_n;
output[3:0] cnt ;		//4 bit
//wire can't be used in always
reg[3:0] cnt;//so define cnt as reg
//Timing logic
always@(posedge clk or negedge rst_n)//Asynchronous reset need "negedge rst_n"
begin
if(!rst_n) cnt<=4'd0;
else cnt <= cnt +1'b1;


end 

endmodule

```

**4位宽10进制计数器**

```verilog
module counter10(clk,rst_n,cnt,cout);
input rst_n;
    input clk;		//rst pos active
    output[3:0] cnt;	//clock
    output count;		//
    reg[3:0] cnt_temp;//计数器寄存器
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            cnt_temp <= 4'b0;
        end
        else if(cnt_temp==4'd9)begin//计时10个cycle
            cnt_temp <=4'b000;
        end
        else begin
            cnt_temp <=cnt_temp +1'b1;
        end
    end
    assign count = (cnt_temp==4'd9);//输出周期位
    assign cnt=cnt_temp;			//输出实时计时器
endmodule
    
    
    
```







![image-20220906134445917](C:\Users\24811\Pictures\md图片\msp430f5529\image-20220906134445917.png)





**输出任务**

`$display()`

`$write()`













net reg wire数据类型区别














