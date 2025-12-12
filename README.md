# IOT-Enabled-3D-Printed-4DOF-Robotics-Arm
This project features a 4-DOF robotic arm built from 3D-printed parts and MG996R servos. Powered by an ESP32, it utilizes IoT protocols for remote control via a web app. With base, shoulder, elbow, and gripper joints, it enables global teleoperation. This low-cost system is ideal for education and handling hazardous materials remotely.

```markdown
# ESP32 4DOF Robotic Arm Controller — Full procedure for learning and control

This README gives a step-by-step, hands-on procedure to build, deploy, and control the 4-DOF 3D-printed robotic arm using an ESP32. It is written for open-source learning and reproducibility.

Contents
- Hardware checklist
- Wiring and power
- Software prerequisites
- Building and uploading firmware (Arduino IDE and PlatformIO)
- First boot and network setup
- Web UI and API usage (examples)
- WebSocket realtime control (examples)
- MQTT integration (examples)
- Inverse kinematics notes and examples
- Calibration and safety
- Troubleshooting
- Contributing and learning pointers

Hardware checklist
- ESP32 development board (e.g., ESP32 DevKitC)
- 4 hobby servos (SG90 / MG90S or similar)
- External 5V power supply capable of supplying current for all servos (recommended 2A or more depending on servos)
- Wires, connectors, and a breadboard or custom PCB
- 3D-printed arm parts (assembled)
- USB cable for programming the ESP32
- Optional: logic level converter if your servo power scheme requires it (not usually necessary)

Wiring and power
1. Connect each servo signal wire to the configured ESP32 GPIO pins. Default pins in the firmware: J1=GPIO13, J2=GPIO12, J3=GPIO14, J4=GPIO27. Edit `RobotArm.ino` if you use different pins.
2. Power servos from the external 5V supply. Do NOT power multiple servos from the ESP32 5V pin.
3. Connect the GND of the external 5V supply to the ESP32 GND (common ground is required).
4. Verify servo connector orientation: brown/black = GND, red = +5V, yellow/orange = signal. Connect carefully to avoid reversing polarity.

Software prerequisites
- Arduino IDE or PlatformIO
- ESP32 board support installed:
  - Arduino IDE: use Boards Manager to install "esp32 by Espressif Systems"
  - PlatformIO: platform = espressif32
- Libraries required (install via Library Manager or PlatformIO):
  - ESP32Servo
  - PubSubClient
  - arduinoWebSockets
  - ArduinoJson
- Optionally: mosquitto or an MQTT broker for local testing

Edit secrets and configuration
1. Open `secrets.h` and set:
   - WIFI_SSID
   - WIFI_PASSWORD
   - MQTT_SERVER (broker hostname or IP)
   - MQTT_PORT
   - MQTT_USER and MQTT_PASS (if required)
2. Adjust `SERVO_PIN[]` in `RobotArm.ino` to match your wiring.
3. If using IK, open `ik.h` and set link lengths L1, L2, L3 to match your arm.

Build & upload (Arduino IDE)
1. Open the entire folder as a sketch (ensure `RobotArm.ino` is present).
2. Install the listed libraries via Sketch > Include Library > Manage Libraries.
3. Select the correct ESP32 board and COM port (Tools menu).
4. Build and Upload.
5. Open Serial Monitor at 115200 baud to observe boot logs and the assigned IP address.

Build & upload (PlatformIO)
1. Ensure `platformio.ini` includes the dependencies (provided in scaffold).
2. From PlatformIO, run "Build" then "Upload".
3. Monitor serial (115200) to see network information.

First boot and network
1. With `secrets.h` configured and device powered, watch Serial Monitor at 115200.
2. The device will attempt to connect to the configured Wi‑Fi. If it fails within 15 seconds, it will start a SoftAP named "RobotArm-Setup". Connect to that AP to access a fallback UI or debug.
3. When connected, the Serial Monitor prints the device IP. Open a browser and navigate to that IP.

Web UI and simple HTTP API
- Visit http://<device-ip>/ to use the embedded web UI with sliders and WebSocket control.
- GET state: http://<device-ip>/api/state returns JSON like:
  {
    "j1": 90,
    "j2": 90,
    "j3": 90,
    "j4": 90
  }
- Move using query parameters:
  - Example: http://<device-ip>/move?j1=120&j3=45
  - This will set joint 1 to 120° and joint 3 to 45° (angles are clamped to 0–180).

HTTP examples (curl)
- Query state:
  ```bash
  curl http://192.168.1.50/api/state
  ```
- Move joints:
  ```bash
  curl "http://192.168.1.50/move?j1=100&j2=80"
  ```

WebSocket realtime control
- The device runs a WebSocket server on port 81.
- The provided web UI connects to ws://<device-ip>:81.
- WebSocket messages are JSON. Examples:
  - Move joints:
    ```json
    {"cmd":"move","j1":90,"j2":80}
    ```
  - Inverse-kinematics target:
    ```json
    {"cmd":"ik","x":120,"y":0,"z":60}
    ```
  - Home:
    ```json
    {"cmd":"home"}
    ```
- The device broadcasts JSON state updates to connected WebSocket clients.

MQTT integration
- Default topics:
  - Subscribe to commands: `robotarm/commands`
  - Publish state: `robotarm/state`
  - Availability: `robotarm/availability`
- MQTT command format:
  - Move joints:
    ```json
    {"type":"move","j1":110,"j2":70}
    ```
  - IK:
    ```json
    {"type":"ik","x":100,"y":0,"z":40,"grip":90}
    ```
  - Home:
    ```json
    {"type":"home"}
    ```
- Example publish with mosquitto_pub:
  ```bash
  mosquitto_pub -h broker.hivemq.com -t robotarm/commands -m '{"type":"move","j1":120}'
  ```
- Example subscribe to state:
  ```bash
  mosquitto_sub -h broker.hivemq.com -t robotarm/state
  ```

Inverse kinematics (IK) notes
- The provided `ik.h` is a simple helper intended as a starting point. It:
  - Computes base rotation from atan2(y,x)
  - Solves a reduced 2D planar 2-link problem for shoulder and elbow
  - Does a basic reachability check
- IK is approximate and depends on link lengths (L1, L2, L3). Update values in `ik.h` and test carefully.
- IK does not consider collisions, joint offsets, servo orientation mismatches, or singularities. Treat IK motions slowly and supervise initial tests.

Calibration and mapping
1. Verify each servo's neutral position (90°) corresponds to the physical neutral pose of the arm.
2. If a servo is mirrored, invert the angle for that servo in code or add an offset mapping table.
3. Add per-servo offsets in code to correct mechanical mounting differences. A suggested approach:
   - Create an offsets[] array and apply offsets when writing servo angles:
     ```
     servos[i].write(constrain(currentAngles[i] + offsets[i], ANGLE_MIN, ANGLE_MAX));
     ```
4. Record calibration values in a file (LittleFS/SPIFFS) or in `secrets.h` for a simple static approach.

Safety and best practices
- Always use a separate 5V power supply for servos and make sure its maximum current rating is adequate.
- Keep hands and objects away from the arm while testing automated or IK moves.
- Start with low-power/slow motions; do not command large sudden steps.
- Implement motion smoothing/ramping if you see jerky motions or you want more precise control.

Troubleshooting
- No Wi‑Fi IP shown:
  - Check `secrets.h` SSID and password.
  - Verify Wi‑Fi signal and credentials by connecting another device.
- Servos jitter or behave erratically:
  - Check power supply capacity and wiring.
  - Ensure common ground between ESP32 and servo power.
  - Try increasing PWM update frequency or using different min/max microsecond limits in attach().
- MQTT won't connect:
  - Verify broker address and port.
  - Check credentials and broker ACLs.
  - Use a known public broker for testing (e.g., broker.hivemq.com) then switch to a private broker.

Learning and contribution pointers
- Study `RobotArm.ino` to understand how HTTP, WebSocket, and MQTT are integrated.
- Explore `ik.h` and implement a more accurate kinematic model for your arm geometry.
- Add motion planning: implement interpolation between current and target angles to avoid abrupt moves.
- Add persistent storage of calibration values using LittleFS or SPIFFS.
- Add authentication for WebSocket/HTTP if planning to expose the device on untrusted networks.
- Consider adding unit tests (simulated) and improve CI to run static analysis.

Example project flow for learners
1. Assemble hardware and wire servos with a proper power supply.
2. Configure `secrets.h` with your Wi‑Fi and MQTT details.
3. Upload firmware and open Serial Monitor to verify IP.
4. Use the web UI to operate joints manually and observe behavior.
5. Test HTTP API with curl for automation.
6. Add MQTT messages for integration with home automation or ROS bridges.
7. Tweak `ik.h` and test simple IK commands from the web UI or MQTT.
8. Add calibration offsets and save them to flash.

Licensing and sharing
- This repository is intended for educational and open-source learning. Choose a license (MIT, Apache 2.0, etc.) and add a LICENSE file if you plan to publish.

If you want, I can now:
- Push the updated no-comment `RobotArm.ino` and this README into a branch in your repository (tell me the branch name and commit message).
- Add per-servo offset support and an example calibration page in the web UI.
- Implement motion smoothing and a basic safety kill-switch endpoint.

Tell me which next step you prefer and I will proceed.
```
