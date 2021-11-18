# yodo1-android-sdk-manual


## 一. Yodo1 sdk Introduction
### 1.Domestic release background

- There are many domestic distribution channels, and the content and number of interfaces defined by each vendor varies. yodo1 sdk has made compatible adaptations and packaging for each vendor's sdk as far as possible. Each vendor has different requirements for the interfaces that must be accessed, and the return value types of the same meaningful interfaces may also be different. Developers are advised to focus on understanding this. Also game access factory, in order to be compatible with as many types of processing as possible, it is recommended to use as broad as possible interaction processing.
- Mandatory functions are those that must be accessed by the game and processed through the entire process. Features that are not marked as mandatory are not mandatory, and are either selected for access based on a specific vendor, or based on the type of cooperation with the vendor. In principle, accessing only mandatory features will guarantee a problem-free launch. Game developers are advised to access as many features as possible, depending on the in-game functionality. It is important to note that some features are simply packaged bridges and the exact implementation depends on whether the three party vendor already supports them. It is also recommended in development to handle post-call processing as broadly as possible.
- If the game has no advertising business, the advertising functionality can be ignored altogether. If the game has no in-app payment requirements, the iap function can be ignored altogether. Non-mandatory features can also be implemented by the game itself.
- The key point is that all games released in China, regardless of type and source, must be connected to the anti-addiction system. The second point is that all games must be legally compliant, improve the game's privacy document and user agreement document. yodo1 sdk has developed a general and broad agreement content, if the game has special permission data acquisition and other operations, please explain clearly in the privacy document.
        
### 2. yodo1 sdk structure and interface description
- The yodo1 sdk is divided into three modules, yodo1 channels, yodo1 ads, and yodo1 anti-addiction. The game accesses the bridge.aar of the three modules' functions and it needs to be packaged again by the yodo1 backend system before it can online. Please contact us for this part of the operation.
- There are three types of interfaces to call: those with a return value, those without a return value and without callbacks, and those without a return value and with callbacks.
  - Those with a return value: these are the simplest interfaces to call, returning the required data without delay and without interaction.
  - No return value without callback: such interfaces are generally information reporting interfaces, even if designed with callback data can be ignored to deal with.
  - No return value with callbacks: the mandatory interface is generally this type of interface, encapsulating the data type required for the interface to be called. In the registered dependencies, the business type is determined and the business logic is processed.
- ***As the yodo1 sdk is derived from the company's internal sdk, which mainly uses the Unity engine, there is still a lot of Unity naming and calling rules in the code. Note that the gameObject and callbackName parameters are currently free to be named when called, and the same gameObject and callbackName can be determined in the callback first.*** 
### 3. Related bridge file(Please contact our developers to request)
- yodo1_ChannelBridge.aar
- mas-unity-bridge.aar
- anti-unity-bridge.aar

## 二. Related configuration and introduction of initialization

