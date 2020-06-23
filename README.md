스마트 모바일 프로그래밍 ASSA 최종 보고서
===================================
목차   
-----


### 1.소개
>#### 1-1 주제선정이유
>#### 1-2 앱 개발중 사용한 기능
>#### 1-3 앱 개발로 얻는효과

### 2. 기능 구현
>#### 2-1 로그인 및 회원가입
>>##### 2-1-1 서버구축
>>##### 2-1-2 안드로이드 스튜디오와 서버연결
>>##### 2-1-3 카카오 로그인
>#### 2-2 Google Map
>>##### 2-2-1 지도상 내위치 표시
>>##### 2-2-2 지도상 대결상대 표시
>>##### 2-2-3 대결 가능 거리 설정
>#### 2-3 Android Studio에서 구현한 기능
>>##### 2-3-1 원형 프로필
>>##### 2-3-2 팝업창
>>##### 2-3-3 로그아웃
>>##### 2-3-4 수익성 측면
>#### 2-4 Unity를 이용한 게임
>>##### 2-4-1 고스톱 알고리즘
>>>###### 2-4-1-1 Player
>>>###### 2-4-1-2 AI
>>##### 2-4-2 특수효과
>>>###### 2-4-2-1 효과음
>>>###### 2-4-2-2 카드 효과(Card Hitting)
>>>>###### 2-4-2-2-1 보간 함수   




<hr/>   

## 1. 소개

