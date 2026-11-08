# Ex.No:2 Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.The client app calls the service and changes the button's colour within the Main activity.



## AIM:

To Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.
The client app calls the service and changes the button's colour within the Main activity using AIDL interface in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Griaffe )

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as CSAIDL and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display message give in MainActivity file(client/server).

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the client/server services using AIDL”.
Developed by: Sanjit A
Registeration Number : 212224220087
*/
```
## MainActivity.java
```java
package com.example.pmdex2server;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.os.RemoteException;
import android.util.Log;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.example.aidl.IColorService;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";

    private IColorService colorService;
    private boolean isBound = false;
    private Button btnChangeColor;
    private TextView tvStatus;

    private final ServiceConnection serviceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            colorService = IColorService.Stub.asInterface(service);
            isBound = true;
            tvStatus.setText("AIDL Color Service Connected");
            Log.d(TAG, "ColorService connected.");
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            colorService = null;
            isBound = false;
            tvStatus.setText("AIDL Color Service Disconnected");
            Log.d(TAG, "ColorService disconnected.");
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvStatus = findViewById(R.id.tvStatus);
        btnChangeColor = findViewById(R.id.btnChangeColor);

        // Bind to ColorService (Bound Service)
        Intent intent = new Intent(this, ColorService.class);
        startService(intent);
        bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE);

        // Create an on click listener button
        btnChangeColor.setOnClickListener(v -> {
            if (isBound && colorService != null) {
                try {
                    // Call the service to get a random color
                    int randomColor = colorService.getRandomColor();
                    // Change the button's color
                    btnChangeColor.setBackgroundColor(randomColor);
                    Toast.makeText(MainActivity.this, "Color changed via AIDL!", Toast.LENGTH_SHORT).show();
                } catch (RemoteException e) {
                    Log.e(TAG, "RemoteException while calling getRandomColor", e);
                }
            } else {
                Toast.makeText(MainActivity.this, "Service not bound yet", Toast.LENGTH_SHORT).show();
            }
        });
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (isBound) {
            unbindService(serviceConnection);
            isBound = false;
        }
    }
}


```
## AndroidManifest.XML
```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:label="AIDL Server App"
        android:supportsRtl="true"
        android:theme="@style/Theme.PmdExp2">

        <service
            android:name=".ColorService"
            android:exported="true">
            <intent-filter>
                <action android:name="com.example.server.IColorService" />
            </intent-filter>
        </service>

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>

```
## IColorService.aidl
```java
package com.example.server;
public interface IColorService extends android.os.IInterface
{
  /** Default implementation for IColorService. */
  public static class Default implements com.example.server.IColorService
  {
    @Override public int getColor() throws android.os.RemoteException
    {
      return 0;
    }
    @Override
    public android.os.IBinder asBinder() {
      return null;
    }
  }
  /** Local-side IPC implementation stub class. */
  public static abstract class Stub extends android.os.Binder implements com.example.server.IColorService
  {
    /** Construct the stub and attach it to the interface. */
    @SuppressWarnings("this-escape")
    public Stub()
    {
      this.attachInterface(this, DESCRIPTOR);
    }
    /**
     * Cast an IBinder object into an com.example.server.IColorService interface,
     * generating a proxy if needed.
     */
    public static com.example.server.IColorService asInterface(android.os.IBinder obj)
    {
      if ((obj==null)) {
        return null;
      }
      android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
      if (((iin!=null)&&(iin instanceof com.example.server.IColorService))) {
        return ((com.example.server.IColorService)iin);
      }
      return new com.example.server.IColorService.Stub.Proxy(obj);
    }
    @Override public android.os.IBinder asBinder()
    {
      return this;
    }
    @Override public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException
    {
      java.lang.String descriptor = DESCRIPTOR;
      if (code >= android.os.IBinder.FIRST_CALL_TRANSACTION && code <= android.os.IBinder.LAST_CALL_TRANSACTION) {
        data.enforceInterface(descriptor);
      }
      if (code == INTERFACE_TRANSACTION) {
        reply.writeString(descriptor);
        return true;
      }
      switch (code)
      {
        case TRANSACTION_getColor:
        {
          int _result = this.getColor();
          reply.writeNoException();
          reply.writeInt(_result);
          break;
        }
        default:
        {
          return super.onTransact(code, data, reply, flags);
        }
      }
      return true;
    }
    private static class Proxy implements com.example.server.IColorService
    {
      private android.os.IBinder mRemote;
      Proxy(android.os.IBinder remote)
      {
        mRemote = remote;
      }
      @Override public android.os.IBinder asBinder()
      {
        return mRemote;
      }
      public java.lang.String getInterfaceDescriptor()
      {
        return DESCRIPTOR;
      }
      @Override public int getColor() throws android.os.RemoteException
      {
        android.os.Parcel _data = android.os.Parcel.obtain();
        android.os.Parcel _reply = android.os.Parcel.obtain();
        int _result;
        try {
          _data.writeInterfaceToken(DESCRIPTOR);
          boolean _status = mRemote.transact(Stub.TRANSACTION_getColor, _data, _reply, 0);
          _reply.readException();
          _result = _reply.readInt();
        }
        finally {
          _reply.recycle();
          _data.recycle();
        }
        return _result;
      }
    }
    static final int TRANSACTION_getColor = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
  }
  /** @hide */
  public static final java.lang.String DESCRIPTOR = "com.example.server.IColorService";
  public int getColor() throws android.os.RemoteException;
}

```

## OUTPUT

<img width="1917" height="1136" alt="image" src="https://github.com/user-attachments/assets/43f30eab-bac9-426d-9d69-4a535bf46802" />

<img width="1917" height="1132" alt="image" src="https://github.com/user-attachments/assets/ce454e2f-8d85-4665-9ae5-fcc800dcc9f2" />




## RESULT
Thus a Simple Android Application to create a AIDL interface and communicate the process between client and server using AIDL interface in Android Studio is developed and executed successfully.
