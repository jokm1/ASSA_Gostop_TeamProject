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
>>##### 2-1-4 원형 프로필
>#### 2-2 Google Map
>>##### 2-2-1 지도상 내위치 표시
>>##### 2-2-2 지도상 대결상대 표시
>>##### 2-2-3 대결 가능 거리 설정
>#### 2-3 Unity를 이용한 게임
>>##### 2-3-1 고스톱 알고리즘
>>>###### 2-3-1-1 Player
>>>###### 2-3-1-2 AI
>>##### 2-3-2 특수효과
>>>###### 2-3-2-1 효과음
>>>###### 2-3-2-2 카드 효과(Card Hitting)
>>>###### 2-3-2-3 보간 함수   




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
<pre>
<code>
implementation 'com.android.volley:volley:1.1.1'
</code>
</pre>
안드로이드 스튜디오에서 서버로 정보를 전송하기위한 Login.php / Register.php 파일을 생성한다.   
##### Login.php
<pre>
<code>

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

</code>
</pre>

##### Register.php
<pre>
<code>
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
</code>
</pre>

2개의 파일을 FileZilla를 이용하여 html폴더에 추가해준다.    
![fillzilla](https://user-images.githubusercontent.com/62593452/85231104-76558480-b42f-11ea-8bfa-6faaf3dd6ac2.PNG)   


>#### 2-1-3 카카오 로그인
카카오톡은 대한민국에서 가장 큰 sns로 우리나라의 국민 전부가 쓰고 있다고 해도 과언이 아닌 어플로, 카카오 로그인을 통하여 간편하게 어플을 즐길 수 있도록 하였다. 
카카오에 APP등록하기   
[kakao app등록 링크](https://developers.kakao.com/)   
![카카오톡등록](https://user-images.githubusercontent.com/62593452/85231709-7c019900-b434-11ea-98f0-0b163564188c.png)   
Buildgradle(app)파일에 카카오 로그인을 위한 sdk, url을 추가해준다.
<pre>
<code>
dependencies {
    implementation 'com.kakao.sdk:usermgmt:1.29.0'
}

</code>
</pre>
<pre>
<code>
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
</code>
</pre>
manifest파일에 카카오 로그인에 필요한 인터넷연결을 permission하고 AppKey와 네이티브 키를 입력한다.
<pre>
<code>
uses-permission android:name="android.permission.INTERNET"
</pre>
</code>
<pre>
<code>
meta-data
    android:name="com.kakao.sdk.AppKey"
    android:value="네이티브 키"
</pre>
</code>

카카오 로그인을 하기위해 해시 키를 생성 후 카카오 디벨로퍼에 입력해준다.
##### 해시 키 생성
<pre>
<code>
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
</code>
</pre>
##### logcat창에 나오는 HashKey
![해시키](https://user-images.githubusercontent.com/62593452/85231710-7c9a2f80-b434-11ea-8124-d118791b0e7b.png)    
##### 해시 키 등록
![해시키등록](https://user-images.githubusercontent.com/62593452/85231707-7ad06c00-b434-11ea-8227-65cfb3187d6c.png)     

>#### 2-1-4 원형 프로필
원형 프로필을 만들기 위해서는 Buildgradle(app)에 지정을 해준다.
<pre>
<code>
annotationProcessor 'com.github.bumptech.glide:compiler:4.8.0'
implementation 'de.hdodenhof:circleimageview:2.2.0'
</code>
</pre>   
layout 설정 후 string변수를 이용하여 LoginActivity에서 보낸 정보를 받아온다.
<pre>
<code>
CircleImageView ivProfile = findViewById(R.id.ivProfile);

   Intent intent = getIntent();

    strProfile = intent.getStringExtra("profile");

Glide.with(this).load(strProfile).into(ivProfile);
</code>
</pre>
##### 원형프로필
![원형프로필](https://user-images.githubusercontent.com/62593452/85231708-7c019900-b434-11ea-8394-b2ee8c21b71c.png)   

    
    

>### 2-2 Google Map
Google Map을 기반으로 지도를 구현한다.    
Google Map에 엑세스하기 위해서는 최소 SDK레벨 2.0 이여야한다.   
1)Google 계정 및 GoogleMap 계정 및 API Key 발급받는다.   
2)Google console developer 사이트에 방문하여 새 프로젝트를 생성 후 API Key발급받고   
AndroidManifest.xml파일에 추가해준다.
<pre>
<code>
 meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="API키"
</code>
</pre>   

3)Module app에 GooglePlayServices를 사용하기 위한 코드를 추가해준다.
<pre>
<code>
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    implementation 'com.google.android.gms:play-services-maps:17.0.0'
    implementation 'com.google.android.gms:play-services-location:17.0.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
</code>
</pre>    

4)activity_main.xml layout파일에 <fragment>태그를 지도가 표시될 위치에 적어준다.
<pre>
<code>
    fragment
        android:id="@+id/map"
        android:name="com.google.android.gms.maps.SupportMapFragment"

        android:layout_width="match_parent"
        android:layout_height="match_parent"
</code>
</pre>   
지도를 포함하는 MainActivity.java파일에 코드를 추가해준다.
<pre>
<code>
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
</code>
</pre>    


>>#### 2-2-1 지도상 내위치 표시
1)내 기기의 위치를 지도상에 표시해주기 위해서 권한 요청을 요구하는 코드와 GPS 활성화를 위한 코드를 추가해준다.
<pre>
<code>
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
</code>
</pre>    

2)위 코드를 작성하면 퍼미션 검사를 통해 GPS 활성화가 진행된다.   
3)Geocorder를 이용한 내 위치를 불러오는 코드와 나의 위치로 카메라 포지션을 변경해 주기 위한 코드를 MainActivity에 추가해준다.
##### Geocorder를 이용한 내위치 불러오기
<pre>
<code>
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
</code>
</pre>    
##### 나의 위치로 카메라 포지션 변경
<pre>
<code>
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
</code>
</pre>



