# 纯JNI封装的CTP库，Windows 64 bit和linux 64 bit

ctp是ctp_api_6.3.15_20190220的看穿生产版。

LINUX下面，在运行java之前，需要EXPORT LD_LIBRARY_PATH=/tmp/ctp/lib/:$LD_LIBRARY_PATH.
不然在wrap的库在寻找ctp库的时候，找不到～～～
```
public class Main {
	static {
		try {

			String templibpath = System.getProperty("java.io.tmpdir") + "ctp" + File.separator + "lib" + File.separator;
			Files.createDirectories(Paths.get(templibpath));

			String jarPathPrefix = "lib/win/";
			// load dll for windows
			String[] dll_entries = { "WinDataCollect.dll", "thostmduserapi_se.dll", "thosttraderapi_se.dll",
					"MdApiWrap.dll", "TdApiWrap.dll" };

			for (int i = 0; i < dll_entries.length; i++) {
				String entry = dll_entries[i];
				URL url = new URL("jar:file:ctp.jar!/" + jarPathPrefix + entry);
				File targetfile = new File(templibpath + entry);
				FileOutputStream out = new FileOutputStream(targetfile);
				InputStream ins = url.openStream();
				int byte_read;
				byte[] buffer = new byte[8192];
				while ((byte_read = ins.read(buffer,0,8192)) != -1) {
					out.write(buffer,0,byte_read);
				}
				out.flush();
				out.close();
				targetfile.deleteOnExit();
				System.out.println("copy : " + templibpath + entry);

			}
			for (int i = 0; i < dll_entries.length; i++) {
				String entry = dll_entries[i];
				System.out.println("load : " + templibpath + entry);
				System.load(templibpath + entry);
			}
		} catch (Exception e) {
			System.out.println(e.getMessage());
		}
	}
    
    public static void main(String[] args) {

        CThostFtdcMdApi mdapi = CThostFtdcMdApi.CreateFtdcMdApi("./con/md/", false, false);
        MyMdSpi mdspi = new MyMdSpi();
        mdapi.RegisterSpi(mdspi);
        mdapi.RegisterFront("tcp://180.168.146.187:10011"); // 电信 （看穿式前置，使用监控中心生产秘）
        mdapi.Init();
        Thread.sleep(1000);
        CThostFtdcReqUserLoginField req2 = new CThostFtdcReqUserLoginField();
        mdapi.ReqUserLogin(req2, 1);
        Thread.sleep(1000);
        System.out.println("TradingDay from MdApi = " + mdapi.GetTradingDay());
        String[] instruments = { "au1912" };
        Thread.sleep(1000);
        mdapi.SubscribeMarketData(instruments, 1);

    }
```  