### 1.manifest configuration
- Many components are configured to be integrated into the game through indirect dependencies. The upper level of the game requires the configuration of several components as shown below.
Examples are as follows.
```JAR Manifest
<! -1, the game must be configured with application name or indirectly inheritance to implement "com.yodo1.android.sdk.Yodo1Application" --> 
<application
            android:name="com.yodo1.android.sdk.Yodo1Application"
            android:hardwareAccelerated="true"
            android:isGame="true"
            android:largeHeap="true"
            android:networkSecurityConfig="@xml/network_security_config"
            android:requestLegacyExternalStorage="true"
            android:theme="@style/UnityThemeSelector"
            android:usesCleartextTraffic="true">

<! -2, customize YODO1_MAIN_CLASS, for the main Activity of the game --> 
        <meta-data
                android:name="YODO1_MAIN_CLASS"
                android:value="com.yodo1.plugin.u3d.Yodo1UnityActivity"/>
 
<! -3, the game must be configured with com.yodo1.android.sdk.view.SplashActivity, other configurations can be customized by the game, SplashActivity must be the app launcher component"--> 
        <activity android:name="com.yodo1.android.sdk.view.SplashActivity">
            <intent-filter
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
 

<! -4, direct copy-paste"-->
<meta-data
        android:name="Yodo1ChannelCode"
        android:value="Yodo1ChannelCode_value"
        tools:replace="android:value"/>
<meta-data
        android:name="Yodo1SDKVersion"
        android:value="Yodo1SDKVersion_value"
        tools:replace="android:value"/>
<meta-data
        android:name="Yodo1SDKType"
        android:value="Yodo1SDKtype_value"
        tools:replace="android:value"/>
</application>  
```
### 2.sdk introduction
configure the dependency in gradle, and type in the three aar files.
```Gradle
     api fileTree(include: ['*.jar','*.aar'], dir: 'libs')
or
     implementation(name: 'anti-unity-bridge', ext:'aar')
     implementation(name: 'mas-unity-bridge', ext:'aar')
     implementation(name: 'yodo1_ChannelBridge', ext:'aar')
```
Life cycle dependent calls, where the game accessor needs to inject life api calls into each declaration cycle in the main game Activity.
```Java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    Yodo1BridgeUtils.onActivityCreated(this, savedInstanceState);
    Yodo1BridgeUtils.gamesdk().onCreate(this);
}

@Override
protected void onStart() {
    super.onStart();
    Yodo1BridgeUtils.gamesdk().onStart(this);
    Yodo1BridgeUtils.onActivityStarted(this);
}

@Override
protected void onResume() {
    super.onResume();
    Yodo1BridgeUtils.gamesdk().onResume(this);
    Yodo1BridgeUtils.onActivityResumed(this);
}

@Override
protected void onRestart() {
    super.onRestart();
    Yodo1BridgeUtils.gamesdk().onRestart(this);
    Yodo1BridgeUtils.onActivityRestart(this);
}

@Override
protected void onPause() {
    super.onPause();
    Yodo1BridgeUtils.gamesdk().onPause(this);
    Yodo1BridgeUtils.onActivityPause(this);
}

@Override
protected void onStop() {
    super.onStop();
    Yodo1BridgeUtils.gamesdk().onStop(this);
    Yodo1BridgeUtils.onActivityStopped(this);
}

@Override
protected void onDestroy() {
    super.onDestroy();
    Yodo1BridgeUtils.gamesdk().onDestroy(this);
    Yodo1BridgeUtils.onActivityDestroyed(this);
}

@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    Yodo1BridgeUtils.gamesdk().onNewIntent(this, intent);
    Yodo1BridgeUtils.onActivityNewIntent(this, intent);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    Yodo1BridgeUtils.gamesdk().onActivityResult(this, requestCode, resultCode, data);
    Yodo1BridgeUtils.onActivityResult(this, requestCode, resultCode, data);
}


@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    Yodo1BridgeUtils.gamesdk().onRequestPermissionsResult(this, requestCode, permissions, grantResults);
    Yodo1BridgeUtils.onActivityRequestPermissionsResult(this, requestCode, permissions, grantResults);
}

@Override
public void onSaveInstanceState(Bundle outState, PersistableBundle outPersistentState) {
    super.onSaveInstanceState(outState, outPersistentState);
    Yodo1BridgeUtils.onActivitySaveInstanceState(this, outState);
}

@Override
public void onConfigurationChanged(Configuration configuration) {
    super.onConfigurationChanged(configuration);
    Yodo1BridgeUtils.onActivityConfigurationChanged(this, configuration);
}
```
### 3. sdk initialisation 
- The yodo1 sdk contains three separate modules, which are initialised separately. If there are no special circumstances, do not change the order of initialisation.
```Java
/**
* gameSdk initialisation
*/
Yodo1GameSDK.initSDK("appKey","regionCode");
//or
Yodo1GameSDK.initSDK("appKey");
/**
* Ad initialization
*/
UnityYodo1Mas.initSDK(activity,'appkey');
/**
* Anti-addiction initialization. See the Anti-addiction chapter initialisation section for code details
*/
Yodo1AntiAddiction.init(activity,'appkey',....);
```
- Register the callback bus and all callbacks will return data from the callback bus：
```Java
//Arg1 and agr2 are the gameobject and callbackname, you can define by your-self and in callback you use them to judge which method it is . 
//game sdk callback
Yodo1GameSDK.setCallBack(new IYodo1CallBack() {
    @Override
    public void callBackParameter(String arg1, String arg2, String jsonParameter) {
        //some eg:
      if ("userAccount".equals(arg1)) {
            if ("login".equals(arg2)) {
                //login
            } else if ("logout".equals(arg2)) {
                //logout
            } else if ("openAchivement".equals(arg2)) {
                //openAchive
            }
        } else if ("purchase".equals(arg1)) {
            if ("queryMissOrder".equals(arg2)) {
                //queryMissOrder
            } else if ("purchase".equals(arg2)) {
                //purchase
            } else if ("queryProductInfo".equals(arg2)) {
                //queryProductInfo
            }
        } else if ("analytics".equals(arg1)) {
            if ("report".equals(arg2)) {
                //
            }
        }
    }
});


//ads sdk callback
Yodo1MasAdapter.setCallback(new IYodo1CallBack() {
    @Override
    public void callBackParameter(String arg1, String arg2, String jsonParameter) {
        //some
    }
});


//anti sdk callback
Yodo1AntiAdapter.setCallback(new IYodo1CallBack() {
    @Override
    public void callBackParameter(String arg1, String arg2, String jsonParameter) {
        //some
    }
});
```
## 三. Access to account functions
### 1.Login function - (***mandatory***) , must be called in the main thread
```Java
/**
 * Login   you can get channelCode from method Yodo1GameUtils.getPublishChannelCode();
 *
* @param loginType   0: channel login; 1: device login. No special cases use 0 (Now if channel code is 4399ad,4399dd,233XYX2, need to go device login)Subject to change, contact * the developer when accessing
 * @param extra  Normal is empty. Currently there is only one special case, the TXYYB channel, 1 for WeChat login and 2 for QQ login.
 * @param gameObjcet
 * @param callback
 */
Yodo1UserCenter.login(loginType,extra,gameobject,callback);
```
> The data structure of the callback is.
>{"data": {"playerId":"864166039790427","userId":"5e57a5420c14952e1e3ab774","nickName":"864166039790427","level":1,"age":0,"gender":0,"thirdpartyChannel":0},"resulType":3001,"error_code":0,"code":1} 
### 2.Submit Player Information - (***mandatory***)
After a successful login, the game processes the upload to the sdk and channel sdk according to its own logic, robust to the logic that follows.
```Java
/**
* [{"playerId":"864166039790427","userId":null,"nickName":"864166039790427","level":1,"age":0,"gender":0,"gameServerId":null,"thirdpartyUid":null,"thirdpartyToken":null,
 * "thirdpartyChannel":0,"partyid":0,"partyname":null,"partyroleid":0,"partyrolename":null,
 * "power":0,"type":"enterServer","roleCTime":0}]
*/
Yodo1UserCenter.sumbitUser(json);
```
### 3. check if you are logged in
```Java
/**
* Check if you are logged in
*/
boolean isloogin = Yodo1UserCenter.isLogin();
```
### 4. Logout function
```Java
/**
* Logout
*/
Yodo1UserCenter.logout(gameObject,callbackName);
```
The data structure of the callback is.
//???
### 5. Switching accounts
```Java
/**
* Switching accounts
*/
Yodo1UserCenter.changeAccount(gameObject,callbackName);
```
The data structure of the callback is the same as the callback when logging in.