>>#### 2-2-2 지도상 대결상대 표시   
2-2-1의 내용을 토대로 AI상대를 지도에 표시한다.    
1)AI상대들을 지도에 표시하기 위해 생성할 좌표를 확인한다.   
2)AI를 지도상에 나타내기 위해 MainActivity.java를 생성 후 개체 생성 코드에 해당 좌표를 삽입하여 생성한다.
##### AI 대결상대인 한누리관   
<pre>
<code>
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
</code>
</pre>


>>#### 2-2-3 대결 가능 거리 설정    
2-2-2에서 AI대결상대를 생성 후 대결상대와 내 위치가 10M 이상으로 차이가 난다면 게임 실행이 안된다.    
1)10M 이상의 차이가 나는지 확인하기 위해 거리를 계산해주는 코드를 MainActivity.java에 추가한다.
##### AI 대결상대인 한누리관의 거리를 계산
<pre>
<code>
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
</code>
</pre>    

3)2)에서 코드를 작성 후 내 위치와 상대의 위치를 비교하기 위해 상대와 내 위치 데이터를 불러와 10M 이내이면 UnityPlayerActivity가 실행되며, 10M 이외이면 몇M 떨어져 있는지 알려주는 Toast 메세지를 출력한다.
##### AI 대결상대인 한누리관과 내 위치를 비교
<pre>
<code>
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
</code>
</pre>    




>### 2-3 Unity를 이용한 게임
>>#### 2-3-1 고스톱 알고리즘
>>>##### 2-3-1-1 Player
>>>##### 2-3-1-2 AI

>>#### 2-3-2 특수효과
>>>##### 2-3-2-1 효과음
효과음이 필요한 오브젝트가 많아 오브젝트마다 효과음을 넣으면 비효율적이어서 효과음 오브젝트를 만들어서 효과음이 필요한 부분에 각자 코드로 적용시켜 효율적인 효과음 재생이 가능하다.
>>>##### 2-3-2-2 카드 효과(Card Hitting)
전체적인 카드의 움직임은 앱을 구동시키는 기기의 성능에 따라 움직임이 자연스러워 보이기도 하고 부자연스러워 보이기도 하기에 최대한 성능에 구애받지 않는 퍼포먼스를 보여주기 위해 Time.time을 이용해 시간이 흘러가는것에 따라 카드의 움직임을 조절가능하게 하였다.
>>>##### 2-3-2-3 보간 함수
각종 보간함수를 통해 오브젝트를 부드럽게 이동시키거나 회전 가능하게 하였다. 보간함수 같은 경우는


