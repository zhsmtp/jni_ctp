纯JNI封装的CTP库，linux 64 bit， ctp是ctp_api_6.3.15_20190220的看穿生产版。


```
public class Main {
    static {
        System.loadLibrary("TdApiWrap");
        System.loadLibrary("MdApiWrap");
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