### 1-1 주제선정이유
고스톱은 사행성 게임으로만 불려왔다. 하지만 게임이란 것은 전 연령이 즐길 수 있어야 진정한 게임이라 생각한다. 그렇기에 어린 연령층도 우리나라의 전통 놀이 중 하나인 고스톱을 즐기게 하기 위해 개발했다. 또한 게임만 하는 것이 아닌 위치에 따라 거리를 측정하며 진행하여 나날히 증가하는 비만율에 도움이 되고자 운동과 게임 두 가지를 잡는 방향으로 개발하여 게임을 하며 흥미를 느끼고 걷기를 하게 되게끔 하였다.   
![주제선정이유](https://user-images.githubusercontent.com/62593452/85231705-799f3f00-b434-11ea-9621-898667310757.png)   

### 1-2 앱 개발중 사용한 기능
지도, 카카오 서버를 이용한 간편로그인, 유니티를 이용한 게임 개발   

### 1-3 앱 개발로 얻는효과
연령에 구애받지 않는 고스톱앱의 개발로 인해 어린 연령층의 사용자도 우리나라 전통 놀이인 고스톱을 배울 수 있고 고스톱에 사용되는 화투는 알츠하이머 환자에게 일종의 두뇌 게임, 인지 훈련으로 실행되기도 하며 환자의 기억력 훈련에 도움이 된다고 한다.   
<hr/>

## 2. 기능 구현

### 2-1 로그인 및 회원가입
로그인 및 회원가입시 데이터저장을 위한 서버를 생성해야한다.
>#### 2-1-1 서버구축
무료 서버 호스팅 사이트인 dothome을 이용하여 ASSA 서버를 생성한다.
![assa서버](https://user-images.githubusercontent.com/62593452/85231105-781f4800-b42f-11ea-89a4-71158ffcdc17.PNG)

MySQL 관리 웹페이지로 들어가 회원가입시 기입하는 정보를 저장하는 테이블 생성한다.
![sql테이블](https://user-images.githubusercontent.com/62593452/85231107-79507500-b42f-11ea-92d9-59a3e70dccef.PNG)


>#### 2-1-2 안드로이드 스튜디오와 서버연결
Volley API 를 이용한 HTTP 통신을 위해 build.gradle파일에 implementation해준다.
~~~xml
implementation 'com.android.volley:volley:1.1.1'
~~~

안드로이드 스튜디오에서 서버로 정보를 전송하기위한 Login.php / Register.php 파일을 생성한다.   
##### Login.php
~~~php
    $con = mysqli_connect("localhost", "assa", "tkdaud1!", "assa");
    mysqli_query($con,'SET NAMES utf8');

    $userID = $_POST["userID"];
    $userPassword = $_POST["userPassword"];
    
    $statement = mysqli_prepare($con, "SELECT * FROM USER WHERE userID = ? AND userPassword = ?");
    mysqli_stmt_bind_param($statement, "ss", $userID, $userPassword);
    mysqli_stmt_execute($statement);


    mysqli_stmt_store_result($statement);
    mysqli_stmt_bind_result($statement, $userID, $userPassword, $userName, $userAge);

    $response = array();
    $response["success"] = false;
 
    while(mysqli_stmt_fetch($statement)) {
        $response["success"] = true;
        $response["userID"] = $userID;
        $response["userPassword"] = $userPassword;
        $response["userName"] = $userName;
        $response["userAge"] = $userAge;        
    }

    echo json_encode($response);
~~~

##### Register.php
~~~php
    $con = mysqli_connect("localhost", "assa", "tkdaud1!", "assa");
    mysqli_query($con,'SET NAMES utf8');

    $userID = $_POST["userID"];
    $userPassword = $_POST["userPassword"];
    $userName = $_POST["userName"];
    $userAge = $_POST["userAge"];

    $statement = mysqli_prepare($con, "INSERT INTO USER VALUES (?,?,?,?)");
    mysqli_stmt_bind_param($statement, "sssi", $userID, $userPassword, $userName, $userAge);
    mysqli_stmt_execute($statement);


    $response = array();
    $response["success"] = true;
 
   
    echo json_encode($response);
~~~

2개의 파일을 FileZilla를 이용하여 html폴더에 추가해준다.    
![fillzilla](https://user-images.githubusercontent.com/62593452/85231104-76558480-b42f-11ea-8bfa-6faaf3dd6ac2.PNG)   


>#### 2-1-3 카카오 로그인
카카오톡은 대한민국에서 가장 큰 sns로 우리나라의 국민 전부가 쓰고 있다고 해도 과언이 아닌 어플로, 카카오 로그인을 통하여 간편하게 어플을 즐길 수 있도록 하였다. 
카카오에 APP등록하기   
[kakao app등록 링크](https://developers.kakao.com/)   
![카카오톡등록](https://user-images.githubusercontent.com/62593452/85231709-7c019900-b434-11ea-98f0-0b163564188c.png)   
Buildgradle(app)파일에 카카오 로그인을 위한 sdk, url을 추가해준다.

~~~java

dependencies {
    implementation 'com.kakao.sdk:usermgmt:1.29.0'
}
~~~

~~~java
allprojects {
    repositories {

        flatDir {
            dirs 'libs'
        }
        google()
        jcenter()
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
    }
}
~~~

manifest파일에 카카오 로그인에 필요한 인터넷연결을 permission하고 AppKey와 네이티브 키를 입력한다.
~~~java
uses-permission android:name="android.permission.INTERNET"
~~~

~~~java
meta-data
    android:name="com.kakao.sdk.AppKey"
    android:value="네이티브 키"
~~~

카카오 로그인을 하기위해 해시 키를 생성 후 카카오 디벨로퍼에 입력해준다.
##### 해시 키 생성

~~~java
private void getAppKeyHash() {
    try {
        PackageInfo info = getPackageManager().getPackageInfo(getPackageName(), PackageManager.GET_SIGNATURES);
        for (Signature signature : info.signatures) {
            MessageDigest md;
            md = MessageDigest.getInstance("SHA");
            md.update(signature.toByteArray());
            String something = new String(Base64.encode(md.digest(), 0));
            Log.e("Hash key", something);
        }
    } catch (Exception e) {
        // TODO Auto-generated catch block
        Log.e("name not found", e.toString());
    }
}
~~~

##### logcat창에 나오는 HashKey
![해시키](https://user-images.githubusercontent.com/62593452/85231710-7c9a2f80-b434-11ea-8124-d118791b0e7b.png)    
##### 해시 키 등록
![해시키등록](https://user-images.githubusercontent.com/62593452/85231707-7ad06c00-b434-11ea-8227-65cfb3187d6c.png)     

## 주의사항
안드로이드 스튜디오를 통해 직접 스마트폰으로 빌드 할 경우 디버그 키를 사용하기 때문에 
카카오디벨로퍼에 앱을 등록한 개발자가 해시키를 등록해야만 로그인이 가능하다.
단, apk파일로 설치할 경우에는 릴리즈 키를 사용하기 때문에 위와 같은 과정이 필요없다. 
카카오 로그인을 하려면 app폴더의 release폴더의 ASSA_gostop.apk를 다운받아 사용해주세요.
![빌드 후 주의사항](https://user-images.githubusercontent.com/51045433/85376702-094b0780-b573-11ea-87a8-080dfc6d8b33.png)
안드로이드 스튜디오를 통해 직접 스마트폰으로 빌드 하고 싶다면 앱 개발자를 통해 해시키를 등록하는 과정을 거쳐야 한다.


>### 2-2 Google Map
Google Map을 기반으로 지도를 구현한다.    
Google Map에 엑세스하기 위해서는 최소 SDK레벨 2.0 이여야한다.   
1)Google console developer 사이트에 방문하여 새 프로젝트를 생성한다. 
![1](https://user-images.githubusercontent.com/62593452/85274012-55397600-b4b9-11ea-9238-02ee0d58d6bb.png)
![2](https://user-images.githubusercontent.com/62593452/85274017-55d20c80-b4b9-11ea-9f43-df94531c21bb.png)
![3](https://user-images.githubusercontent.com/62593452/85274020-55d20c80-b4b9-11ea-8d8a-d5347e500a69.png)

2)생성 후 Google Maps JavaScript API를 선택 후 사용설정 클릭한다.
![4](https://user-images.githubusercontent.com/62593452/85274022-566aa300-b4b9-11ea-80de-5077f149c8d6.png)

3)사용자 인증 정보 만들기 클릭 -> 만든 구글 프로젝트로 설정한 후 API키 발급한다. 
![5](https://user-images.githubusercontent.com/62593452/85274023-566aa300-b4b9-11ea-9a2b-8c6ed9d12c78.png)

4)AndroidManifest.xml파일에 추가해준다.
~~~java
 meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="API키"
~~~  

3)Module app에 GooglePlayServices를 사용하기 위한 코드를 추가해준다.

~~~java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    implementation 'com.google.android.gms:play-services-maps:17.0.0'
    implementation 'com.google.android.gms:play-services-location:17.0.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
~~~  

4)activity_main.xml layout파일에 <fragment>태그를 지도가 표시될 위치에 적어준다.

~~~java
    fragment
        android:id="@+id/map"
        android:name="com.google.android.gms.maps.SupportMapFragment"

        android:layout_width="match_parent"
        android:layout_height="match_parent"
~~~

지도를 포함하는 MainActivity.java파일에 코드를 추가해준다.
~~~java
import com.google.android.gms.ads.AdRequest;
import com.google.android.gms.ads.AdSize;
import com.google.android.gms.ads.AdView;
import com.google.android.gms.ads.MobileAds;
import com.google.android.gms.ads.RequestConfiguration;
import com.google.android.gms.ads.initialization.InitializationStatus;
import com.google.android.gms.ads.initialization.OnInitializationCompleteListener;
import com.google.android.gms.common.ConnectionResult;
import com.google.android.gms.common.api.GoogleApiClient;
import com.google.android.gms.location.LocationListener;
import com.google.android.gms.location.LocationRequest;
import com.google.android.gms.location.LocationServices;
import com.google.android.gms.maps.CameraUpdate;
import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.BitmapDescriptorFactory;
import com.google.android.gms.maps.model.CircleOptions;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.Marker;
import com.google.android.gms.maps.model.MarkerOptions;
import com.google.android.material.navigation.NavigationView;
public class MainActivity extends AppCompatActivity

        implements OnMapReadyCallback,
        GoogleApiClient.ConnectionCallbacks,
        GoogleApiClient.OnConnectionFailedListener,
        LocationListener {
private GoogleApiClient mGoogleApiClient = null;
private GoogleMap mGoogleMap = null;
private Marker currentMarker = null;
private static final String TAG = "googlemap_example";
private static final int GPS_ENABLE_REQUEST_CODE = 2001;
private static final int PERMISSIONS_REQUEST_ACCESS_FINE_LOCATION = 2002;
private static final int UPDATE_INTERVAL_MS = 1000;  // 1초
private static final int FASTEST_UPDATE_INTERVAL_MS = 500; // 0.5초

private AppCompatActivity mActivity;
boolean askPermissionOnceAgain = false;
boolean mRequestingLocationUpdates = false;
Location mCurrentLocatiion;
boolean mMoveMapByUser = true;
boolean mMoveMapByAPI = true;
LatLng currentPosition;
LocationRequest locationRequest = new LocationRequest()
        .setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY)
        .setInterval(UPDATE_INTERVAL_MS)
        .setFastestInterval(FASTEST_UPDATE_INTERVAL_MS);
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);


    getWindow().setFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON,
            WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
    setContentView(R.layout.activity_main);
Log.d(TAG, "onCreate");
mActivity = this;


    mGoogleApiClient = new GoogleApiClient.Builder(this)
            .addConnectionCallbacks(this)
            .addOnConnectionFailedListener(this)
            .addApi(LocationServices.API)
            .build();


    SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
            .findFragmentById(R.id.map);
    mapFragment.getMapAsync(this);

}
~~~   


>>#### 2-2-1 지도상 내위치 표시
1)내 기기의 위치를 지도상에 표시해주기 위해서 권한 요청을 요구하는 코드와 GPS 활성화를 위한 코드를 추가해준다.
~~~java
private void showDialogForLocationServiceSetting() {

    AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
    builder.setTitle("위치 서비스 비활성화");
    builder.setMessage("앱을 사용하기 위해서는 위치 서비스가 필요합니다.\n"
            + "위치 설정을 수정하실래요?");
    builder.setCancelable(true);
    builder.setPositiveButton("설정", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int id) {
            Intent callGPSSettingIntent
                    = new Intent(android.provider.Settings.ACTION_LOCATION_SOURCE_SETTINGS);
            startActivityForResult(callGPSSettingIntent, GPS_ENABLE_REQUEST_CODE);
        }
    });
    builder.setNegativeButton("취소", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int id) {
            dialog.cancel();
        }
    });
    builder.create().show();
}
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    switch (requestCode) {

        case GPS_ENABLE_REQUEST_CODE:

            //사용자가 GPS 활성 시켰는지 검사
            if (checkLocationServicesStatus()) {
                if (checkLocationServicesStatus()) {

                    Log.d(TAG, "onActivityResult : 퍼미션 가지고 있음");


                    if (mGoogleApiClient.isConnected() == false) {

                        Log.d(TAG, "onActivityResult : mGoogleApiClient connect ");
                        mGoogleApiClient.connect();
                    }
                    return;
                }
            }

            break;
    }
}
~~~  

