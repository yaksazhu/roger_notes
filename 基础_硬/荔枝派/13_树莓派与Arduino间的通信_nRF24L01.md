2017-04-10 一 晴

#1 方案选择

| 方案             | 优缺点        |
| :--              | :--           |
| nRF24L01+（RF）  | 综合较好      |
| ESP8266（WIFI）  | 功耗高        |
| XBee (ZigBee)    | 最优,但成本高 |
| ENC28J60（LAN）  | 不方便        |
| W5100,W5500(LAN) | 不方便        |
| 蓝牙             | 距离短        |

故考虑RF方案，成本和效果平衡较好。
nRF24L01+价格便宜（5块左右，做工好带天线的15左右），编程简单，
且存在能同时支持树莓派、Arduino、Linux的RF24库。
Git连接为：https://github.com/TMRh20/RF24.git

#2 连接
nRF24L01引脚图
| net  | #    | #    | net  |
| :--  | :--: | :--: | :--  |
| IRQ  | 8    | 7    | MISO |
| MOSI | 6    | 5    | SCK  |
| CSN  | 4    | 3    | CE   |
| Vcc  | 2    | 1    | GND  |
> 注: Vcc=3.3v, 切不可接5V!!!

#3 代码 & 运行
RF24库中自带的GettingStarted例子非常方便，其代码包含发送端和接收端两种类型，
默认为接受模式，输入T时切换为发送，输入R则切为接受模式，并有简单的超时判断。
为了易于理解，可简单修正代码，让接收端返回一自增数字。
## 3.1 Arduino
1）RF24库安装
从(https://github.com/TMRh20/RF24.git)下载RF24后，将其复制到Arduino安装目录下的libraries目录下，启动ArduinoIDE后,从例子中选择`RF24->GettingStarted`。
2）代码修改
发送端不必修改，直接编译上传即可。（注意UNO和Mega的选择和串口选择）
接收端将代码中的radioNumber从默认的0修改为1。如下:
```
bool radioNumber = 0; （自身为2Node，发送给1Node）
  ->
bool radioNumber = 1;（自身为1Node，发送给2Node）
```
简言之，1Node为接收端，2Node为发送端。
建议：原代码中的got_time不易观察理解，
可将接收端中的got_time发送前赋值为一静态可增计数值。
3）运行
发送端启动后，输入T，使其进入发送模式。
接受端启动即可，无需输入R。（默认为R接收模式）
如上述配置接线正常，可在Serial Monitor中看到发送方和接收方的输出.
## 3.2 树莓派
本文中使用的树莓派为 16年新发布的RPi3 B型
1）RF库安装
将RF24库复制到树莓派（或通过git直接获取）。进入RF24目录后执行如下命令，进行编译和安装（选择SPI方式）
```bash
./configure --driver=SPIDEV
sudo make install -B
```
2） 修改系统配置
```
修改/etc/modprobe.d/raspi-blacklist.conf，如果其中存在
blacklist spi-bcm2708，将其注释。

修改/etc/modules文件，在其中追加一行，开启SPI。
spidev
```
reboot重启树莓派后，/dev下会新增spidev0.0和spidev0.1两个设备文件。
3）代码修改
修改`RF24/example_linux/GettingStarted.cpp`文件，
同上面的Arduino一样，发送端不必修改，
接收端将radioNumber从默认的0修改为1，并建议吧回送的时戳数据改为自增数字。
在当前目录下执行make后，生成GettingStarted的二进制文件。

4）运行
使用`sudo ./GettingStarted`执行，并输入0进入接收模式。
如Arduino的发送端配置、运行正常，则正常发送回应包。

#4 注意&体会
便宜版本的nRF24L01效果一般，很容易受到干扰。
带天线的会好些，真做项目不可图便宜。
接线要准确，SPI要理解下原理。CE、CSN其实是可以任意指定的，只是要修改下RF24的初始化代码。

#5 RH24例子代码简单说明
以下是RH24（TMRh20）自带的Arduino例子，简单说明一下，
树莓派上为C语言实现的版本，变量、语法略有区别，但逻辑是基本一致的。
##5.1 变量定义
```C
bool radioNumber = 1;  // RF节点名称决定Flag

/* Hardware configuration: Set up nRF24L01 radio on SPI bus plus pins 7 & 8 */
// 指定CE用GPIO7，CSN用GPIO8，需要和接线一致
// 如接线不采用7,8，代码这里需要修改。
RF24 radio(7, 8);

byte addresses[][6] = {"1Node", "2Node"}; // 两个节点名

bool role = 0; // 发送&接收模式Flag
```
##5.2 初始化函数
```C
void setup()
{
	Serial.begin(115200);
	Serial.println(F("RF24/examples/GettingStarted"));
	Serial.println(F("*** PRESS 'T' to begin transmitting to the other node"));

	radio.begin();
	radio.setPALevel(RF24_PA_LOW);

	// Open a writing and reading pipe on each radio, with opposite addresses
	if(radioNumber){
		radio.openWritingPipe(addresses[1]);
		radio.openReadingPipe(1,addresses[0]);
	}else{
		radio.openWritingPipe(addresses[0]);
		radio.openReadingPipe(1,addresses[1]);
	}

	// 默认为监听模式，开始监听
	radio.startListening();
}
```
##5.3 执行逻辑
```C
void loop()
{
	/****************** Ping Out Role ***************************/
	if (role == 1)  { // 发送模式
		radio.stopListening();  // 发送数据前要停止监听
		Serial.println(F("Now sending"));

		unsigned long start_time = micros(); // 待发送的时戳

		// RF24内部会自动处理payload和发送数据不等长的问题
		if (!radio.write( &start_time, sizeof(unsigned long) )){ // 发送数据
			Serial.println(F("failed"));
		}

		radio.startListening(); // 数据发送完，需要监听回应数据的到来

		unsigned long started_waiting_at = micros();
		boolean timeout = false;

		while ( ! radio.available() ){  // 超时判断
			if (micros() - started_waiting_at > 200000 ){
				timeout = true;
				break;
			}
		}

		if ( timeout ){
			Serial.println(F("Failed, response timed out."));
		}
		else { // 读数据并显示数据和间隔时间
			unsigned long got_time;
			radio.read( &got_time, sizeof(unsigned long) );
			unsigned long end_time = micros();

			// Spew it
			Serial.print(F("Sent "));
			Serial.print(start_time);
			Serial.print(F(", Got response "));
			Serial.print(got_time);
			Serial.print(F(", Round-trip delay "));
			Serial.print(end_time-start_time);
			Serial.println(F(" microseconds"));
		}

		// Try again 1s later
		delay(1000);
	}

	/****************** Pong Back Role ***************************/
	if ( role == 0 )  { // 接收模式
		static long count = 1;  // 自增计数
		unsigned long got_time = 0;

		if( radio.available()){
			while (radio.available()) { // 读数据
				radio.read( &got_time, sizeof(unsigned long) );
			}

			got_time = count++;  // 为便于理解，回送自增计数值
			radio.stopListening();
			radio.write( &got_time, sizeof(unsigned long) ); // 写回应
			radio.startListening();
			Serial.print(F("Sent response "));
			Serial.println(got_time);
		}
	}

	/****************** Change Roles via Serial Commands ***************************/
	if ( Serial.available() )  { // 发送&接收模式通过串口决定
		char c = toupper(Serial.read());
		if ( c == 'T' && role == 0 ){  // 发送模式
			Serial.println(F("*** CHANGING TO TRANSMIT ROLE -- PRESS 'R' TO SWITCH BACK"));
			role = 1;
		}
		else
		  if ( c == 'R' && role == 1 ){  //接受模式
			  Serial.println(F("*** CHANGING TO RECEIVE ROLE -- PRESS 'T' TO SWITCH BACK"));
			  role = 0;
			  radio.startListening();
		  }
	}

} // Loop
```
#6 参考
详细参考（英语）：
http://arduino-info.wikispaces.com/Nrf24L01-2.4GHz-HowTo

其他人的成功经验：
http://www.cnblogs.com/hangxin1940/archive/2013/05/01/3053467.html
http://www.cnblogs.com/hangxin1940/archive/2013/05/01/3048315.html
注意：其所使用的RF24库并不相同

转载自 http://blog.csdn.net/ydogg/article/details/53307365
