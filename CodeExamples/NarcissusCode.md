### Narcissus Code

---

## This is currently the final code for the Narcissus project. WIP -

---

**Language:** Arduino C++ (ESP32 camera and heart-rate sensor with OSC)

```cpp
#include <WiFi.h>
#include <WiFiUdp.h>
#include <OSCMessage.h>
#include "esp_camera.h"
#include "esp_http_server.h"

// ===== Settings =====
const char *ssid = "YOUR_WIFI_SSID";
const char *password = "YOUR_WIFI_PASSWORD";
IPAddress tdIP(192, 168, 0, 133);  // TouchDesigner PC's IP
const int tdPort = 9000;

// ===== Heart rate settings =====
const int SENSOR_PIN = 1;
const int SAMPLE_INTERVAL_MS = 20;
const int SMOOTHING_WINDOW = 15;
const float BASELINE_ALPHA = 0.02;
const int REFRACTORY_MS = 300;
const float PEAK_DECAY = 0.01;
const float THRESHOLD_RATIO = 0.5;
const float MIN_THRESHOLD = 8;

WiFiUDP udp;

// ===== Camera (GOOUUU ESP32-S3-CAM, same pins as ESP32-S3-EYE) =====
void startCamera() {
camera_config_t c = {};
c.pin_pwdn = -1;
c.pin_reset = -1;
c.pin_xclk = 15;
c.pin_sccb_sda = 4;
c.pin_sccb_scl = 5;
c.pin_d0 = 11;
c.pin_d1 = 9;
c.pin_d2 = 8;
c.pin_d3 = 10;
c.pin_d4 = 12;
c.pin_d5 = 18;
c.pin_d6 = 17;
c.pin_d7 = 16;
c.pin_vsync = 6;
c.pin_href = 7;
c.pin_pclk = 13;
c.xclk_freq_hz = 20000000;
c.ledc_timer = LEDC_TIMER_0;
c.ledc_channel = LEDC_CHANNEL_0;
c.pixel_format = PIXFORMAT_JPEG;
c.frame_size = FRAMESIZE_VGA;  // 640x480
c.jpeg_quality = 12;            // higher number = smaller, faster frames
c.fb_count = 2;
c.fb_location = CAMERA_FB_IN_PSRAM;
c.grab_mode = CAMERA_GRAB_LATEST;

if (esp_camera_init(&c) != ESP_OK) {
    Serial.println("Camera init failed");
    while (true) delay(1000);
}

sensor_t *s = esp_camera_sensor_get();
s->set_vflip(s, 1);
s->set_brightness(s, 1);
s->set_saturation(s, -2);
}

// ===== MJPEG stream at http://<ip>:81/stream =====
esp_err_t streamHandler(httpd_req_t *req) {
httpd_resp_set_type(req, "multipart/x-mixed-replace;boundary=frame");
char header[96];

while (true) {
    camera_fb_t *fb = esp_camera_fb_get();
    if (!fb) return ESP_FAIL;

    int len = snprintf(header, sizeof(header),
                    "\r\n--frame\r\nContent-Type: image/jpeg\r\nContent-Length: %u\r\n\r\n",
                    fb->len);
    esp_err_t res = httpd_resp_send_chunk(req, header, len);
    if (res == ESP_OK) res = httpd_resp_send_chunk(req, (const char *)fb->buf, fb->len);
    esp_camera_fb_return(fb);
    vTaskDelay(pdMS_TO_TICKS(40));  // cap at ~25fps so TD can keep up
    if (res != ESP_OK) return res;  // viewer disconnected
}
}

void startStreamServer() {
httpd_config_t config = HTTPD_DEFAULT_CONFIG();
config.server_port = 81;

httpd_uri_t uri = {};
uri.uri = "/stream";
uri.method = HTTP_GET;
uri.handler = streamHandler;

httpd_handle_t server = NULL;
if (httpd_start(&server, &config) == ESP_OK) {
    httpd_register_uri_handler(server, &uri);
}
}

// ===== OSC helper =====
void sendOSC(const char *address, float value) {
OSCMessage msg(address);
msg.add(value);
udp.beginPacket(tdIP, tdPort);
msg.send(udp);
udp.endPacket();
}

// ===== Heart rate task (runs on its own core) =====
void sensorTask(void *parameter) {
int buffer[SMOOTHING_WINDOW];
long sum = 0;
for (int i = 0; i < SMOOTHING_WINDOW; i++) {
    buffer[i] = analogRead(SENSOR_PIN);
    sum += buffer[i];
}
int index = 0;
float baseline = sum / (float)SMOOTHING_WINDOW;
float peak = MIN_THRESHOLD * 2;
unsigned long lastBeat = 0;
bool aboveThreshold = false;

while (true) {
    // Smooth the raw reading and remove the slow-moving baseline
    int raw = analogRead(SENSOR_PIN);
    sum += raw - buffer[index];
    buffer[index] = raw;
    index = (index + 1) % SMOOTHING_WINDOW;
    float smoothed = sum / (float)SMOOTHING_WINDOW;
    baseline += BASELINE_ALPHA * (smoothed - baseline);
    float signal = smoothed - baseline;

    sendOSC("/heartbeat/signal", signal);

    // Adaptive threshold follows the recent peak height
    peak = (signal > peak) ? signal : peak * (1 - PEAK_DECAY);
    float threshold = max(peak * THRESHOLD_RATIO, MIN_THRESHOLD);

    unsigned long now = millis();
    if (!aboveThreshold && signal > threshold && now - lastBeat > REFRACTORY_MS) {
    aboveThreshold = true;
    unsigned long interval = now - lastBeat;
    lastBeat = now;

    sendOSC("/heartbeat/pulse", 1);
    vTaskDelay(pdMS_TO_TICKS(30));
    sendOSC("/heartbeat/pulse", 0);

    if (interval < 2000) sendOSC("/heartbeat", 60000.0 / interval);
    } else if (signal < threshold * 0.5) {
    aboveThreshold = false;
    }

    vTaskDelay(pdMS_TO_TICKS(SAMPLE_INTERVAL_MS));
}
}

void setup() {
Serial.begin(115200);
startCamera();

WiFi.begin(ssid, password);
WiFi.setSleep(false);
Serial.print("WiFi connecting");
while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
}
Serial.printf("\nStream: http://%s:81/stream\n", WiFi.localIP().toString().c_str());

startStreamServer();
xTaskCreatePinnedToCore(sensorTask, "SensorTask", 4096, NULL, 1, NULL, 0);
}

void loop() {
vTaskDelay(portMAX_DELAY);  // camera server and sensor task do all the work
}
```