2)위 코드를 작성하면 퍼미션 검사를 통해 GPS 활성화가 진행된다.   
3)Geocorder를 이용한 내 위치를 불러오는 코드와 나의 위치로 카메라 포지션을 변경해 주기 위한 코드를 MainActivity에 추가해준다.
##### Geocorder를 이용한 내위치 불러오기

~~~java
public String getCurrentAddress(LatLng latlng) {

    //지오코더... GPS를 주소로 변환
    Geocoder geocoder = new Geocoder(this, Locale.getDefault());

    List<Address> addresses;

    try {

        addresses = geocoder.getFromLocation(
                latlng.latitude,
                latlng.longitude,
                1);
    } catch (IOException ioException) {
        //네트워크 문제
        Toast.makeText(this, "지오코더 서비스 사용불가", Toast.LENGTH_LONG).show();
        return "지오코더 서비스 사용불가";
    } catch (IllegalArgumentException illegalArgumentException) {
        Toast.makeText(this, "잘못된 GPS 좌표", Toast.LENGTH_LONG).show();
        return "잘못된 GPS 좌표";
    }

    if (addresses == null || addresses.size() == 0) {
        Toast.makeText(this, "주소 미발견", Toast.LENGTH_LONG).show();
        return "주소 미발견";

    } else {
        Address address = addresses.get(0);
        return address.getAddressLine(0).toString();
    }

}
public boolean checkLocationServicesStatus() {
    LocationManager locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);

    return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)
            || locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);
}
public void setCurrentLocation(Location location, String markerTitle, String markerSnippet) {

    mMoveMapByUser = false;

    if (currentMarker != null) currentMarker.remove();

    LatLng currentLatLng = new LatLng(location.getLatitude(), location.getLongitude());

    MarkerOptions markerOptions = new MarkerOptions();
    markerOptions.position(currentLatLng);
    markerOptions.title(markerTitle);
    markerOptions.snippet(markerSnippet);
    markerOptions.draggable(true);
    markerOptions.icon(BitmapDescriptorFactory.fromResource(R.drawable.ex));  //내위치 마커//

    currentMarker = mGoogleMap.addMarker(markerOptions);


    if (mMoveMapByAPI) {

        Log.d(TAG, "setCurrentLocation :  mGoogleMap moveCamera "
                + location.getLatitude() + " " + location.getLongitude());
        // CameraUpdate cameraUpdate = CameraUpdateFactory.newLatLngZoom(currentLatLng, 15);
        CameraUpdate cameraUpdate = CameraUpdateFactory.newLatLng(currentLatLng);
        mGoogleMap.moveCamera(cameraUpdate);
    }
}
 public void setDefaultLocation() {

    mMoveMapByUser = false;

    //디폴트 위치, Seoul
    LatLng DEFAULT_LOCATION = new LatLng(37.56, 126.97);
    String markerTitle = "위치정보 가져올 수 없음";
    String markerSnippet = "위치 퍼미션과 GPS 활성 요부 확인하세요";

    if (currentMarker != null) currentMarker.remove();

    MarkerOptions markerOptions = new MarkerOptions();
    markerOptions.position(DEFAULT_LOCATION);
    markerOptions.title(markerTitle);
    markerOptions.snippet(markerSnippet);
    markerOptions.draggable(true);
    markerOptions.icon(BitmapDescriptorFactory.defaultMarker(BitmapDescriptorFactory.HUE_RED));

    currentMarker = mGoogleMap.addMarker(markerOptions);

    CameraUpdate cameraUpdate = CameraUpdateFactory.newLatLngZoom(DEFAULT_LOCATION, 15);
    mGoogleMap.moveCamera(cameraUpdate);

}
~~~

