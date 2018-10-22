Hardware:
Board:	ESP32 Dev Module
Description:
The firmware file is not loaded correctly. In the old version this file was loaded correctly.

Sketch:
//Change the code below by your sketch
/*
To upload through terminal you can use: curl -F "image=@firmware.bin" esp8266-webupdate.local/update
*/

#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>
#include <ESPmDNS.h>
#include <Update.h>

const char* host = "esp32-webupdate";
const char* ssid = "rtk59";
const char* password = "ntktajyntktajy";

WebServer server(80);
const char* serverIndex = "

";
void setup(void) {
Serial.begin(115200);
Serial.println();
Serial.println("Booting Sketch...");
WiFi.mode(WIFI_AP_STA);
WiFi.begin(ssid, password);
if (WiFi.waitForConnectResult() == WL_CONNECTED) {
MDNS.begin(host);
server.on("/", HTTP_GET, {
server.sendHeader("Connection", "close");
server.send(200, "text/html", serverIndex);
});
server.on("/update", HTTP_POST, {
server.sendHeader("Connection", "close");
server.send(200, "text/plain", (Update.hasError()) ? "FAIL" : "OK");
ESP.restart();
}, {
HTTPUpload& upload = server.upload();
if (upload.status == UPLOAD_FILE_START) {
Serial.setDebugOutput(true);
Serial.printf("Update: %s\n", upload.filename.c_str());
if (!Update.begin()) { //start with max available size
Update.printError(Serial);
}
} else if (upload.status == UPLOAD_FILE_WRITE) {
if (Update.write(upload.buf, upload.currentSize) != upload.currentSize) {
Update.printError(Serial);
}
} else if (upload.status == UPLOAD_FILE_END) {
if (Update.end(true)) { //true to set the size to the current progress
Serial.printf("Update Success: %u\nRebooting...\n", upload.totalSize);
} else {
Update.printError(Serial);
}
Serial.setDebugOutput(false);
}
});
server.begin();
MDNS.addService("http", "tcp", 80);

Serial.printf("Ready! Open http://%s.local in your browser\n", host);
} else {
Serial.println("WiFi Failed");
}
}

void loop(void) {
server.handleClient();
delay(1);
}

Debug Messages:
rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1324
load:0x40078000,len:7788
ho 0 tail 12 room 4
load:0x40080400,len:6448
entry 0x400806e8

Booting Sketch...
Ready! Open http://esp32-webupdate.local in your browser
Update: Esp32.bin
Update Success: 752448
Rebooting...
ets Jun 8 2016 00:22:57

rst:0xc (SW_CPU_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1324
load:0x40078000,len:7788
ho 0 tail 12 room 4
load:0x40080400,len:6448
entry 0x400806e8
assertion "false && "item should have been present in cache"" failed: file "/Users/ficeto/Desktop/ESP32/ESP32/esp-idf-public/components/nvs_flash/src/nvs_item_hash_list.cpp", line 85, function: void nvs::HashList::erase(size_t)
abort() was called at PC 0x400ec2af on core 0

Backtrace: 0x400881d4:0x3ffcfba0 0x400882d3:0x3ffcfbc0 0x400ec2af:0x3ffcfbe0 0x400f5d2c:0x3ffcfc10 0x400f63e6:0x3ffcfc30 0x400f6715:0x3ffcfc80 0x400f57dc:0x3ffcfce0 0x400f5336:0x3ffcfd30 0x400f53cf:0x3ffcfd50 0x400f541a:0x3ffcfd70 0x400e6318:0x3ffcfd90 0x4012d993:0x3ffcfdb0 0x400edc5e:0x3ffcfde0

Rebooting...
ets Jun 8 2016 00:22:57

rst:0xc (SW_CPU_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1324
load:0x40078000,len:7788
ho 0 tail 12 room 4
load:0x40080400,len:6448
entry 0x400806e8
assertion "false && "item should have been present in cache"" failed: file "/Users/ficeto/Desktop/ESP32/ESP32/esp-idf-public/components/nvs_flash/src/nvs_item_hash_list.cpp", line 85, function: void nvs::HashList::erase(size_t)
abort() was called at PC 0x400ec2af on core 0

Backtrace: 0x400881d4:0x3ffcfba0 0x400882d3:0x3ffcfbc0 0x400ec2af:0x3ffcfbe0 0x400f5d2c:0x3ffcfc10 0x400f63e6:0x3ffcfc30 0x400f6715:0x3ffcfc80 0x400f57dc:0x3ffcfce0 0x400f5336:0x3ffcfd30 0x400f53cf:0x3ffcfd50 0x400f541a:0x3ffcfd70 0x400e6318:0x3ffcfd90 0x4012d993:0x3ffcfdb0 0x400edc5e:0x3ffcfde0

Rebooting...
ets Jun 8 2016 00:22:57

rst:0xc (SW_CPU_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1324
load:0x40078000,len:7788
ho 0 tail 12 room 4
load:0x40080400,len:6448
entry 0x400806e8
assertion "false && "item should have been present in cache"" failed: file "/Users/ficeto/Desktop/ESP32/ESP32/esp-idf-public/components/nvs_flash/src/nvs_item_hash_list.cpp", line 85, function: void nvs::HashList::erase(size_t)
abort() was called at PC 0x400ec2af on core 0

Backtrace: 0x400881d4:0x3ffcfba0 0x400882d3:0x3ffcfbc0 0x400ec2af:0x3ffcfbe0 0x400f5d2c:0x3ffcfc10 0x400f63e6:0x3ffcfc30 0x400f6715:0x3ffcfc80 0x400f57dc:0x3ffcfce0 0x400f5336:0x3ffcfd30 0x400f53cf:0x3ffcfd50 0x400f541a:0x3ffcfd70 0x400e6318:0x3ffcfd90 0x4012d993:0x3ffcfdb0 0x400edc5e:0x3ffcfde0

Rebooting...
ets Jun 8 2016 00:22:57

rst:0xc (SW_CPU_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1324
load:0x40078000,len:7788
ho 0 tail 12 room 4
load:0x40080400,len:6448
entry 0x400806e8
assertion "false && "item should have been present in cache"" failed: file "/Users/ficeto/Desktop/ESP32/ESP32/esp-idf-public/components/nvs_flash/src/nvs_item_hash_list.cpp", line 85, function: void nvs::HashList::erase(size_t)
abort() was called at PC 0x400ec2af on core 0

Backtrace: 0x400881d4:0x3ffcfba0 0x400882d3:0x3ffcfbc0 0x400ec2af:0x3ffcfbe0 0x400f5d2c:0x3ffcfc10 0x400f63e6:0x3ffcfc30 0x400f6715:0x3ffcfc80 0x400f57dc:0x3ffcfce0 0x400f5336:0x3ffcfd30 0x400f53cf:0x3ffcfd50 0x400f541a:0x3ffcfd70 0x400e6318:0x3ffcfd90 0x4012d993:0x3ffcfdb0 0x400edc5e:0x3ffcfde0

Rebooting...
ets Jun 8 2016 00:22:57
