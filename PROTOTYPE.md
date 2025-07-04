# Metering with NodeMCU (obsolete)

**Hardware and configuration described here was previously used as a prototype and is now obsolete.**

## Hardware

- NodeMCU Amica v2 Board (ESP 8266)
- Electric meter: Hichi IR TTL v1.1
- Gas meter: Honeywell IN-Z61 impulse counter

## NodeMCU Wiring

GPIO0: D3 / Boot0 / Flash Button
GPIO1: D10 / Tx0 (USB)
GPIO2: D4 / Tx1 / ESP LED
GPIO3: D9 / Rx0 (USB)
GPIO4: D2
GPIO5: D1
GPIO12: D6
GPIO13: D7 / Rx2 / Electric meter Rx
GPIO14: D5 / Gas meter impulse
GPIO15: D8 / Tx2 / Electric meter Tx
GPIO16: D0 / Board LED

## Firmware Install

Firmware: [Tasmota](https://tasmota.github.io/) ([GitHub](https://github.com/arendst/tasmota), [Webinstaller](https://tasmota.github.io/install/), [Download](https://ota.tasmota.com/tasmota/release/))

**Note: Use `tasmota-scripting.bin` firmware which has scripting and smart home features enabled**

One-time hardware configuration and setup (via serial port or Wifi AP mode):

```text
Backlog Template {"NAME":"NodeMCU","GPIO":[1,3200,576,3232,1,1,1,1,1,1,1,1,320,1],"FLAG":0,"BASE":18}; Ssid1 xxxxxxxxxx; Password1 xxxxxxxxxx; OtaUrl http://ota.tasmota.com/tasmota/release/tasmota-minimal.bin.gz; Timezone 0; MqttHost mqtt.home.zargony.com
```

## Configuration

Console Script (in Tasmota UI select `Consoles` -> `Edit Script`. Make sure `Script enable` is enabled!):

```text
>D
>B
SerialConfig 7E1
TelePeriod 10
=>sensor53 r
>M 2
; +<M>,<rxGPIO>,<type>,<flag>,<parameter>,<jsonPrefix>{,<txGPIO>,<txPeriod>,<cmdTelegram>}
+1,13,o,0,9600,Electr,15
+2,14,c,1,100,Gas
; <M>,<decoder>@<scale><offs>,<label>,<UoM>,<var>,<precision>
1,1-0:1.8.0*255(@0.001,Consumption,Wh,consumption_total,5)
;1,1-0:1.8.1*255(@0.001,Consumption T1,Wh,consumption_t1,5)
;1,1-0:1.8.2*255(@0.001,Consumption T2,Wh,consumption_t2,5)
;1,1-0:2.8.0*255(@0.001,Export,Wh,export_total,5)
;1,1-0:2.8.1*255(@0.001,Export T1,Wh,export_t1,5)
;1,1-0:2.8.2*255(@0.001,Export T2,Wh,export_t2,5)
1,1-0:16.7.0*255(@1,Power,W,power_total,18)
1,1-0:36.7.0*255(@1,Power L1,W,power_l1,18)
1,1-0:56.7.0*255(@1,Power L2,W,power_l2,18)
1,1-0:76.7.0*255(@1,Power L3,W,power_l3,18)
;1,1-0:32.7.0*255(@1,Voltage L1,V,voltage_l1,1)
;1,1-0:52.7.0*255(@1,Voltage L2,V,voltage_l2,1)
;1,1-0:72.7.0*255(@1,Voltage L3,V,voltage_l3,1)
1,1-0:96.5.0*255(@#),Status,,status,0
;1,1-0:0.0.0*255(@#),Owner Identification,,owner_id,0
;1,1-0:96.1.0*255(@#),Device Identification,,device_id,0
;1,0-0:96.8.0*255(@#),Time of Operation,,time,0
2,1-0:1.8.0*255(@100,Consumption,m³,consumption_total,2)
#
```

<!-- Example serial dump:
14:06:30.096 : /EBZxxxxxxxxxxx_xxx
14:06:30.098 : 1-0:0.0.0*255(1EBZxxxxxxxxxx)
14:06:30.146 : 1-0:96.1.0*255(1EBZxxxxxxxxxx)
14:06:30.196 : 1-0:1.8.0*255(001234.12345678*kWh)
14:06:30.198 : 1-0:16.7.0*255(000370.35*W)
14:06:30.246 : 1-0:36.7.0*255(000123.45*W)
14:06:30.248 : 1-0:56.7.0*255(000123.45*W)
14:06:30.296 : 1-0:76.7.0*255(000123.45*W)
14:06:30.346 : 1-0:96.5.0*255(00112233)
14:06:30.349 : 0-0:96.8.0*255(00112233)
14:06:30.350 : !
-->

## Maintenance

### Electric Metering (meter 1)

- Disable parsing SML and dump to console instead: `sensor53 d1`
- Turn off dumps and resume normal operation: `sensor53 d0`

### Gas Metering (Meter 2)

- Set/adjust counter: `sensor53 c1 12345` (sets gas meter to 123.45 m³)