##### 나의 위치로 카메라 포지션 변경
~~~java
mGoogleMap.setOnMyLocationButtonClickListener(new GoogleMap.OnMyLocationButtonClickListener() {

    @Override
    public boolean onMyLocationButtonClick() {

        Log.d(TAG, "onMyLocationButtonClick : 위치에 따른 카메라 이동 활성화");
        mMoveMapByAPI = true;
        return true;
    }
});
mGoogleMap.setOnMapClickListener(new GoogleMap.OnMapClickListener() {

    @Override
    public void onMapClick(LatLng latLng) {

        Log.d(TAG, "onMapClick :");
    }
});
mGoogleMap.setOnCameraMoveStartedListener(new GoogleMap.OnCameraMoveStartedListener() {

    @Override
    public void onCameraMoveStarted(int i) {

        if (mMoveMapByUser == true && mRequestingLocationUpdates) {

            Log.d(TAG, "onCameraMove : 위치에 따른 카메라 이동 비활성화");
            mMoveMapByAPI = false;
        }

        mMoveMapByUser = true;

    }
});
mGoogleMap.setOnCameraMoveListener(new GoogleMap.OnCameraMoveListener() {

        @Override
        public void onCameraMove() {
        }
    });
}
@Override
public void onLocationChanged(Location location) {

    currentPosition
            = new LatLng(location.getLatitude(), location.getLongitude());

    Log.d(TAG, "onLocationChanged : ");

    MarkerOptions markerOptions = new MarkerOptions();
    String markerTitle = getCurrentAddress(currentPosition);
    String markerSnippet = "위도:" + String.valueOf(location.getLatitude())
        + " 경도:" + String.valueOf(location.getLongitude());

    //현재 위치에 마커 생성하고 이동
   setCurrentLocation(location, markerTitle, markerSnippet);

    mCurrentLocatiion = location;
}
~~~



>>#### 2-2-2 지도상 대결상대 표시   
2-2-1의 내용을 토대로 AI상대를 지도에 표시한다.    
1)AI상대들을 지도에 표시하기 위해 생성할 좌표를 확인한다.   
2)AI를 지도상에 나타내기 위해 MainActivity.java를 생성 후 개체 생성 코드에 해당 좌표를 삽입하여 생성한다.
##### AI 대결상대인 한누리관   
~~~java
MarkerOptions marker = new MarkerOptions();
marker .position(new LatLng(36.834258, 127.179241))
        .title("한누리관")
        .snippet("삐빅! 나는 공대생들의 창조물이다. 나를 이길수 있는가 휴먼?");
marker.icon(BitmapDescriptorFactory.fromResource(R.drawable.hannn));
googleMap.addMarker(marker).showInfoWindow();
</code>
</pre>    
<pre>
<code>
@Override
    public void onInfoWindowClick(Marker marker) {


        Location locationA = new Location("point A");
        locationA.setLatitude(36.834258);  //한누리//
        locationA.setLongitude(127.179241);
    }
~~~


>>#### 2-2-3 대결 가능 거리 설정    
2-2-2에서 AI대결상대를 생성 후 대결상대와 내 위치가 10M 이상으로 차이가 난다면 게임 실행이 안된다.    
1)10M 이상의 차이가 나는지 확인하기 위해 거리를 계산해주는 코드를 MainActivity.java에 추가한다.
##### AI 대결상대인 한누리관의 거리를 계산
~~~java
double distanceMeter4 =
        distance(currentPosition.latitude, currentPosition.longitude, 36.834258, 127.179241, "meter"); //한누리//

