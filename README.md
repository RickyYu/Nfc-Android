# Nfc-Android
Android Nfc技术使用详解，具体详情可查看[文章地址](http://www.jianshu.com/p/e89cc9bba8a6)

NFC是Near Field Communication缩写，即`近距离无线通讯技术`。可以在移动设备、消费类电子产品、PC 和智能控件工具间进行近距离无线通信。简单一点说，nfc功能是什么？nfc功能有什么用？其实NFC提供了一种简单、触控式的解决方案，可以让消费者简单直观地交换信息、访问内容与服务。`NFC技术允许电子设备之间进行非接触式点对点数据传输`，在十厘米(3.9英吋)内，交换数据，其传输速度有106Kbit/秒、212Kbit/秒或者424Kbit/秒三种。接下来我们更加详细的来了解一下nfc的应用



* 由飞利浦公司和索尼公司共同开发的NFC是一种非接触式识别和互联技术
                                 
 ![](http://upload-images.jianshu.io/upload_images/5443336-d3a5aa356f50cd57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 一、nfc是什么，它的工作模式？
NFC工作模式主要有三种工作模式，分别是卡模式(Card emulation)、点对点模式(P2P mode)和读卡器模式(Reader/writer mode)。
    
#### （1）读卡器模式

        数据在NFC芯片中，可以简单理解成“刷标签”。本质上就是通过支持NFC的手机或其它电子设备从带有NFC芯片的标签、贴纸、名片等媒介中读写信息。通常NFC标签是不需要外部供电的。当支持NFC的外设向NFC读写数据时，它会发送某种磁场，而这个磁场会自动的向NFC标签供电

#### （2）仿真卡模式
        数据在支持NFC的手机或其它电子设备中，可以简单理解成“刷手机”。本质上就是将支持NFC的手机或其它电子设备当成借记卡、公交卡、门禁卡等IC卡使用。基本原理是将相应IC卡中的信息凭证封装成数据包存储在支持NFC的外设中 。在使用时还需要一个NFC射频器（相当于刷卡器）。将手机靠近NFC射频器，手机就会接收到NFC射频器发过来的信号，在通过一系列复杂的验证后，将IC卡的相应信息传入NFC射频器，最后这些IC卡数据会传入NFC射频器连接的电脑，并进行相应的处理（如电子转帐、开门等操作）。

#### （3）点对点模式

        该模式与蓝牙、红外差不多，用于不同NFC设备之间进行数据交换，不过这个模式已经没有有“刷”的感觉了。其有效距离一般不能超过4厘米，但传输建立速度要比红外和蓝牙技术快很多，传输速度比红外块得多，如过双方都使用Android4.2，NFC会直接利用蓝牙传输。这种技术被称为AndroidBeam。所以使用androidBeam传输数据的两部设备不再限于4厘米之内

  NFC、蓝牙和红外之间的差异
![](http://upload-images.jianshu.io/upload_images/5443336-a02d3bde35fa7d5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 二、Android对NFC的支持
不同的NFC标签之间差异很大，有的只支持简单的读写操作，有时还会采用支持一次性写入的芯片，将NFC标签设计成只读的。当然，也存在一些复杂的NFC标签，例如，有一些NFC标签可以通过硬件加密的方式限制对某一区域的访问。还有一些标签自带操作环境，允许NFC设备与这些标签进行更复杂的交互。这些标签中的数据也会采用不同的格式。但Android SDK API主要支持NFC论坛标准（Forum Standard），这种标准被称为NDEF（NFC Data Exchange Format，NFC数据交换格式）。

NDEF格式其实就类似于硬盘的NTFS，下面我们看一下NDEF数据：

#### （1）NDEF数据的操作
Android SDK API支持如下3种NDEF数据的操作：

* 1）从NFC标签读取NDEF格式的数据
* 2）向NFC标签写入NDEF格式的数据
* 3）通过Android Beam技术将NDEF数据发送到另一部NFC设备

用于描述NDEF格式数据的两个类：

* 1）NdefMessage：描述NDEF格式的信息，实际上我们写入NFC标签的就是NdefMessage对象。
* 2）NdefRecord：描述NDEF信息的一个信息段，一个NdefMessage可能包含一个或者多个NdefRecord。

NdefMessage和NdefRecord是Android NFC技术的核心类，无论读写NDEF格式的NFC标签，还是通过Android Beam技术传递Ndef格式的数据，都需要这两个类。

#### （2）非NDEF数据的操作
对于某些特殊需求，可能要存任意的数据，对于这些数据，我们就需要自定义格式。这些数据格式实际上就是普通的字节流，至于字节流中的数据代表什么，就由开发人员自己定义了。

#### （3）编写NFC程序的基本步骤
* 1）设置权限，限制Android版本、安装的设备：

  ![](http://upload-images.jianshu.io/upload_images/5443336-02ea26579ecb2e80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 2）定义可接收Tag的Activity，配置一下launchMode属性,处理NFC的Activity都要设置launchMode属性为singleTop或者singleTask，保证activty唯一实例

  ![](http://upload-images.jianshu.io/upload_images/5443336-6d589d8e59e8083d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 三、实战使用NFC标签
#### (1)利用NFC标签让Android自动运行程序
场景是这样的：现将应用程序的包写到NFC程序上，然后我们将NFC标签靠近Android手机，手机就会自动运行包所对应的程序，这个是NFC比较基本的一个应用。下面以贴近标签自动运行Android自带的“短信”为例。

向NFC标签写入数据一般分为三步：

* 1）获取Tag对象

  ![](http://upload-images.jianshu.io/upload_images/5443336-13ca866b33394118.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 2）判断NFC标签的数据类型（通过Ndef.get方法）
* 3）写入数据

  ![](http://upload-images.jianshu.io/upload_images/5443336-06b126d1bf203a48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

操作步骤,实际运行效果如下:

点击自动打开短信界面或百度页面，进入对应Activity，将NFC标签贴近手机背面，提示写入成功。返回主界面，再将NFC标签贴近手机背面，便能看到自动打开短信或者打开百度页面。

* 应用主界面:

  ![](http://upload-images.jianshu.io/upload_images/5443336-6e799fa3a8598b77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 数据写入界面:

  ![](http://upload-images.jianshu.io/upload_images/5443336-5b65998645199bc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 扫描NFC标签后跳转页面

  ![](http://upload-images.jianshu.io/upload_images/5443336-ac73588141ca844a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 四、NDEF文本格式深度解析

获取NFC标签中的数据要通过 NdefRecord.getPayload 方法完成。当然，在处理这些数据之前，最好判断一下NdefRecord对象中存储的是不是NDEF文本格式数据。

#### (1)判断数据是否为NDEF格式
* 1）TNF（类型名格式，Type Name Format）必须是NdefRecord.TNF_WELL_KNOWN。
* 2）可变的长度类型必须是NdefRecord.RTD_TEXT。

如果这两个标准同时满足，那么就为NDEF格式

#### (2)NDEF文本格式规范
不管什么格式的数据本质上都是由一些字节组成的。对于NDEF文本格式来说，这些数据的第1个字节描述了数据的状态，然后若干个字节描述文本的语言编码，最后剩余字节表示文本数据。这些数据格式由NFC Forum的相关规范定义，可以通过 http://members.nfc-forum.org/specs/spec_dashboard 下载相关的规范。

 下面这两张表是规范中 3.2节 相对重要的翻译部分：
 
 * NDEF文本数据格式
 
  ![](http://upload-images.jianshu.io/upload_images/5443336-4ca1bae404f85a49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 状态字节编码格式

  ![](http://upload-images.jianshu.io/upload_images/5443336-8507380e27ef4496.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实现NFC标签中的文本数据的读写操作：

* 写文本数据核心代码，具体可查阅项目中代码WriteTextActivity类

  ![](http://upload-images.jianshu.io/upload_images/5443336-24f1eaf321c6bfbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 读文本数据核心代码，具体可查阅项目中代码ReadTextActivity类

  ![](http://upload-images.jianshu.io/upload_images/5443336-bc192aaa34e5d5af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击写NFC标签中的文本数据，跳转到对应的Activity，将NFC标签卡贴近手机，写入成功后会提示，再退回主页面，选择读文本数据，便可读取。

* 数据写入界面

  ![](http://upload-images.jianshu.io/upload_images/5443336-caffd6bf63a3cae2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 数据读取界面

  ![](http://upload-images.jianshu.io/upload_images/5443336-d1e503f2dda83c40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 五、NDEF Uri格式存储（操作步骤如上图，具体实现可查阅项目代码）

### 六、非NDEF格式（操作步骤如上图，具体实现查阅项目代码）

       MifareUltralight数据格式：将NFC标签的存储区域分为16个页，每一个页可以存储4个字节，一个可存储64个字节（512位）。页码从0开始（0至15）。前4页（0至3）存储了NFC标签相关的信息（如NFC标签的序列号、控制位等）。从第5页开始存储实际的数据（4至15页）。使用MifareUltralight.get方法获取MifareUltralight对象，然后调用MifareUltralight.connect方法进行连接，并使用MifareUltralight.writePage方法每次写入1页（4个字节）。
       也可以使用MifareUltralight.readPages方法每次连续读取4页。如果读取的页的序号超过15，则从头开始读。例如，从第15页（序号为14）开始读。readPages方法会读取14、15、0、1页的数据。


* 此为本次测试所使用设备，手机：小米5  NFC标签卡：淘宝可购买

  ![](http://upload-images.jianshu.io/upload_images/5443336-35393841d4ae6d61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)