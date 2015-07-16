#REF: https://developers.google.com/analytics/devguides/collection/android/v4/start#
https://github.com/googlesamples/google-services

1. Create a Google Analytics Account (www.google.com/analytics/)
2. Create a New Android Application Project
3. Import Google Play Services in your project

app_tracker.xml
```
#!xml

<?xml version="1.0" encoding="utf-8"?>
<resources>

    <string name="ga_trackingId">UA-XXXXXXXX-1</string>

    <bool name="ga_autoActivityTracking">true</bool>
    <bool name="ga_reportUncaughtExceptions">true</bool>

    <!--<string name="ga_appName">GoogleAnalyticsApp</string>-->
    <!--<string name="ga_appVersion">1.1.3</string>-->

    <bool name="ga_debug">true</bool>

    <item
        name="ga_dispatchPeriod"
        format="integer"
        type="integer">120
    </item>

    <string name="ga_sampleFrequency">90</string>

    <bool name="ga_anonymizeIp">true</bool>
    <bool name="ga_dryRun">false</bool>

    <string name="ga_sampleFrequency">100.0</string>

    <integer name="ga_sessionTimeout">-1</integer>

    <string name="cvnhan.android.androidanalyticsexample">MainActivity</string>

</resources>
```

ecommerce_tracker.xml

```
#!xml

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <integer name="ga_sessionTimeout">60</integer>
    <string name="ga_trackingId">UA-XXXXXXXX-1</string>
</resources>
```

global_tracker.xml

```
#!xml

<?xml version="1.0" encoding="utf-8"?>
<resources>

    <integer name="ga_sessionTimeout">300</integer>

    <bool name="ga_autoActivityTracking">true</bool>

    <screenName name="com.google.android.gms.analytics.samples.mobileplayground.ScreenviewFragment">GoogleAnalyticsExample ScreenViewSampleScreen
    </screenName>
    <screenName name="com.google.android.gms.analytics.samples.mobileplayground.EcommerceFragment">GoogleAnalyticsExample EcommerceSampleScreen
    </screenName>

    <string name="ga_trackingId">UA-XXXXXXXX-1</string>

</resources>
```

GoogleAnalyticsApp

```
#!java

package cvnhan.android.androidanalyticsexample;

/**
 * Created by Administrator on 31-Mar-15.
 */
import android.app.Application;

import com.google.android.gms.analytics.GoogleAnalytics;
import com.google.android.gms.analytics.Tracker;

import java.util.HashMap;

public class GoogleAnalyticsApp extends Application {
    public enum TrackerName {
        APP_TRACKER, // Tracker used only in this app.
        GLOBAL_TRACKER, // Tracker used by all the apps from a company. eg: roll-up tracking.
        ECOMMERCE_TRACKER, // Tracker used by all ecommerce transactions from a company.
    }

    private static final String PROPERTY_ID = "UA-XXXXXXXX-1";

    public static int GENERAL_TRACKER = 0;

    public HashMap mTrackers = new HashMap();

    public GoogleAnalyticsApp() {
        super();
    }

    public synchronized Tracker getTracker(TrackerName appTracker) {
        if (!mTrackers.containsKey(appTracker)) {
            GoogleAnalytics analytics = GoogleAnalytics.getInstance(this);
            Tracker t = (appTracker == TrackerName.APP_TRACKER) ? analytics.newTracker(PROPERTY_ID) : (appTracker == TrackerName.GLOBAL_TRACKER) ? analytics.newTracker(R.xml.app_tracker) : analytics.newTracker(R.xml.ecommerce_tracker);
            mTrackers.put(appTracker, t);
        }
        return (Tracker) mTrackers.get(appTracker);
    }
}

```

MainActivity

```
#!java

package cvnhan.android.androidanalyticsexample;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import com.google.android.gms.analytics.GoogleAnalytics;
import com.google.android.gms.analytics.HitBuilders;
import com.google.android.gms.analytics.Tracker;


public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Tracker t = ((GoogleAnalyticsApp) getApplication()).getTracker(GoogleAnalyticsApp.TrackerName.APP_TRACKER);
        t.setScreenName("Home");
        t.send(new HitBuilders.AppViewBuilder().build());
    }

    @Override
    protected void onStart() {
        super.onStart();
        GoogleAnalytics.getInstance(MainActivity.this).reportActivityStart(this);
    }


    @Override
    protected void onStop() {
        super.onStop();
        GoogleAnalytics.getInstance(MainActivity.this).reportActivityStop(this);
    }
}

```

AndroidManifest.xml

```
#!xml

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="cvnhan.android.androidanalyticsexample">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:name=".GoogleAnalyticsApp"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <meta-data
            android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
        <meta-data
            android:name="com.google.android.gms.analytics.globalConfigResource"
            android:resource="@xml/global_tracker" />
    </application>

</manifest>

```