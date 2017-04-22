* MCU模式. 优点是：控制简单方便, 无需时钟和同步信号。缺点是：要耗费GRAM, 所以难以做到大屏（QVGA以上）。
  * I8080总线: CS#, RS, RD#, WR#, DATA(8/9/16/18/24)
  * M6800总线: CS#, RS, R/W#, EN, DATA(8/9/16/18)
* VSYNC模式. 在MCU模式上加了一个VSYNC信号, 应用于运动画面更新
* RGB模式. 大屏采用较多. VSYNC, HSYNC, DOTCLK, CS, RESET, (RS), DATA(6/16/18/24)
* SPI模式. 连线少但是软件控制比较复杂
* MDDI模式. 取代SPI模式. host_data,host_strobe,client_data,client_strobe,power,GND
* DSI模式. 串行的双向高速命令传输模式. D0P，D0N，D1P，D1N，CLKP，CLKN

MCU接口和RGB接口主要的区别是：
* MCU接口方式：显存在LCD模块内, 然后通过专门命令来更新显存, 常用于静止图片显示。
* RGB接口方式：显存是由系统内存充当的, LCD-DMA会自动把显存中的数据通过RGB口送到LCM, 速度快, 常用于显示视频或动画用。