int i4 = Integer.parseInt(String.valueOf(Math.round(distanceMeter4)));
</code>
</pre>    
2)1)에서 계산을 진행한 값은 삼각함수 방식으로 정수값으로 바꾸어 준다.
<pre>
<code>
private static double distance(double lat1, double lon1, double lat2, double lon2, String unit) {

    double theta = lon1 - lon2;
    double dist = Math.sin(deg2rad(lat1)) * Math.sin(deg2rad(lat2)) + Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * Math.cos(deg2rad(theta));

    dist = Math.acos(dist);
    dist = rad2deg(dist);
    dist = dist * 60 * 1.1515;

    if (unit == "kilometer") {
        dist = dist * 1.609344;
    } else if(unit == "meter"){
        dist = dist * 1609.344;
    }

    return (dist);
}

private static double deg2rad(double deg) {
    return (deg * Math.PI / 180.0);
}

private static double rad2deg(double rad) {
    return (rad * 180 / Math.PI);
~~~ 

3)2)에서 코드를 작성 후 내 위치와 상대의 위치를 비교하기 위해 상대와 내 위치 데이터를 불러와 10M 이내이면 UnityPlayerActivity가 실행되며, 10M 이외이면 몇M 떨어져 있는지 알려주는 Toast 메세지를 출력한다.
##### AI 대결상대인 한누리관과 내 위치를 비교
~~~java
            if (marker.getTitle().equals("한누리관"))
            {
                if(distanceMeter4<=10) {
                    Intent intent = new Intent(MainActivity.this, UnityPlayerActivity.class);
                    startActivity(intent);
                }
                else
                {
                    Toast.makeText(getApplicationContext(), "NPC 한누리관과의 거리가 "+ i4 + "M만큼 떨어져 있습니다." , Toast.LENGTH_LONG).show();
                }
            }
~~~    

<img src="https://user-images.githubusercontent.com/62869017/85375170-def84a80-b570-11ea-9fd7-6f4dbc42edce.jpg" width="40%">



>### 2-3 안드로이드 스튜디오에서 구현한 기능

>#### 2-3-1 원형 프로필
원형 프로필을 만들기 위해서는 Buildgradle(app)에 지정을 해준다.
~~~java
annotationProcessor 'com.github.bumptech.glide:compiler:4.8.0'
implementation 'de.hdodenhof:circleimageview:2.2.0'
~~~ 
layout 설정 후 string변수를 이용하여 LoginActivity에서 보낸 정보를 받아온다.
~~~java
CircleImageView ivProfile = findViewById(R.id.ivProfile);

   Intent intent = getIntent();

    strProfile = intent.getStringExtra("profile");

Glide.with(this).load(strProfile).into(ivProfile);
~~~

##### 원형프로필
![2](https://user-images.githubusercontent.com/62593452/85274002-536fb280-b4b9-11ea-89f6-5b3b11b68f23.png)

    
2-3-2 팝업창
지도가 나오는 화면에서 카카오 로그인 시에는 왼쪽 상단에 자신의 카카오 프로필 사진이 나오며, 일반 로그인 시에는 지정된 이미지가 나오게 된다.
그리고 이미지를 클릭 시 팝업 창이 나오며 카카오 로그인 시에는 이름이, 일반 로그인 시에는 가입할 때 사용한 아이디가 나오며 로그아웃 버튼 또한 있다.

Menifest파일에 팝업창으로 열기 위해 테마를 Dialog로 지정해 준다.

~~~java
<activity android:name=".Main3Activity" android:theme="@android:style/Theme.Dialog"/>
~~~

Layout의 원형 프로필 사진에 onclick을 지정 해준다.

~~~java
<de.hdodenhof.circleimageview.CircleImageView
    android:id="@+id/ivProfile"
    android:layout_width="70dp"
    android:layout_height="70dp"
    android:layout_marginLeft="10dp"

    android:layout_marginTop="10dp"
    android:onClick="mOnPopupClick"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    tools:ignore="OnClick" />
~~~


MainActivity에 프로필 사진을 클릭하게 되면 팝업 창이 나오도록 
Intent 해준다. 이때 카카오 프로필 사진과 이름을 Activity 간 전송해 주어야 한다.

~~~java
public void mOnPopupClick(View v){
    //데이터 담아서 팝업(액티비티) 호출
    Intent intent = new Intent(MainActivity.this, Main3Activity.class);

    intent.putExtra("profile", strProfile);


    intent.putExtra("name", strNickname);
    startActivity(intent);
}
~~~

Dialog창으로 나오지만 상단의 타이틀 바로 나오지 않게 지정한다.

~~~java
String strNickname1, strProfile1;

 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //타이틀바 없애기
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activitiy_main3);
        Button btnLogout = findViewById(R.id.logoutbtn);
        //UI 객체생성

MainActivty에서 보낸 이름과 프로필 사진을 다시 받고 현재의 Layout에 지정하여 준다.
TextView tvNickname = findViewById(R.id.Nik);
ImageView ivProfile = findViewById(R.id.ivProfile);

//데이터 가져오기
Intent intent = getIntent();
strNickname1 = intent.getStringExtra("name");
strProfile1 = intent.getStringExtra("profile");
tvNickname.setText(strNickname1);

Glide.with(this).load(strProfile1).into(ivProfile);
~~~

확인 버튼을 클릭 시 팝업 창을 닫을 수 있도록 한다.

~~~java
public void mOnClose(View v){
    //데이터 전달하기
    Intent intent = new Intent();
    intent.putExtra("result", "Close Popup");
    setResult(RESULT_OK, intent);

    //액티비티(팝업) 닫기
    finish();
}
~~~

