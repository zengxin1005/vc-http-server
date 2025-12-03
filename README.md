# vc-http-server
VC HttpServer 是一个使用原生 C/C++ 及 Win32 API 构建的高性能、轻量级 HTTP 服务器库。
旨在为 VC++ 桌面应用（如 MFC、Qt 程序）提供简洁、易嵌入的 Web 接口方案。

✅ 纯原生无依赖：不依赖任何第三方库，直接利用 Windows 系统特性。

⚡ 高性能设计：基于http.sys内核驱动和 Windows IOCP 完成端口模型开发，异步IO，高并发，资源占用低。  

🔌 易于嵌入：提供简洁 API，最小化配置，可轻松为现有 VC++ 桌面应用添加 Web 接口。

🏭 生产就绪：代码健壮，日志完善 ，适用于实际生产环境。

支持MSVC 9.0及以上编译器版本

IOCP层：真正的异步非阻塞
业务处理层：半同步半异步（线程池+阻塞队列）

类proactor模式

多接收线程 + 多工作线程 高并发，高吞吐量，IO异步非阻塞

接收请求1 → 接收请求2 → 接收请求3 → ...

    ↓           ↓           ↓
处理业务1 → 处理业务2 → 处理业务3 → ...

    ↓           ↓           ↓  
消费线程: 发送1 → 发送2 → 发送3 → ...（可选）

✅ 完整的异步架构 - IOCP + 线程池

✅ HTTP/HTTPS支持 - 自动证书配置

✅ Keep-Alive连接管理 - 高性能连接复用

✅ 轻量配置 - 最小化配置开箱即用

✅ 优雅关闭 - 完整的停止序列，等待线程退出

✅ 易于使用 - 简单的消息处理器接口

✅ 健壮的错误处理，线程安全的消息处理

✅ 基础安全防护，基础日志监控


不支持静态文件下载，静态文件下载请使用Nginx/Apache等，目前仅支持GET,POST, 更适合于业务逻辑处理，内部微服务调用！！！！


最小化配置，可拓展   

// HTTPS证书配置

struct ServerConfig {

	std::wstring url;
	
	bool enableHttps;
	
	std::wstring certSubjectName;  // 证书主题名
	
	std::wstring certStoreName;    // 证书存储名
	
	DWORD certCheckFlags;          // 证书检查标志
	
};

static const DWORD COMPLETION_THREAD_COUNT = 1;  // 完成端口线程数

static const DWORD PROCESS_THREAD_COUNT = 1;  // 处理线程数

static const DWORD MAX_CONNECTIONS = 10000; // 最大连接数

// Keep-Alive 常量配置

static const DWORD KEEP_ALIVE_TIMEOUT = 15;        // 15秒超时

static const DWORD CONNECTION_CLEANUP_INTERVAL = 30; // 30秒清理一次

// 优雅关闭相关常量

static const DWORD SHUTDOWN_TIMEOUT = 30000;       // 30秒关闭超时

static const DWORD SHUTDOWN_CHECK_INTERVAL = 100;  // 100ms检查间隔

使用示例

#include "HttpThread.h"

CHttpThread m_pHttpServer; 

//初始化Http

if (!m_pHttpServer.Initialize(L"http://+:80/")) 
{

	return FALSE;
	
}

//初始化Https

ServerConfig config;

config.url = L"https://+:443/";

config.enableHttps = true;

config.enableHttps = false;

config.certStoreName = L"MY";  // 默认在"个人"存储中查找

config.certCheckFlags = 0;

if (!m_pHttpServer.Initialize(config))

{

	return FALSE;
	
}


//开始监听

if (!m_pHttpServer.Start()) 
{

	return FALSE;
	
}

//关闭结束

m_pHttpServer.Stop();
