
## 背景 ##
做到一个项目从施耐德电力系统采集水电气数据到app数据库。虽然合作伙伴和供应商有类似的工具，但是为了troubleshooting 方便利用业余时间看了一下opc classic 的原理和实现。这套协议基于微软COM来架构和实现，于是又延伸阅读了指针接口CLSID,PROGID 以及dll二进制层级的oo 复用。因为运行时的依赖非常干净，所以即使到了这个标准已经过期16年的今天还是能在工业和电力SCADA 看到它。并且弄清楚这里面的弯弯绕还蛮简单的。

## C++ + COM 实现client ##
我不知道怎么从网上下载了一个迄今为止没找到源代码的dll：opcdaAuto.dll,又搜索了到了为数不多的利用Cpp和VB 调用dll 的说明和代码，没想到还真跑通了。调用隐藏在dll 里的COM 架构需要一个native 桌面Cpp 程序
+ COM Init
+ 搜索或者创建COM 层已经注册的server
+ 利用server 的指针拿到group
+ 通过group Add 特定的点位存取Tag或者叫做Item

### COM Init ###

'''
using namespace std;
//声明全局变量
typedef struct OLEInit{
	OLEInit() { CoInitialize(NULL); }
~OLEInit() { CoUninitialize(); }
} OLEInit;
OLEInit oleInit; // 必须在最前面定义，因为在使用COM之前必须初始化COM库,否则程序会崩溃

'''

### 创建作为COM 对象的 server ###

'''
	HRESULT hr;
	hr = opcSvr.CreateInstance(__uuidof(OPCServer));
	if (FAILED(hr)) {
		cerr << "OPCServer CreateInstance failed, hr = " << hr << endl;
		exit(1);
	}

'''

### 拿到group ###

'''
	opcGrps = opcSvr->OPCGroups;
	IOPCGroupPtr ConnectedGroup = opcGrps->Add(_variant_t("group0")); // 组名随意取

'''

### 存取Tag/Item ###
'''
	ConnectedGroup->SyncRead(OPCDevice,
		3, // 读取的Item数目
		&pServerHandles, // 输入的服务器端句柄数组
		&pValues, // 输出的Item值数组
		&pErrors, // 输出的Item错误状态数组
		&quality, // 读取的值的状态
		&timestamp); // 读取的事件戳

'''

在group 这个函数的前面还需要在内存空间里建立这样一个数据结构：

'''
	vector<OPCItemPtr> opcReadItms;
	vector<_bstr_t>
	 itemAsStringArray = { "Saw-toothed Waves.Int4" ,"Saw-toothed Waves.Int2", "Bucket Brigade.String" };
	for (i = 0; i < itemAsStringArray.size(); i++) {
		OPCItemPtr opcItm;
		opcItm = ConnectedGroup->OPCItems->AddItem(itemAsStringArray[i], 1);
		opcReadItms.push_back(opcItm);
	}

	for (i = 0; i < opcReadItms.size(); i++) {
		svrHdl = opcReadItms[i]->ServerHandle;
		dim[0] = i + 1;
		// 给数组的每个元素赋值，对应的索引值是1， 2， 3
		SafeArrayPutElement(pServerHandles, dim, &svrHdl);
	}


'''

这附近的常量字符串就是点位名。
- "Saw-toothed Waves.Int4" 
- "Saw-toothed Waves.Int2"
- "Bucket Brigade.String"

## 下一步 ##
说实话我对这个dll 一点也不懂只是按照例子去调通了read only 这个路径。
最好能够遍历一下服务器。
例如美剧一下它底下所有的group 以及每个group 底下的所有点位。
而不是我来给出上面的字符串。
？
如果有类似于opcEnum.exe 那样的服务器枚举工具，针对Item 版本的有吗。
如果做一个Installer 程序，能方便发布和部署。