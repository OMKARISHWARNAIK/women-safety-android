# 🛡️ GuardianAlert: Sensor-Driven Android Emergency SOS Dispatcher

> **A hardware-accelerated Android emergency utility engineered for real-time distress detection, low-latency GPS geolocation acquisition, and autonomous SMS broadcast.**

[![Platform](https://img.shields.io/badge/Platform-Android-3DDC84?style=flat-square&logo=android&logoColor=white)](https://developer.android.com/)
[![Language](https://img.shields.io/badge/Language-Java%20%2F%20Android%20SDK-ED8B00?style=flat-square&logo=openjdk&logoColor=white)](https://www.java.com/)
[![Build](https://img.shields.io/badge/Build-Gradle-02303A?style=flat-square&logo=gradle&logoColor=white)](https://gradle.org/)
[![Min SDK](https://img.shields.io/badge/Min%20SDK-26%20(Oreo%2B)-blue?style=flat-square)](https://developer.android.com/about/versions/oreo)

---

## 📌 Problem & Engineering Motivation

During critical distress situations, victims rarely have the luxury of unlocking their phone, opening a messaging app, and typing out coordinates. 

**GuardianAlert** addresses this critical latency gap by utilizing on-device hardware sensors. Through continuous background accelerometer monitoring, a distinct physical kinetic pattern (device shake) triggers a multi-stage emergency pipeline that immediately retrieves the user's high-precision GPS coordinates and dispatches distress SMS payloads to pre-registered emergency contacts without requiring visual screen interaction.

---

## 🏛️ Event Pipeline Architecture

```mermaid
flowchart TD
    Sensor["Hardware Accelerometer Sensor"] -->|Continuous Tri-Axial G-Force Samples| Listener["SensorEventListener (Kinetic Engine)"]
    Listener -->|Threshold Evaluator (Acc > 12m/s²)| Debounce["Debounce & False-Positive Filter"]
    Debounce -->|Confirmed Shake Event| Service["Emergency Dispatch Service (Foreground)"]
    
    subgraph Geo["Location Subsystem"]
        GPS["FusedLocationProviderClient / GPS"]
        Format["Format Lat/Lng into Google Maps URL"]
    end
    
    subgraph Persistence["Storage Layer"]
        DB["Persistent Emergency Contact Store"]
    end
    
    subgraph Broadcast["Telephony Subsystem"]
        SMS["SmsManager Pipeline"]
        Contacts["Emergency Contacts (Direct SMS Delivery)"]
    end

    Service --> Geo
    Geo --> Format
    Service --> Persistence
    DB --> Broadcast
    Format --> Broadcast
    SMS --> Contacts
```

---

## ✨ Key Engineering Features

* **Kinetic Shake Pattern Recognition:** Implements a tuned `SensorEventListener` measuring vector acceleration magnitude across $X$, $Y$, and $Z$ axes. Applies sliding-window thresholding to prevent false triggers during walking or normal transit.
* **Autonomous Foreground Background Execution:** Configured as a persistent Android service with wakelock management to ensure the kinetic detection listener remains active when the screen is locked.
* **Fused GPS Location Resolution:** Interacts with Google Play Location Services to resolve high-accuracy latitude and longitude coordinates, automatically generating clickable navigation links (`https://maps.google.com/?q=lat,lng`).
* **Direct Telephony Broadcast:** Utilizes Android `SmsManager` to bypass third-party cloud messaging dependencies, guaranteeing delivery even in low-bandwidth or offline cellular environments.
* **Persistent Contact Management:** Clean local storage for managing primary and secondary emergency contacts with instant update capabilities.

---

## 🛠️ Tech Stack & Android Components

| Component | Implementation |
|---|---|
| **Language & Framework** | Java, Android SDK |
| **Hardware Sensors** | `SensorManager`, `Sensor.TYPE_ACCELEROMETER` |
| **Location Services** | `FusedLocationProviderClient`, `LocationManager`, GPS Provider |
| **Messaging & Telephony** | `android.telephony.SmsManager` |
| **Build & Tooling** | Gradle, Android Studio |

---

## 🚀 Getting Started & Installation

### Prerequisites
* **Android Studio** (Hedgehog / Iguana or later)
* **Android SDK**: API 26 (Android 8.0 Oreo) or higher
* Physical Android device (required for hardware accelerometer sensor testing)

### Build Instructions
1. **Clone the repository:**
   ```bash
   git clone https://github.com/OMKARISHWARNAIK/women-safety-android.git
   cd women-safety-android
   ```

2. **Open in Android Studio:**
   - Select `File > Open` and navigate to the cloned project folder.
   - Allow Gradle to sync dependencies automatically.

3. **Required Device Permissions:**
   GuardianAlert requires the following runtime permissions declared in `AndroidManifest.xml`:
   ```xml
   <uses-permission android:name="android.permission.SEND_SMS"/>
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
   <uses-permission android:name="android.permission.WAKE_LOCK"/>
   <uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
   ```

4. **Deploy & Test:**
   - Connect your physical Android test device via USB Debugging.
   - Click **Run** (`Shift + F10`).
   - Grant SMS and Location permissions on initial launch, configure your emergency contacts, and test the shake trigger.

---

## 🔒 Security & Privacy Considerations

* **Local-Only Data Storage:** Contact records and location payloads are never transmitted to external third-party tracking servers.
* **Rate-Limiting Protection:** Built-in cool-down timer prevents duplicate SMS burst transmissions on prolonged shaking.

---

## 👨‍💻 Author

**Omkar Ishwar Naik**  
*Software Engineer @ ISG Labs | Computer Science & Engineering (2026 Graduate)*  
GitHub: [@OMKARISHWARNAIK](https://github.com/OMKARISHWARNAIK) &bull; Email: [omkarnamadhari2147@gmail.com](mailto:omkarnamadhari2147@gmail.com)