팝업 창 바깥 레이어 클릭 시에는 팝업 창이 닫히지 않도록 한다.
또한 스마트폰의 백 버튼 또한 잠궈준다.

~~~java
@Override
public boolean onTouchEvent(MotionEvent event) {
    //바깥레이어 클릭시 안닫히게
    if(event.getAction()==MotionEvent.ACTION_OUTSIDE){
        return false;
    }
    return true;
}

@Override
public void onBackPressed() {
    //안드로이드 백버튼 막기
    return;
}
~~~
##### 팝업창 이미지
<img src="https://user-images.githubusercontent.com/62869017/85279810-03e1b480-b4c2-11ea-84d0-a49e142eb5d1.jpg" width="40%">

2-3-3 로그아웃

로그아웃을 하기 위해 Layout에 버튼을 만들어 준다. 
~~~java
<Button
    android:id="@+id/logoutbtn"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="90dp"
    android:layout_marginTop="44dp"

    android:text="로그아웃"
    app:layout_constraintLeft_toLeftOf="@id/ivProfile"
    app:layout_constraintTop_toBottomOf="@+id/ivProfile" />
~~~

로그아웃을 위한 클릭 메소드를 입력한다.
~~~java
btnLogout.setOnClickListener(new Button.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(getApplicationContext(), "정상적으로 로그아웃되었습니다.", Toast.LENGTH_SHORT).show();

        UserManagement.getInstance().requestLogout(new LogoutResponseCallback() {
            @Override
            public void onCompleteLogout() {
                Intent intent = new Intent(Main3Activity.this, StartActivity.class);
                intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP);
                startActivity(intent);
            }
        });
    }
});
~~~






##### 팝업창 이미지
<img src="https://user-images.githubusercontent.com/62869017/85279810-03e1b480-b4c2-11ea-84d0-a49e142eb5d1.jpg" width="40%">


2-3-4 수익성 측면
수익성을 얻기 위해 LoginActivity와 MainActivity에 하단 배너 광고를 추가하였다
Google AdMob에 가입한 후 APP을 등록 한다.