### 6.open the leaderboard
```Java
/**
* Open the leaderboard. Currently only googlePlay,appleStore and a few other channels have this feature
*/
Yodo1UserCenter.leaderboardsOpen();
```
### 7.open the achievement list
```Java
/**
* Open the achievement list. Currently only googlePlay, appleStore and a few other channels have this function
*/
Yodo1UserCenter.achievementsOpen(); 
```
### 8.Update player scores
```Java
/**
* Update player scores, currently only googlePlay, appleStore, huawei and a few other channels have this feature
*/
Yodo1UserCenter.updateScore(String scoreId, long score);
```
### 9.Achievement level unlocking and getting level
```Java
/**
* Achievement level unlocking
*/
Yodo1UserCenter.achievementsUnlock(String achievementStr, int step);
/**
* Get the achievement level
*/
Yodo1UserCenter.getAchievementSteps(String gameObjcetName, String callbackName).
```

### 10.Cloud Archive
```Java
/**
* Cloud archive, currently only a few channels such as googlePlay,appleStore have this function
*/
Yodo1UserCenter.saveToCloud(String saveName, String saveValue);
/**
* Get the cloud archive data
*/
Yodo1UserCenter.loadToCloud(String name, String gameObjcetName, String callbackName);
```
###  11.Game recording
```Java
/**
* Whether recording is supported, currently only GooglePlay is supported
*/
boolean isCan = Yodo1UserCenter.isCaptureSupported();
/**
* Show saved game footage
*/
Yodo1UserCenter.showRecordVideo();
```
## IV. Anti-addiction access
### 1.Initialising the anti-addiction system - (***mandatory***)
Before logging in, the anti-addiction system must be initialised. If the initialisation of the anti-addiction system fails, exit the game or call the initialisation again.
```Java
/**
 * @param activity       Activity
 * @param appKey         App key
 * @param extraSettings  Extra Settings,nullable
 */
Yodo1AntiAddiction.Init(Activity activity, String appKey, String extraSettings, String gameObjectName, String callbackName);
```
The data structure of the callback is.
Initialize onInitFinish, state true success, false failure.
> Callback success:{\"result_type\":8001,\"state\":true,\"content\":\"初始化成功\"}

Player timeout onTimeLimitNotify, event_action==0 continue game, event_action==1 end game; event_code:11001 for played-time notification-minors,12001 for no-play-time notification-minors,11011 for played-time notification-visitors ,12011 For Notification of No Play Time - Visitor,50005 Generated by the third party channel SDK.
> Callbacks: eg:{\"result_type\":8005,\"event_action\":0,\"event_code\":\"连接异常\",\"title\":\"连接异常\",\"content\":\"玩家未成功上线结束游戏\"}
- Flow Chart:
![流程图：](https://github.com/Yodo1-OPP/yodo1-android-sdk-manual/blob/main/Image/flow.png)

### 2.Real Name Authentication - (***mandatory***)
After successful login, call the real name authentication interface.
```Java
/**
 * @param accountId Account id, a unique id, either the playId given after successful login, or the deviceId (you can get it by Yodo1GameUtils.getDeviceId()).
 */
Yodo1AntiAddiction.VerifyCertificationInfo(String accountId, String gameObjectName, String callbackName);
```
The data structure of the callback is
> Authentication success：{\"result_type\":8003,\"event_action\":0}
### 3.Players log in and log off - (***mandatory***)
The token for calculating the player's accumulated hours. sdk already handles the application level declaration cycle internally, the game only needs to handle the time period defined as the accumulated hours in its own game logic.
```Java
/**
 * Player is log in
 */
Yodo1AntiAddiction.Online(String gameObjectName, String callbackName);
/**
 * Player is log off
 */
Yodo1AntiAddiction.Offline(String gameObjectName, String callbackName);
```
The data structure of the callback is.
> Online or offline success.{\"result_type\":8006,\"state\":true,\"content\":\"online offline成功\"}
### 4.Payment verification - (mandatory with Iap)
Payment verification for anti-addiction is mandatory when initiating a payment.
/**
 * @param price Product price, the price of the product the player wants to buy, in cents.
 * @param currency Product currency, the currency symbol, set to "CNY".
 */
Yodo1AntiAddiction.VerifyPurchase(double price, String currency, String gameObjectName, String callbackName);
The data structure of the callback is.
Anti-addiction payment amount is not limited to.{\"result_type\":8004,\"state\":true,\"content\":\"VerifyPurchase不限制\"}
### 5.Goods shipped on the report - (with Iap must access)
 After the purchase is successful and the game accepts the merchandise. In addition to the call to notify the success of the shipment interface, you also need to call the anti-addiction shipment reporting interface.
 ```Java
/**
 * Report product receipt information.
 *
 * @param receipt report json data
 */
Yodo1AntiAddiction.ReportProductReceipt(String receipt);
```
## V.commodity purchase function access (with Iap then must access)
### 1.billing point configuration and billing point hosting
The iap commodity billing points for in-game goods are configured and placed separately in the project. Most of the channel packages, through the xml configuration that hit the package, to get the latest information about the price, name, description of the goods. One of the channels, Xiaomi, Huawei, googlePlay, etc., uses a billing point hosted on the channel server to get information about the product, or to initiate payment, based on the corresponding productId.
        IapConfig_sample.xls
        The access point collects statistics on all products in the game, fills in an excel sheet and uploads it to the yodo1 system. For billing points that need to be hosted, the operations staff will edit the format required for each channel and upload it to open. Game development is treated equally.
### 2.Query all product information - (***mandatory***)
Generally done at the end of the game's real name authentication, but can also be requested in the game lobby and before commodity information is needed.
```Java
/**
 * Request product information. The game displays the product price, name, description, etc. based on the billing point file.
 */
Yodo1Purchase.requestProductsData(String gameObjcet, String callback);
```
The data structure of the callback is.
> {"resulType":2003,"code":1,"data":[{"productId":"iap_double_coin_1","productName":" Double the coins ","price":1,"description":" Double your in-game coins for 24 hours after purchase","priceDisplay":"¥1.0","currency":"CNY","coin":1,"paytime":0,"expiresTime":0,"autoRenewing":true,"discount":0,"ProductType":1},{"productId":"iap_lucky_lamb_combination_china_rmb9","productName":" Lucky Lamb Gift Pack","price":9,"description":" Get a lucky lamb and a medium share of gold coins after purchase","priceDisplay":"¥9.0","currency":"CNY","coin":6000,"paytime":0,"expiresTime":0,"autoRenewing":true,"discount":0,"ProductType":0},]}
### 3.Check for missed orders - (***mandatory***)
Usually done after the end of the game real name authentication, but also in the game lobby and before the need for product information, request to get.
```Java
/**
 * Request product information. The game displays the product price, name, description, etc. based on the billing point file.
 */
Yodo1Purchase.requestProductsData(String gameObjcet, String callback);
```
The data structure of the callback is.
> //???
### 4.Query for subscribed products
This is usually done after the game real name authentication is finished, but can also be requested in the game lobby and before the product information is needed.
```Java
/**
 * Request product information. The game displays the product price, name, description, etc. based on the billing point file.
 */
Yodo1Purchase.requestProductsData(String gameObjcet, String callback);
```
The data structure of the callback is.
> //???
### 5.get the specified product
```Java
/**
 * Request products information based on id
 */
Yodo1Purchase.requestProductsDataById(String productId, String gameObjcet, String callback);
```
The data structure of the callback is.
> {"resulType":2003, "code":1, "data":[{"productId": "iap_open_door", "productName": "Open Door Double", "price":0.1, "description": "Get double the business revenue for the current open door after purchase"," priceDisplay":"¥0.1", "currency": "CNY", "coin":1, "paytime":0, "expiresTime":0, "autoRenewing":true, "discount":0, "ProductType":1}]}
### 6.Resuming a purchase
Players who have been interrupted in a previous purchase process and have not completed payment can resume the purchase and continue the player's intent. This is generally accessed when entering the game lobby.
```Java
/**
 * Resume purchase. Currently only googleplay channels are supported.
 */
Yodo1Purchase.restorePurchases(String gameObjcetName, String callbackName);
```
The data structure of the callback is.
> //???
7. item purchase - (***mandatory***)
Only one item is purchased at a time, and a second purchase can be initiated only after the purchase has been initiated. The result of the purchase is all failure except success, because the definition of errorCode varies from channel to channel, all types of error access as much as possible unified in the else for interactive processing.
```Java
/**
 * Request product information. The game displays the product price, name, description, etc. based on the billing point file.
 */
Yodo1Purchase.purchase(String productId, String gameObjcet, String callback);
//or
Yodo1Purchase.purchase(String productId, double discount, String gameObjcet, String callback);
```
The data structure of the callback is.
> cancel payment: eg: {"resulType":2001, "code":2, "orderId": "O20210811154101YK2FBCL", "payType":3, "uniformProductId": "iap_unlock_map_point_5"}
> Payment success: eg: {"resulType":2001, "code":1, "orderId": "O202108111552375YEFDLR", "payType":3, "uniformProductId": "iap_unlock_map_point_5"," extra":{"channelOrderId": "O202108111552375YEFDLR", "currency": "CNY"}}
### 8.Send Shipping Success Notification - (***mandatory***)
After a successful purchase, call the shipping success notification interface. The function is to sound the purchase process and serve as a statistical basis for lost orders.
```Java
/**
 * Send shipping success notification
 */
Yodo1Purchase.sendGoods(String[] orders);
//or
Yodo1Purchase.sendGoods(String orders);
```
The data structure of the callback is.
> // The call has a callback, which can be ignored.      

### 9.Send Shipping Failure Notification - (***mandatory***)
After a purchase failure, the shipping failure notification interface is called. The function is to sound the purchase process and serve as a statistical basis for lost orders.
```Java
/**
 * Send shipment failure notification
 */
Yodo1Purchase.sendGoodsFail(String orders);
//or
Yodo1Purchase.sendGoodsFail(String[] orders);
```
The data structure of the callback is.
> // The call has a callback, which can be ignored.

## VI.advertising function access (***if there is an ad then it’s mandatory***)
In general, the game can be accessed for inserts, video ads.
### 1.Initialisation - (mandatory)
Called in the main Activity onCreate.
```Java
/**
 * initialise
 */
UnityYodo1Mas.initSDK(Activity activity, String appKey);
```
### 2.Video Ads
```Java
/**
 * Video ads are displayed. Determine videoIsReady before displaying. handle the reward after the ad.
 */
UnityYodo1Mas.showVideo(String gameObject, String callbackName);
```
The data structure of the callback is.
> //playback complete: {"resulType":1003, "code":1}
```Java
UnityYodo1Mas.videoIsReady(String gameObject, String callbackName);
```
> //it will return a bool value
### 3.Interstitial ads
```Java
/**
 * Interstitial ads. Determine interstitialIsReady before displaying. handle the reward after the ad.
 */
UnityYodo1Mas.showInterstitial(String gameObject, String callbackName) ;

UnityYodo1Mas.interstitialIsReady().
```
The data structure of the showInterstitial callback is.
> //playback complete: {"resulType":1002, "code":1}      

### 4.Original Ads
```Java
/**
 * Show the original ad. native ads. Determine nativeIsReady before showing. handle rewards after the ad.
 *
 * @param px The X coordinates for native ads
 * @param py The Y coordinates for native ads
 * @param pw The Width of native ads
 * @param ph The height of native ads
 */
UnityYodo1Mas.showNativeAd(String gameObject, float px, float py, float pw, float ph, String callbackName);
UnityYodo1Mas.nativeIsReady();
/**
 * Remove native ad, Remove original ad.
**/
UnityYodo1Mas.removeNativeAd();
```
The data structure of the showNativeAd callback is.
> // play complete: {"resulType":1005, "code":1}
### 5. banner ad
```Java
/**
 * Show banner ad. determine bannerIsReady() before displaying. Handle the reward after the ad.
 *
 */
UnityYodo1Mas.showBanner(String gameObject, String callbackName);
UnityYodo1Mas.bannerIsReady();
/**
 * Remove banner ad, Remove banner ad.
**/
UnityYodo1Mas.removeBanner();
/**
 * hide banner ad, Remove banner ad.
**/
UnityYodo1Mas.hideBanner();
```
The data structure of the showBanner callback is.
> // play complete: {"resulType":1001, "code":1}
### 6.Grand Carousel function
Give players rewards by showing the configured Grand Carousel interface method.
```Java
/**
 * Show the Big Spin function. Determine if it is enabled before calling. isRewardGameEnable
 */
UnityYodo1Mas.showRewardGame(String gameObject, String callbackName);
UnityYodo1Mas.isRewardGameEnable();
```
The data structure of the showRewardGame callback is.
> //play completion: {"resulType":1007, "code":1}      

### 7.Configure EEA/GDPR/COPPA/NotSell tagging
```Java
/**
 * SDK requires that publishers set a flag indicating whether a user located in the European Economic Area (i.e., EEA/GDPR data subject) has provided opt-in consent for the  collection and use of personal data.
 */
UnityYodo1Mas.setUserConsent(boolean consent);
/**
 * To ensure COPPA, GDPR, and Google Play policy compliance, you should indicate whether a user is a child.
 *
 * @param underAgeOfConsent true, If the user is known to be in an age-restricted category (i.e., under the age of 16), false otherwise.
 */
UnityYodo1Mas.setTagForUnderAgeOfConsent(boolean underAgeOfConsent);
/**
 * Publishers may choose to display a "Do Not Sell My Personal Information" link. Such publishers may choose to set a flag indicating whether a user located in California, USA has opted to not have their personal data sold.
 *
 * @param doNotSell true, If the user has opted out of the sale of their personal information, false otherwise.
 */
UnityYodo1Mas.setDoNotSell(boolean doNotSell);
```
## VII.Statistical Function Access
### 1.Sending custom events
```Java
/**
 * Customised event reporting,eg: [Guide_Frist_Jump, {"default":"1"}]
 *
 * @param event
 * @param jsonData
 */
Yodo1Analytics.customEvent(String event, String jsonData);
Yodo1Analytics.onCustomEvent(String event, String jsonData) ;
Yodo1Analytics.onTrack(String eventName);
Yodo1Analytics.submitTrack(String eventName) ;
```
### 2.Recharge related - specific events
```Java
/**
 * Props recharge order initiation
 *
 * @param orderId       orderorderID
 * @param itemId Billing point      productId.
 * @param currencyAmount      Amount of money
 * @param currencyType
 * @param virtualCurrencyAmount
 * @param paymentType
 */
Yodo1Analytics.onRechargeRequest(String orderId, String itemId, double currencyAmount, String currencyType, double virtualCurrencyAmount, int paymentType);
Yodo1Analytics.onChargeRequest(String orderId, String itemId, double currencyAmount, String currencyType, double virtualCurrencyAmount, int paymentType);
Yodo1Analytics.onPurchase(String itemId, int buyNum, double gameCoin);
//
Yodo1Analytics.onRechargeSuccess(String orderId);
Yodo1Analytics.onChargeSuccess(String orderId);
//
Yodo1Analytics.onRechargeFail(String orderId);
Yodo1Analytics.onChargeFail(String orderId);
//
Yodo1Analytics.onPurchanseGamecoin(String itemId, int buyNum, double gamecoin);
```

### 2.User-related - specific events
```Java
Yodo1Analytics.onGameReward(double gamecoin, int trigger, String reason);

Yodo1Analytics.validateInAppPurchase(String publicKey, String signature, String purchaseData, String price, String currency);

Yodo1Analytics.onMissionBegion(String missionId);

Yodo1Analytics.onMissionCompleted(String missionId);

Yodo1Analytics.onMissionFailed(String missionId, String cause);

Yodo1Analytics.onReward(double gameCoin, int trigger, String reason);

Yodo1Analytics.createRole(String roleName);

Yodo1Analytics.setPlayerLevel(int level);

Yodo1Analytics.onValidateInAppPurchase(String publicKey, String signature, String purchaseData, String price, String currency);

Yodo1Analytics.onMissionCompleted(String missionId);
```
## VIII.Community sharing function access
### 1.Call to share
```Java
Yodo1SNSTypeNone(-1),

Yodo1SNSTypeTencentQQ(1 << 0), //Friends
Yodo1SNSTypeWeixinMoments(1 << 1), //Friends

Yodo1SNSTypeWeixinContacts(1 << 2), //Chat interface

Yodo1SNSTypeSinaWeibo(1 << 3), //Sina Weibo

Yodo1SNSTypeFacebook(1 << 4), //Facebook

Yodo1SNSTypeTwitter(1 << 5), //Twitter

Yodo1SNSTypeInstagram(1 << 6), //Instagram

Yodo1SNSTypeAll(1 << 7); //all platforms share
```
This can be calculated using a programmer's computer, or manually bit-calculated in decimal.
All platforms = 127 or 128
Facebook + Twitter+ Instagram = 112
Friends, qq controls, WeChat, Weibo = 15
```Java
/**
 * Share
 *
 * @param shareObj [Yodo1U3dSDK, Yodo1U3dSDKCallBackResult, {"snsType":11, "title":"\u5206\u4eab\u6807\u9898", "desc":"\u5206\u4eab\u5185\ u5bb9\u63cf\u8ff0", "image":"/storage/emulated/0/share_test_image.png", "url": "https://www.baidu.com",
"qrLogo": "AppIcon.png", "qrText":"\u957f\u6309\u8bc6\u522b", "qrTextX":0, "qrImageX":0, "gameLogo": "sharelogo.png", "gameLogoX":0," composite":true}]
 */
Yodo1Share.share(String gameObject, String callbackName, String shareObj);
```
The data structure of the callback is.
> //????
## IX, In-app notification access
### 1.Call the notification bar
```Java
/**
 * Set up notifications and turn them on
 *
 * @param notificationKey
 * @param notificationId
 * @param alertTime
 * @param title
 * @param msg
 */
Yodo1PushNotification.pushNotification(String notificationKey, String notificationId, String alertTime, String title, String msg);
```
### 2.Remove notification bar notifications
```Java
/**
 * Cancellation notice
 *
 * @param notificationKey
 * @param notificationId
 */
Yodo1PushNotification.cancelNotification(String notificationKey, String notificationId);
```
## X.Others
### 1. Exiting the game - (***mandatory***)
The game is to be accessed when it triggers an exit. A successful callback will allow the actual execution of the exit code or suicide.
```Java
/**
 * Quit the game
 */
Yodo1GameUtils.exit(String gameObject, String callbackName);
```
The data structure of the callback is.
> //eg: exit successful : {"code":1}
### 2.get device id, SIM, country code, language, version number, etc.
```Java
/**
 * Get the device id
 */
Yodo1GameUtils.getDeviceId();
/**
 * Get the current system region
 */
Yodo1GameUtils.getCountryCode();
/**
 * Get the sim card of the device
 *
 * @return no card : 0 cmcc : 1 ct : 2 cu : 4
 */
Yodo1GameUtils.getSIM();
/**
 * Get the version number
 */
Yodo1GameUtils.getVersion();
/**
 * Determine if it is Mainland China.ext is empty.
 */
Yodo1GameUtils.IsChineseMainland(String ext);
```
### 3.Get current channels
```Java
/**
 * Gets the current channel number.
 */
Yodo1GameUtils.getPublishChannelCode();
````
### 4.open the web page
```Java
/**
 * Open a three-way browser
 */
Yodo1GameUtils.openReviewPage(String url);
/**
 * Open an in-app web page
 */
Yodo1GameUtils.openWebPage(String url, String valuePaireJson);
```
### 5.in-app cache
In-memory storage for easy execution of data during app survival and for easy data sharing between the game engine and native layer.
```Java
/**
 * Save
 */
Yodo1GameUtils.saveToNativeRuntime(String key, String valuePaireJson);
/**
 * Get
 */
Yodo1GameUtils.getNativeRuntime(String key);
```
### 6.Querying and accessing the privacy agreement and user agreement - (***mandatory for using yodo1 rules***)
yodo1's internally configured privacy agreement, user agreement distribution interface.
```Java
/**
 * Query and fetch privacy protocols and user agreements. Generally optional to be called after initialization.
 */
Yodo1GameUtils.queryUserAgreementHasUpdate(final String gameObject, final String callbackName);
/**
 * Get the user user agreement link. yodo1 configure queryUserAgreementHasUpdate to return.
 */
Yodo1GameUtils.getTermsLink().
/**
 * Get link to user privacy policy. yodo1 configure queryUserAgreementHasUpdate returned.
 */
Yodo1GameUtils.getPolicyLink().
/*
 * Overseas privacy policy popup box. googlePlay, used by appleStore overseas channel, provides age selection function.
 */
Yodo1GameUtils.showUserPrivateInfoUI(final String gameObject, final String callbackName, String appKey)
```
> The data structure of the queryUserAgreementHasUpdate callback is.
> {"resulType":8002, "data":{"is_update":true, "child_age_limit":16, "url_terms": "https:\/\/gamepolicy.yodo1.com\/terms_of_Service_zh. html", "url_privacy": "https:\\/\/privacy.mi.com\/xiaomigame-sdk\/zh_CN\/"}}
### 7.Activation code verification
Generate some activation codes in bulk online for in-game rewards, reissues and other scenarios.
```Java
/**
 * Activation code verification
 */
Yodo1GameUtils.verifyActivationcode(String activationCode, final String gameObject, final String callbackName);
```
The data structure of the callback is.
> //?
### 8.More games(***mandatory***)
Some domestic channels support more games to facilitate promotional pulling. Get higher recommendations.
```Java
/**
 * More games, before calling, determine whether the function is available, domestic oppo super casual channel must have this option
 */
Yodo1GameUtils.moreGame();
Yodo1GameUtils.hasMoreGame();
```
### 9.Game Community
Some domestic channels support more games to facilitate the promotion of new pull. Get higher recommendations.
```Java
/**
 * Open the game community. Before calling, determine if the function is available
 */
Yodo1GameUtils.openCommunity();
Yodo1GameUtils.hasCommunity() ;
```
### 10.Game forums
Some domestic channels support more games and facilitate promotion to pull new ones. Get higher recommendation.
```Java
/**
 * Open the game forum
 */
Yodo1GameUtils.openBBS();
/**
 * Open the channel comment feedback screen
 */
Yodo1GameUtils.openFeedback();
```
### 11.Developer markup
```Java
/**
 * Get online parameters, value distribution. Easy to do cloud control
 */
Yodo1GameUtils.getConfigParameter(String key);
/**
 * Get the meta-data value in androidmanifest.
 */
Yodo1GameUtils.getMetaValueForKey(String keysumbitUser);
Yodo1Analytics.getStringParams();
Yodo1Analytics.getBoolParams();
/**
 * Used to display a dialog box.
 */
Yodo1GameUtils.showAlert(String title,String message,String positiveButton,String negativeButton,String neutralButton,String objectName, String callbackMethod);
/**
 * Open the developer log. Same function.
 */
Yodo1GameUtils.setDebug(boolean isDebug);
```
