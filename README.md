# 🔋 Home Assistant Inverter Control Add-on

## 📌 Overview
The **Inverter Control Add-on** is a **Home Assistant add-on** designed to manage inverter modes:

- **Charge Mode** – Charges the battery.
- **Discharge Mode** – Supplies power from the battery.
- **Idle Mode** – Keeps the inverter in standby.

### 🚀 Features:
✅ Runs as a **Docker container** inside Home Assistant.  
✅ **Mock Mode** to simulate inverter operations.  
✅ Can be upgraded to control a **real inverter** via API or Modbus.  

---

## 🛠️ **Installation & Setup**

### 📌 **Step 1: Install the Add-on**
1. Open **Home Assistant**.
2. Navigate to **Settings → Add-ons → Add-on Store**.
3. Click **Repositories** and add:
   ```
   https://github.com/Khoshal01/Home-Assistant-Inverter-Control-Add-on
   ```
4. Install **Inverter Control Add-on**.
5. Click **Start** to run it.

---


## 🚀 **Running the Add-on in Home Assistant**
Once installed, start the add-on and check logs:
1. **Go to Add-ons → Inverter Control Add-on → Logs**.
2. You should see:
   ```
   [MOCK MODE] Pretending to set mode: charge with power 2500
   State saved: {'mode': 'charge', 'power': 2500}
   ```

### **Manual Run (Docker)**
Run the add-on in Docker manually:
```powershell
docker build -t inverter_control .
docker run --rm inverter_control
```

---

## 🔌 **Transitioning to a Real Inverter**
To move from **mock mode** to **real inverter integration**, follow these steps:

### **Step 1: Identify the Inverter Communication Method**
Check if your inverter supports:
- **REST API** (over HTTP)
- **Modbus TCP** (industrial protocol)
- **MQTT** (message-based control)

### **Step 2: Modify the Code**
Edit `/scripts/test_inverter.py` to send **real commands**.

**For REST API Communication:**
```python
import requests
import json

INVERTER_IP = "192.168.1.100"
API_KEY = "your_api_key"

def set_inverter_mode(mode, power):
    url = f"http://{INVERTER_IP}/api/set_mode"
    payload = {"mode": mode, "power": power, "api_key": API_KEY}

    try:
        response = requests.post(url, json=payload)
        if response.status_code == 200:
            print(f"[SUCCESS] Mode set to {mode} with power {power}")
        else:
            print(f"[ERROR] Failed to set mode: {response.text}")
    except Exception as e:
        print(f"[EXCEPTION] {e}")

# Example usage
set_inverter_mode("charge", 2500)
set_inverter_mode("discharge", -2500)
set_inverter_mode("idle", 0)
```
🔹 **Replace `INVERTER_IP` and `API_KEY` with actual values**.  
🔹 **Ensure the inverter API supports these commands**.  

### **Step 3: Rebuild the Docker Container**
```powershell
docker build -t inverter_control .
docker run --rm inverter_control
```
Now it will send **real commands** instead of mock logs.

### **Step 4: Modify Home Assistant Integration**
1. Update `/config/config.yaml`:
```yaml
options:
  inverter_ip: "192.168.1.100"
  inverter_api_key: "your_api_key"
schema:
  inverter_ip: str
  inverter_api_key: str
```
2. Restart the add-on in Home Assistant.
3. **Now Home Assistant can send commands to the real inverter!**

---

## 💪 **Usage**
### **Available Commands**
| Mode        | Description                                      | Example Command |
|------------|--------------------------------------------------|----------------|
| **Charge** | Charges the battery at a specified power level. | `set_inverter_mode("charge", 2500)` |
| **Discharge** | Supplies power from the battery. | `set_inverter_mode("discharge", -2500)` |
| **Idle** | Keeps the inverter in standby. | `set_inverter_mode("idle", 0)` |

---

## 🛠 **Troubleshooting & Support**

### **1️⃣ The add-on is running, but no changes happen.**
🔹 Ensure the script is sending API requests:
```bash
curl -X POST http://192.168.1.100/api/set_mode -d '{"mode":"charge","power":2500}'
```
If the inverter doesn’t respond, check authentication.

### **2️⃣ Home Assistant doesn’t detect the add-on.**
🔹 Make sure the `config.yaml` file exists in the root directory.  
🔹 Restart Home Assistant after installing the add-on.

### **3️⃣ Getting API errors?**
🔹 Check if the inverter requires authentication.  
🔹 Verify if the API supports `POST` requests.  

---


## 👨‍💻 **Author & License**
- **Author:** Khoshal Amin  
- **License:** MIT  

🌟 **Enjoy using the Home Assistant Inverter Control Add-on!**  