##### Google AdMob 이미지
![AdMob](https://user-images.githubusercontent.com/62593452/85271973-7ea4d280-b4b6-11ea-8fe3-5bcae75b3b03.png)   

https://admob.google.com/intl/ko_ALL/home/?gclid=CjwKCAjwrcH3BRApEiwAxjdPTVkrX0stPQB6hiwrMiF_L6b4KOwC7RZXPFJ13Z7h87UP6IGMh-p2QxoCi-sQAvD_BwE

Build.gradle(app) 에 광고를 하기 위한 코드를 추가한다.

~~~java
implementation 'androidx.appcompat:appcompat:1.0.2'
implementation 'com.google.android.gms:play-services-ads:19.1.0'
~~~

Menifest에 meta-data를 추가한다.
Android:value 에는 광고의 ID를 넣게 되는데, 전면광고, 배너광고, 전면 동영상 광고, 보상형 동영상 광고 등 여러 광고마다 ID가 다르다.

~~~java
meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="ca-app-pub-3940256099942544~3347511713"
~~~

광고를 넣을 Layout에 광고를 추가하고 크기를 맞춘다.

~~~java
Activity_main.Layout
FrameLayout
    android:id="@+id/ad_view_container"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    android:layout_centerInParent="true"
    android:layout_marginTop="660dp" 
~~~

Layout에 맞는 Java 파일에 광고의 초기화 및 여러 설정을 적어준다.

~~~java
private AdView adView;
private static final String AD_UNIT_ID = "ca-app-pub-3940256099942544/9214589741";
private FrameLayout adContainerView;

MobileAds.initialize(this, new OnInitializationCompleteListener() {
    @Override
    public void onInitializationComplete(InitializationStatus initializationStatus) {}
});

// Set your test devices. Check your logcat output for the hashed device ID to
// get test ads on a physical device. e.g.
// "Use RequestConfiguration.Builder().setTestDeviceIds(Arrays.asList("ABCDEF012345"))
// to get test ads on this device."
MobileAds.setRequestConfiguration(
        new RequestConfiguration.Builder().setTestDeviceIds(Arrays.asList("ABCDEF012345")).build());

adContainerView = findViewById(R.id.ad_view_container);

// Since we're loading the banner based on the adContainerView size, we need to wait until this
// view is laid out before we can get the width.
adContainerView.post(new Runnable() {
    @Override
    public void run() {
        loadBanner();
    }
});
/** Called when leaving the activity */
@Override
public void onPause() {
    if (adView != null) {
        adView.pause();
    }
    super.onPause();
}

private void loadBanner() {
    // Create an ad request.
    adView = new AdView(this);
    adView.setAdUnitId(AD_UNIT_ID);
    adContainerView.removeAllViews();
    adContainerView.addView(adView);

    AdSize adSize = getAdSize();
    adView.setAdSize(adSize);

    AdRequest adRequest = new AdRequest.Builder().build();

    // Start loading the ad in the background.
    adView.loadAd(adRequest);
}

private AdSize getAdSize() {
    // Determine the screen width (less decorations) to use for the ad width.
    Display display = getWindowManager().getDefaultDisplay();
    DisplayMetrics outMetrics = new DisplayMetrics();
    display.getMetrics(outMetrics);

    float density = outMetrics.density;

    float adWidthPixels = adContainerView.getWidth();

    // If the ad hasn't been laid out, default to the full screen width.
    if (adWidthPixels == 0) {
        adWidthPixels = outMetrics.widthPixels;
    }

    int adWidth = (int) (adWidthPixels / density);

    return AdSize.getCurrentOrientationBannerAdSizeWithWidth(this, adWidth);
}
~~~





하단 배너에 광고가 나오게 된다.

##### 광고 이미지
<img src="https://user-images.githubusercontent.com/62869017/85280254-bca7f380-b4c2-11ea-8c0c-48fd2f7a0051.jpg" width="40%">
<img src="https://user-images.githubusercontent.com/62869017/85279527-861da900-b4c1-11ea-9e66-0fd08a7656c1.png" width="40%">












>### 2-4 Unity를 이용한 게임
>>#### 2-4-1 고스톱 알고리즘
48개의 화투중 각자 한장씩 뽑아 선을 정한다. 그리고 바닥에 4장, 선이 아닌 플레이어에 5장, 선에게 5장 이렇게 두번 반복해서 바닥에 8장, 플레이어가 각 10장씩 가지게 되면 게임이 시작된다. 이번에 만든 게임은 연령대에 구애받지 않는 게임이기에 점수 측정에 큰 무게를 두지 않았기 때문에 고의 개수에 따른 배점은 존재하지 않는다. 족보에 해당되는 광, 고도리, 홍단, 청단 등등은 점수 측정이 된다. 최종 10점이상이 되면 고 또는 스톱을 선택하게 되서 스톱을 선택하면 게임이 끝나게 된다.
![고스톱_게임화면](https://user-images.githubusercontent.com/51045433/85281883-bd8e5480-b4c5-11ea-925c-af5a4b89e716.jpg)

>>>##### 2-4-1-1 Player
AI가 이미 고를 외친상태에서 플레이어가 10점을 넘겨 고 또는 스톱을 선택하게 되는 상황일때는 플레이어가 자동으로 스톱을 선택하게 해서 역전했을 경우 자동으로 이기게 만들었다.
>>>##### 2-4-1-2 AI
AI가 가지고 있는 패와 바닥에 놓여져있는 패를 비교하여 같은 월의 패이면 해당되는 패를 바닥에 놓고 가지고 간다. AI는 바닥에 쌍피가 있다면 쌍피를 먼저 가져가는 등 점수가 높은 패를 우선적으로 가져가며 AI가 낼수 있는 패가 많다면 피->광->띠->열끗 순으로 선택한다. 그저 쉬운 게임이 아닌 여러 생각이 필요한 게임이기에 AI또한 점수를 높힐수 있는 최선의 선택을 하도록 했다.

>>#### 2-4-2 특수효과
고스톱을 하다 보면 특정한 상황 발생 시 그에 맞는 오브젝트가 나와야 한다.
그런 상황에 맞는 오브젝트를 만들어 상황이 발생하게 되면 Instance 시킨다.

##### 아틀라스 이미지
![아틀라스이미지](https://user-images.githubusercontent.com/62593452/85271978-806e9600-b4b6-11ea-87b9-5e8f57a7f8eb.png)   
  
예를 들어 폭탄의 경우를 설명하자면 이미지들을 아틀라스하여 자른 후
프리팹을 만들어 Sprite를 지정해준다

##### 폭탄 프리팹 이미지
![폭탄프리팹](https://user-images.githubusercontent.com/62593452/85271979-806e9600-b4b6-11ea-891c-88b3a2b8a859.png)   

~~~cs
public class EffectManager : SingletonMonobehaviour<EffectManager>
{
// 이벤트별 이펙트 객체.
Dictionary<CARD_EVENT_TYPE, GameObject> effects;
GameObject dust;


void Awake()
{
this.effects = new Dictionary<CARD_EVENT_TYPE, GameObject>();
}


public void load_effects()
{
load_effect(CARD_EVENT_TYPE.BOMB, "ef_explosion");
}


void load_effect(CARD_EVENT_TYPE event_type, string effect_name)
{
if (this.effects.ContainsKey(event_type))
{
Debug.LogError(string.Format("Already added this effect.  event type {0}, effect name {1}",
event_type, effect_name));
}

GameObject obj = GameObject.Instantiate(Resources.Load("effects/" + effect_name)) as GameObject;

obj.SetActive(false);
this.effects.Add(event_type, obj);
}


public void play(CARD_EVENT_TYPE event_type)
{
if (!this.effects.ContainsKey(event_type))
{
return;
}

this.effects[event_type].SetActive(true);
}


public void play_dust(Vector3 position, float delay, bool is_big)
{
StopAllCoroutines();
GameObject target = this.dust;

target.SetActive(false);
target.transform.position = position;
StartCoroutine(run_dust_effect(target, delay));
}

IEnumerator run_dust_effect(GameObject obj, float delay)
{
yield return new WaitForSeconds(delay);

obj.SetActive(true); }}
~~~

Visual Studio에서 Effect 이미지를 불러와준다.
또한 이미지 출력 후 1.5초 후 사라지게 해준다.

~~~cs
public class CDelayedDeactive : MonoBehaviour {

[SerializeField]
float delay;


void OnEnable() //검정대기화면//
{
StopAllCoroutines();
StartCoroutine(delayed_deactive());
}


IEnumerator delayed_deactive()
{
yield return new WaitForSeconds(this.delay);
gameObject.SetActive(false);}}
~~~

##### Sprite Renderer 이미지
![Sprite이미지](https://user-images.githubusercontent.com/62593452/85271981-81072c80-b4b6-11ea-986f-63266014fc37.png)


1고,2고… 의 경우
1고부터 시작되는 고의 경우엔 일반적인 방식으로는 많이 불편하다.
그래서 Visaul Studio에서 Count하여 각 상황에 맞는 이미지를 순차적으로 넣어주었다.

~~~cs
public class PopupGo : MonoBehaviour
    {

    List<Sprite> go_images;
Image go;

void Awake()
{
this.go_images = new List<Sprite>(); //스프라이트 이미지 불러오기//
    for (int i = 1; i <= 9; ++i) //for문 i를 1~9까지 돌린다//
    {
    Sprite spr = CSpriteManager.Instance.get_sprite(string.Format("go_{0:D2}", i));  //CSpriteManager에서 아틀라스한 이미지 중 go_01~go_09까지 불러온다//
    this.go_images.Add(spr); //이미지를 spr에 더한다//
    }

    this.go = transform.Find("image").GetComponent<Image>(); //Hierarchy의 image를 찾아 넣는다//
        }


        public void refresh(int howmany_go) //go이미지를 하나 넣을때마다 refesh하여 다음 이미지를 넣게 한다.//
        {
        if (howmany_go <= 0 || howmany_go >= 10)
        {
        return;
        }

        this.go.sprite = this.go_images[howmany_go - 1];
        }
        }
~~~

##### Popup_go 이미지
![Popup이미지](https://user-images.githubusercontent.com/62593452/85271985-849ab380-b4b6-11ea-962a-8a5db99c84a3.png)

>>>##### 2-4-2-1 효과음
효과음이 필요한 오브젝트가 많아 오브젝트마다 효과음을 넣으면 비효율적이어서 효과음 오브젝트를 만들어서 효과음이 필요한 부분에 각자 코드로 적용시켜 효율적인 효과음 재생이 가능하다.


카드가 움직일 때마다 소리를 재생하기
Unity의 Hierarchy에 SoundManager를 만든 후 Audio Source를 추가하여 준다.

![2번](https://user-images.githubusercontent.com/62593452/85271989-85cbe080-b4b6-11ea-9120-dd85b49ebcf0.png)

Audio Source에 카드 소리를 넣어주고 Play On Awake를 해제하여 준다.
![4번](https://user-images.githubusercontent.com/62593452/85271992-86647700-b4b6-11ea-9eb8-73cc7c94b968.png)

SoundManager의 Script를 작성한다.
~~~cs
public class SoundManager : MonoBehaviour
        {
public AudioClip soundPlace;
        AudioSource myAudio;

public static SoundManager instance;

        void Awake()
        {
        if(SoundManager.instance == null)
        {
        SoundManager.instance = this;
        }
        }
        void Start()
        {
        myAudio = GetComponent<AudioSource>();
        }

public void PlaySound()
        {
        myAudio.PlayOneShot(soundPlace);
        }
}
~~~

카드의 움직임 마다 소리가 재생되게 하기위해 MovingObject의 IEnumerator 밑에
적어준다.
~~~cs
    IEnumerator run_moving()
    {
        this.sprite_renderer.sortingOrder = CSpriteLayerOrderManager.Instance.Order;

        float begin_time = Time.time;
        while (Time.time - begin_time <= duration) //카드날라가기//
        {
            float t = (Time.time - begin_time) / duration;

            float x = EasingUtil.easeInExpo(begin.x, to.x, t);
            float y = EasingUtil.easeInExpo(begin.y, to.y, t);
            transform.position = new Vector3(x, y, begin.z);

            yield return 0;
        }
        SoundManager.instance.PlaySound();
        transform.position = to;
    }
}
~~~

각종 Effect 생성 시에만 소리가 재생되게 하기
예를 들어 폭탄 Effect가 생성 시에만 소리가 나오게 하기 위해서는 
PreFab의 폭탄 Sprite에 Audio Source를 추가한다.
Play On Awake를 활성화 한다.
![1번](https://user-images.githubusercontent.com/62593452/85271987-85334a00-b4b6-11ea-815a-d70b1c88d82c.png)


>>>##### 2-4-2-2 카드 효과(Card Hitting)
전체적인 카드의 움직임은 앱을 구동시키는 기기의 성능에 따라 움직임이 자연스러워 보이기도 하고 부자연스러워 보이기도 하기에 최대한 성능에 구애받지 않는 퍼포먼스를 보여주기 위해 Time.time을 이용해 시간이 흘러가는것에 따라 카드의 움직임을 조절가능하게 하였다.
>>>>##### 2-4-2-2-1 보간 함수
각종 보간함수를 통해 오브젝트를 부드럽게 이동시키거나 회전 가능하게 하였다. 보간함수 같은 경우는 복잡한 수학식으로 되있어서 이해하기는 힘들지만 예제가 많아서 내가 필요로 하는 기능만 가져다 사용하면 된다.   
~~~cs
public static class EasingUtil
{
    public static float linear(float start, float end, float value)
    {
        return Mathf.Lerp(start, end, value);
    }
    public static float easeInExpo(float start, float end, float value)
    {
        end -= start;
        return end * Mathf.Pow(2, 10 * (value / 1 - 1)) + start;
    }
}
~~~

linear 는 오브젝트의 스케일, Fade in 과 같은 효과에 사용된다.

esaeInExpo가 사용된 run_moving()을 이용해 카드를 이동시키고자 하는 위치로 이동시킬 수 있다.
~~~cs
IEnumerator run_moving()
	{
		this.sprite_renderer.sortingOrder = CSpriteLayerOrderManager.Instance.Order;

		float begin_time = Time.time;
		while (Time.time - begin_time <= duration)
		{
			float t = (Time.time - begin_time) / duration;

			float x = EasingUtil.easeInExpo(begin.x, to.x, t);
			float y = EasingUtil.easeInExpo(begin.y, to.y, t);
			transform.position = new Vector3(x, y, 0);

			yield return 0;
		}
		transform.position = to;
	}
~~~

