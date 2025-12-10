# Basics
Based on MQTT/HTTP communication discovered through Wireshark capture while running the ElegooSlicer software connected to the printer.  
> **Current software version: `ElegooSlicer_Windows_Installer_V1.2.0.19.exe`**  

All messages on the MQTT broker are JSON encoded

MQTT TCP Port: `1883`

MQTT Over Websocket TCP Port: `9001`

Printer Chamber Camera TCP Port: `8080`

Printer Chamber Camera URL: `http://{PRINTER_IP}:8080/?action=stream`

Send Gcode Files To The Printer HTTP TCP Port: `80`

---

## Connect to Printer MQTT via LAN

URL: `mqtt://{PRINTER_IP}:1883`

TLS: **no**

Authentication: **required**

**Username:** `elegoo`

**Password:** `123456`

## MQTT Authentication

**Request**

Topics: `elegoo/{MACHINE_SN}/api_register`

Payload:
```json
{
    "request_id": "{MQTT_CLIENT_ID}",
    "client_id": "{MQTT_CLIENT_ID}"
}
```

**Response**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/register_response`

Payload:
```json
{
    "client_id": "{MQTT_CLIENT_ID}",
    "error": "ok"
}
```

> `{MQTT_CLIENT_ID}` is mqtt_client_id, you can define the name as you like.
>- For example: MQTT_CLIENT_ID is `ecc2_ha`

```json
Request
{
    "request_id": "ecc2_ha",
    "client_id": "ecc2_ha"
}

Response
{
    "client_id": "ecc2_ha",
    "error": "ok"
}
```

## MQTT Heartbeat

**Request**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_request`

Payload:
```json
{
    "type": "PING"
}
```

**Response**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_response`

Payload:
```json
{
    "type": "PONG"
}
```

> [!IMPORTANT] **The client needs to send a heartbeat packet every 30 seconds to maintain execution permission.**  
> 
> **Heartbeat packet not sent for over 1 minute, although the MQTT connection is still maintained, it will be denied command execution permission by the MQTT broker.**


## CMD Heartbeat

[Heartbeat = 1052]

**Request**

Payload:
```json
TODO
```

**Response**

Payload:
```json
TODO
```

## Request Topic And Response Topic

**Request**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_request`

Payload:
```json
{
    "id": 0,
    "method": 1001
}
```

**Response**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_response`

Payload:
```json
{
    "id": 0,
    "method": 1001,
    "result": {
        "error_code": 0,
        ...
        ...
        ...
    }
}
```

## MessageAutoReport

**Response**

Topics: `elegoo/{MACHINE_SN}/api_status`

Payload:
```json
{
    "id": 1113,
    "method": 6000,
    "result": {
        "extruder": {
            "temperature": 24
        }
    }
}
```
>After subscribing to this `elegoo/{MACHINE_SN}/api_status` topic, the printer automatically pushes the fields with the changed values to the client.

## CMD GetSystemInfo

**Request**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_request`

Payload:
```json
{
    "method": 1001,
    "id": 5
}
```

**Response**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_response`

Payload:
```json
{
    "id": 5,
    "method": 1001,
    "result": {
        "error_code": 0,
        "hardware_version": "",
        "hostname": "Centauri Carbon 2",
        "ip": "192.168.31.11",
        "machine_model": "Centauri Carbon 2",
        "protocol_version": "1.0.0",
        "sn": "{MACHINE_SN}",
        "software_version": {
            "mcu_version": "00.00.00.00",
            "ota_version": "01.01.16.22",
            "soc_version": ""
        }
    }
}
```

## CMD GetBasicInfo

**Request**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_request`

Payload:
```json
{
    "method": 1002,
    "id": 6
}
```

**Response**

Topics: `elegoo/{MACHINE_SN}/{MQTT_CLIENT_ID}/api_response`

Payload:
```json
{
    "id": 6,
    "method": 1002,
    "result": {
        "error_code": 0,
        "external_device": {
            "camera": true,
            "type": "0303",
            "u_disk": false
        },
        "extruder": {
            "target": 0,
            "temperature": 22
        },
        "fans": {
            "aux_fan": {
                "speed": 0
            },
            "box_fan": {
                "speed": 0
            },
            "controller_fan": {
                "speed": 0
            },
            "fan": {
                "speed": 0
            },
            "heater_fan": {
                "speed": 0
            }
        },
        "gcode_move": {
            "extruder": 0,
            "speed": 3000,
            "speed_mode": 1,
            "x": 52.5,
            "y": 264,
            "z": 80
        },
        "heater_bed": {
            "target": 0,
            "temperature": 18
        },
        "led": {
            "status": 1
        },
        "machine_status": {
            "exception_status": [],
            "progress": 99,
            "status": 1,
            "sub_status": 0
        },
        "print_status": {
            "bed_mesh_detect": true,
            "current_layer": 250,
            "enable": true,
            "filament_detect": false,
            "filename": "ECC2_0.4__PETG 245 70 16_0.2_57m22s.gcode",
            "print_duration": 3699,
            "remaining_time_sec": 34,
            "state": "complete",
            "total_duration": 3828,
            "uuid": "9fab4208-b457-4f54-8172-2f9c9f686978"
        },
        "tool_head": {
            "homed_axes": ""
        },
        "ztemperature_sensor": {
            "measured_max_temperature": 0,
            "measured_min_temperature": 0,
            "temperature": 18
        }
    }
}
```

## Return error_code

> Return code after method execution 

**Response -> "result" -> "error_code"**
```
{
    0: "Success",
    1000: "TokenValidationFailed",
    1001: "UnknownInterface",
    1002: "FolderOpenFailed",
    1003: "InvalidParameter",
    1004: "FileWriteFailed",
    1005: "UpdateTokenFailed",
    1006: "SendUpdateMosFailed",
    1007: "FileDeleteFailed",
    1008: "ResponseDataEmpty",
    1009: "PrinterBusy",
    1010: "PrinterNotPrinting",
    1011: "FileCopyFailed",
    1012: "TaskNotFound",
    1013: "DatabaseOperationFailed",
    1014: "GcodeFileInvalid",
    1015: "ThumbnailNotExist",
    1016: "ParseThumbnailFailed",
    1017: "UDiskNotDetected",
    1018: "UDiskRemoved",
    1019: "TimeLapseVideoGenerateFailed",
    1020: "TimeLapseVideoNotExist",
    1021: "PrintFileNotExist",
    1026: "BedMeshDataMissing",
    9000: "FileOffsetMismatch",
    9001: "FileWriteOpenFailed",
    9002: "FileWriteFailed",
    9003: "FileSeekFailed",
    9004: "MD5CheckFailed",
    9005: "NoCancelNeeded",
    9006: "CancelFailed",
    9007: "UploadPathNotExist",
    9008: "MD5CheckFailedSystemError",
    9009: "MD5CheckFailedReadError",
    9010: "UploadDeleteSameNameFileFailed",
    9999: "UnknownError"
}
```

## Return exception_status

**Response -> "result" -> "machine_status" -> "exception_status"**
```
{
    701: "MainboardFanError",
    702: "HeatbreakFanError",
    703: "ModelFanError",
    304: "ZHomingFailed",
    704: "LevelingFailed",
    104: "NozzleTempSensorDisconnected",
    105: "NozzleTempSensorShorted",
    103: "NozzleHeatFailed",
    102: "BedTempSensorDisconnected",
    106: "BedTempSensorShorted",
    101: "BedHeatFailed",
    205: "ChamberTempSensorDisconnected",
    206: "ChamberTempSensorShorted",
    401: "AccelerometerChipError",
    605: "PressureSensorDataError",
    801: "MainboardExtruderCommunicationError",
    802: "LevelingSensorControllerCommunicationError",
    803: "CriticalSystemError",
    1101: "ExhaustVentOpenFailed",
    1102: "ExhaustVentCloseFailed",
    705: "AuxiliaryFanError",
    706: "CaseFanError",
    107: "ToolheadOverheatingProtection",
    108: "BedOverheatingProtection",
    1210: "CanvasCommunicationError",
    1211: "CanvasFilamentRunout",
    1220: "ExtruderError",
    1231: "FilamentCutFailed",
    1232: "CutterHandleNotReleased",
    1241: "LoadingError",
    1242: "UnloadFilamentAtToolheadFailed",
    1252: "UnloadFilamentAtToolheadFailed",
    1251: "ToolheadExtrusionFailed",
    1261: "ToolheadFrontCoverDetached",
    1262: "CutterHandleNotReleased",
    1263: "ToolheadExtrusionFailed",
    1264: "ToolheadExtrusionFailed",
    1244: "ToolheadExtrusionFailed",
    1243: "ToolheadExtrusionFailed",
    901: "ChamberTempTooHigh",
    902: "ChamberTempOverheatingProtection",
    903: "MainboardDriverUnitOverheatingProtection",
    904: "UDiskStorageSpaceNotEnough",
    905: "UDiskReadException",
    906: "VersionUpdateFailed",
    707: "ToolheadFrontCoverDetached"
}
```

## Return status

**Response -> "result" -> "machine_status" -> "status"**
```
{
    0: "Initializing",
    1: "Idle",
    2: "Printing",
    3: "Loading",
    4: "UnLoading",
    5: "AutoLeveling",
    6: "PidCalibration",
    7: "ResonanceTester",
    8: "SelfCheck",
    9: "Upgrade",
    10: "ManualHoming",
    11: "FileSending",
    12: "TimeLapseVideoGenerating",
    13: "Extrudering",
    14: "EmergencyStop",
    15: "PowerOffResume"
}
```

## Return sub_status

**Response -> "result" -> "machine_status" -> "sub_status"**
```
{
    0: "Default",
    1041: "EnvironmentTooCold",
    1045: "HotendPreheating",
    1405: "BedPreheating",
    1053: "PidPreheating",
    1054: "PidDetecting",
    1055: "PidCompleted",
    1056: "PidFailed",
    1061: "ExtruderLoading",
    1063: "ExtruderLoadComplete",
    1062: "ExtruderUnloading",
    1064: "ExtruderUnloadingComplete",
    1096: "HotendCooling",
    1906: "BedCooling",
    1133: "NozzleHeating",
    1134: "ManualInsertFilament",
    1135: "BiteFilament",
    1136: "BiteFilamentEnd",
    1143: "CutFilament",
    1144: "EjectFilament",
    1145: "EjectFilamentComplete",
    1150: "LoadStartCanvas",
    1151: "LoadNozzleHeatingCanvas",
    1152: "LoadManualInsertFilamentCanvas",
    1153: "LoadCutFilamentCanvas",
    1154: "LoadDrawFilamentCanvas",
    1155: "LoadPushFilamentCanvas",
    1156: "LoadFlushFilamentCanvas",
    1157: "LoadCompleteCanvas",
    1158: "LoadFailedCanvas",
    1160: "UnloadStartCanvas",
    1161: "UnloadNozzleHeatingCanvas",
    1162: "UnloadCheckFilamentCanvas",
    1163: "UnloadCutFilamentCanvas",
    1164: "UnloadDrawFilamentCanvas",
    1165: "UnloadCompleteCanvas",
    1166: "UnloadFailedCanvas",
    2405: "PowerOffResume",
    2406: "PowerOffResumeComplete",
    2501: "Pausing",
    2502: "Paused",
    2401: "Resuming",
    2402: "Resumed",
    2503: "Stopping",
    2504: "Stopped",
    2075: "Printing",
    2077: "PrintComplete",
    2505: "Interruption",
    2601: "OTAInfoUpdating",
    2603: "InitializeComplete",
    2701: "OTADownloading",
    2702: "OTAExtracting",
    2703: "OTAUpdating",
    2704: "OTAComplete",
    2705: "OTAFailed",
    2801: "Homing",
    2802: "Homed",
    2901: "AutoLeveling",
    2902: "AutoLevelingComplete",
    3000: "FileSending",
    3001: "FileSendingComplete",
    3010: "FileCopying",
    3011: "FileCopyingComplete",
    3020: "TimeLapseVideoGenerating",
    3021: "TimeLapseVideoGeneratingComplete",
    3022: "TimeLapseVideoGeneratingFailed",
    5932: "AccelerometerNormal",
    5933: "AccelerometerError",
    5934: "ResonanceOptimizing",
    5935: "ResonanceOptimized",
    5936: "ResonanceOptimizeFailed"
}
```

## CMD GetMachineStatus

**Request**

Payload:
```json
{
    "method": 1003,
    "id": 5
}
```

**Response**

Payload:
```json
{
    "id": 0,
    "method": 1003,
    "result": {
        "error_code": 0,
        "machine_status": {
            "exception_status": [],
            "progress": -1,
            "status": 1,
            "sub_status": 0
        }
    }
}
```

## CMD GetFanInfo

**Request**

Payload:
```json
{
    "method": 1004,
    "id": 5
}
```

**Response**

Payload:
```json
{
    "id": 0,
    "method": 1004,
    "result": {
        "error_code": 0,
        "fans": {
            "aux_fan": {
                "speed": 0
            },
            "box_fan": {
                "speed": 0
            },
            "controller_fan": {
                "speed": 0
            },
            "fan": {
                "speed": 0
            },
            "heater_fan": {
                "speed": 0
            }
        }
    }
}
```

## CMD GetPrintsInfo

**Request**

Payload:
```json
{
    "method": 1005,
    "id": 5
}
```

**Response**

Payload:
```json
{
    "id": 0,
    "method": 1005,
    "result": {
        "error_code": 0,
        "print_status": {
            "bed_mesh_detect": false,
            "current_layer": 0,
            "enable": true,
            "filament_detect": false,
            "filename": "",
            "print_duration": 0,
            "remaining_time_sec": 0,
            "state": "standby",
            "total_duration": 0,
            "uuid": ""
        }
    }
}
```

## CMD GetHomeStatus

**Request**

Payload:
```json
{
    "method": 1006,
    "id": 5
}
```

**Response**

Payload:
```json
{
    "id": 0,
    "method": 1006,
    "result": {
        "error_code": 0,
        "tool_head": {
            "homed_axes": ""
        }
    }
}
```

## CMD UrgentStop

> [!CAUTION] also known as "emergency stop" or "E-stop"  

**Request**

Payload:
```json
{
    "method": 1007,
    "id": 5
}
```

**Response**

Payload:
```json
{
    "id": 0,
    "method": 1007,
    "result": {
        "error_code": 0
    }
}
```

## CMD SetPrintConfig

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1019,
    "id": 5
}
```

**Response**

Payload:
```json
TODO
```

## CMD StartPrint

**Request**

Payload:
```json
{
    "method": 1020,
    "params": {
        "filename": "ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode",
        "storage_media": "local",
        "config": {
            "delay_video": false,
            "printer_check": false,
            "print_layout": "A",
            "bedlevel_force": false,
            "slot_map": [
                {
                    "t": 0,
                    "canvas_id": 0,
                    "tray_id": 0
                }
            ]
        }
    },
    "id": 15
}
```

**Response**

Payload:
```json
{
    "id": 15,
    "method": 1020,
    "result": {
        "error_code": 0
    }
}
```

## CMD PausePrint

**Request**

Payload:
```json
{
    "method": 1021,
    "id": 17
}
```

**Response**

Payload:
```json
{
    "id": 17,
    "method": 1021,
    "result": {
        "error_code": 0
    }
}
```

## CMD CancelPrint

**Request**

Payload:
```json
{
    "method": 1022,
    "id": 25
}
```

**Response**

Payload:
```json
{
    "id": 25,
    "method": 1022,
    "result": {
        "error_code": 0
    }
}
```

## CMD ResumePrint

**Request**

Payload:
```json
{
    "method": 1023,
    "id": 26
}
```

**Response**

Payload:
```json
{
    "id": 26,
    "method": 1023,
    "result": {
        "error_code": 0
    }
}
```

## CMD Extruder Feed

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1024,
    "id": 26
}
```

**Response**

Payload:
```json
TODO
```

## CMD Extruder Retreat

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1025,
    "id": 26
}
```

**Response**

Payload:
```json
TODO
```

## CMD XYZHomeControl

**Request**

Payload:
```json
{
    "method": 1026,
    "params": {
        "homed_axes": "xyz"
    },
    "id": 9
}
```

> 'homed_axis' field has 4 options.
>- `xyz`
>- `x`
>- `y`
>- `z`

**Response**

Payload:
```json
{
    "id": 9,
    "method": 1026,
    "result": {
        "error_code": 0
    }
}
```

## CMD XYZMoveControl

**Request**

Payload:
```json
{
    "method": 1027,
    "params": {
        "axes": "x",
        "distance": 0.1
    },
    "id": 14
}
```
> 'axes' field has 3 options.
>- `x`
>- `y`
>- `z`
>
> "distance"  field, moving distance increase is `0.1`mm , moving distance decrease is `-0.1`mm

**Response**

Payload:
```json
{
    "id": 14,
    "method": 1027,
    "result": {
        "error_code": 0
    }
}
```

## CMD TemperatureControl For Extruder

**Request**

Payload:
```json
{
    "method": 1028,
    "params": {
        "extruder": 240
    },
    "id": 15
}
```
> "extruder" field is extruder target temperature.

**Response**

Payload:
```json
{
    "id": 15,
    "method": 1028,
    "result": {
        "error_code": 0
    }
}
```

## CMD TemperatureControl For Heaterbed

**Request**

Payload:
```json
{
    "method": 1028,
    "params": {
        "heater_bed": 70
    },
    "id": 16
}
```
> "heater_bed" field is hotbed target temperature.

**Response**

Payload:
```json
{
    "id": 16,
    "method": 1028,
    "result": {
        "error_code": 0
    }
}
```

## CMD LightSwitch

**Request**

Payload:
```json
{
    "method": 1029,
    "params": {
        "power": 1
    },
    "id": 11
}
```

> "power" field has 2 options.
>- `0` is Turn off the LED light
>- `1` is Turn on the LED light

**Response**

Payload:
```json
{
    "id": 11,
    "method": 1029,
    "result": {
        "error_code": 0
    }
}
```

## CMD FanControl For Model Fan

**Request**

Payload:
```json
{
    "method": 1030,
    "params": {
        "fan": 255
    },
    "id": 9
}
```
> "fan" field numerical range `0-255`.
>- `0` is Turn off the Model Fan
>- `255` is Turn on Start the maximum Model Fan

**Response**

Payload:
```json
{
    "id": 9,
    "method": 1030,
    "result": {
        "error_code": 0
    }
}
```

## CMD FanControl For Auxiliary Fan

**Request**

Payload:
```json
{
    "method": 1030,
    "params": {
        "aux_fan": 255
    },
    "id": 9
}
```

> "aux_fan" field numerical range `0-255`.
>- `0` is Turn off the Auxiliary Fan
>- `255` is Turn on Start the maximum Auxiliary Fan


**Response**

Payload:
```json
{
    "id": 9,
    "method": 1030,
    "result": {
        "error_code": 0
    }
}
```

## CMD FanControl For Box Fan

**Request**

Payload:
```json
{
    "method": 1030,
    "params": {
        "box_fan": 255
    },
    "id": 9
}
```

> "box_fan" field numerical range `0-255`.
>- `0` is Turn off the Box Fan
>- `255` is Turn on Start the maximum Box Fan

**Response**

Payload:
```json
{
    "id": 9,
    "method": 1030,
    "result": {
        "error_code": 0
    }
}
```

## CMD PrintSpeedControl

**Request**

Payload:
```json
{
    "method": 1031,
    "params": {
        "mode": 0
    },
    "id": 20
}
```

> "mode" field has 4 options.
>- `0` is Silent
>- `1` is Balanced
>- `2` is Sport
>- `3` is Frenzy

**Response**

Payload:
```json
{
    "id": 20,
    "method": 1031,
    "result": {
        "error_code": 0
    }
}
```

## CMD AutoLeveling

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1032,
    "id": 9
}
```

**Response**

Payload:
```json
TODO
```

## CMD VibrationOptimize

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1033,
    "id": 9
}
```

**Response**

Payload:
```json
TODO
```

## CMD PIDDetect

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1034,
    "id": 9
}
```

**Response**

Payload:
```json
TODO
```

## CMD OneKeyCheck

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1035,
    "params": {
        "ringing_optimize": true,
        "pid_check": true,
        "auto_bed_leveling": true
    },
    "id": 9
}
```

**Response**

Payload:
```json
{
    "id": 9,
    "method": 1035,
    "result": {
        "error_code": 1003
    }
}
```

## CMD GetHistoryTask

**Request**

Payload:
```json
{
    "method": 1036,
    "id": 3
}
```

**Response**

Payload:
```json
{
    "id": 3,
    "method": 1036,
    "result": {
        "error_code": 0,
        "history_task_list": [
            {
                "begin_time": 1763085901,
                "end_time": 1763087284,
                "task_id": "e42d8c31-81ca-4e6a-bdf1-33c6b6e078e8",
                "task_name": "ECC2_0.4__Elegoo PETG _0.2_20m56s.gcode",
                "task_status": 1,
                "time_lapse_video_duration": 0,
                "time_lapse_video_size": 0,
                "time_lapse_video_status": 0,
                "time_lapse_video_url": ""
            },
            {
                "begin_time": 1763088944,
                "end_time": 1763092465,
                "task_id": "42917088-4696-4a91-a934-926cfdfb1af0",
                "task_name": "ECC2_0.4_flowrate_0_Generic PETG _0.2_36m42s.gcode",
                "task_status": 1,
                "time_lapse_video_duration": 0,
                "time_lapse_video_size": 0,
                "time_lapse_video_status": 0,
                "time_lapse_video_url": ""
            },
            {
                "begin_time": 1763705850,
                "end_time": 1763709678,
                "task_id": "9fab4208-b457-4f54-8172-2f9c9f686978",
                "task_name": "ECC2_0.4__PETG 245 70 16_0.2_57m22s.gcode",
                "task_status": 1,
                "time_lapse_video_duration": 0,
                "time_lapse_video_size": 0,
                "time_lapse_video_status": 0,
                "time_lapse_video_url": ""
            }
        ],
        "total": 3
    }
}
```

## CMD GetHistoryTaskDetail

> ***UnTest***

`GetHistoryTaskDetail = 1037`

**Request**

Payload:
```json
{
    "method": 1037,
    "id": 3
}
```

**Response**

Payload:
```json
TODO
```

## CMD HistoryDelete

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1038,
    "params": {
        "list": 
    },
    "id": 3
}
```

**Response**

Payload:
```json
TODO
```

## CMD OTAUpgrade

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1039,
    "id": 3
}
```

**Response**

Payload:
```json
TODO
```

## CMD GetLivingVideoUrl

**Request**

Payload:
```json
{
    "method": 1042,
    "id": 13
}
```

**Response**

Payload:
```json
{
    "id": 13,
    "method": 1042,
    "result": {
        "error_code": 0,
        "url": "http://192.168.31.11:8080/?action=stream"
    }
}
```

## CMD SetDeviceName

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1043,
    "params": {
        "hostname": ""
    },
    "id": 3
}
```

**Response**

Payload:
```json
TODO
```

## CMD GetFileList

- #### 1.For internal storage, Onboard EMMC.  

**Request**  

Payload:  

```json
{
    "method": 1044,
    "params": {
        "storage_media": "local",
        "offset": 0,
        "limit": 20
    },
    "id": 2
}
```

> "storage_media" field has 2 options.
>- `local`
>- `u-disk`

**Response**

Payload:
```json
{
    "id": 2,
    "method": 1044,
    "result": {
        "error_code": 0,
        "file_list": [
            {
                "color_map": [
                    {
                        "color": "#000000",
                        "name": "PLA",
                        "t": 0
                    },
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 1
                    }
                ],
                "create_time": 2145799830,
                "filename": "ECC2_0.4_Elegoo Nameplate_Elegoo PLA _0.2_17m28s.gcode",
                "last_print_time": 0,
                "layer": 30,
                "print_time": 1048,
                "size": 975620,
                "total_filament_used": 5.18,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    },
                    {
                        "color": "#000000",
                        "name": "PLA",
                        "t": 1
                    },
                    {
                        "color": "#FF0000",
                        "name": "PLA",
                        "t": 2
                    }
                ],
                "create_time": 2145799770,
                "filename": "ECC2_0.4_Otter_Elegoo PLA _0.2_15m24s.gcode",
                "last_print_time": 0,
                "layer": 17,
                "print_time": 924,
                "size": 916525,
                "total_filament_used": 3.22,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#000000",
                        "name": "PLA",
                        "t": 0
                    },
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 1
                    }
                ],
                "create_time": 2145799710,
                "filename": "ECC2_0.4_Scraper_Elegoo PLA _0.2_1h23m.gcode",
                "last_print_time": 0,
                "layer": 225,
                "print_time": 4998,
                "size": 6055579,
                "total_filament_used": 42.11,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    },
                    {
                        "color": "#000000",
                        "name": "PLA",
                        "t": 1
                    },
                    {
                        "color": "#FF0000",
                        "name": "PLA",
                        "t": 2
                    },
                    {
                        "color": "#0000FF",
                        "name": "PLA",
                        "t": 3
                    }
                ],
                "create_time": 2145799650,
                "filename": "ECC2_0.4_3DBenchy_Elegoo PLA _0.2_41m50s.gcode",
                "last_print_time": 0,
                "layer": 240,
                "print_time": 2510,
                "size": 4425269,
                "total_filament_used": 15,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    }
                ],
                "create_time": 2145799590,
                "filename": "ECC2_0.4_Vase_Elegoo PLA _0.12_5h17m.gcode",
                "last_print_time": 0,
                "layer": 1629,
                "print_time": 19004,
                "size": 97996914,
                "total_filament_used": 109.63,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#36A8E1",
                        "name": "PLA",
                        "t": 0
                    }
                ],
                "create_time": 2145799530,
                "filename": "ECC2_0.4_CC2 MINI Desk organizer_Elegoo PLA _0.2_1h52m.gcode",
                "last_print_time": 0,
                "layer": 536,
                "print_time": 6705,
                "size": 3643155,
                "total_filament_used": 89.04,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    }
                ],
                "create_time": 2145799470,
                "filename": "ECC2_0.4_The Buddha_Elegoo PLA _0.2_25m47s.gcode",
                "last_print_time": 0,
                "layer": 227,
                "print_time": 1547,
                "size": 5190855,
                "total_filament_used": 9.83,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    }
                ],
                "create_time": 2145799350,
                "filename": "ECC2_0.4_PTFE Tube Tool_Elegoo PLA _0.2_20m47s.gcode",
                "last_print_time": 0,
                "layer": 250,
                "print_time": 1247,
                "size": 3050042,
                "total_filament_used": 6.1,
                "total_print_times": 0,
                "type": "file"
            },
            {
                "color_map": [],
                "create_time": 43,
                "filename": "ECC2_0.4_EiffelTower_Elegoo PLA _0.2_2h5m.gcode",
                "last_print_time": 0,
                "layer": 0,
                "print_time": 0,
                "size": 0,
                "total_filament_used": 0,
                "total_print_times": 0,
                "type": "file"
            }
        ],
        "offset": 0,
        "total": 9
    }
}
```

- #### 2.For external storage, USB Flash Disk, Fat32 format.  

**Request**  

Payload:  

```json
{
"method": 1044,
"params": {
    "storage_media": "u-disk",
    "dir": "/",
    "offset": 0,
    "limit": 20
},
"id": 7
}
```

> "dir" field is optional, ignorable.

**Response**

Payload:

```json
{
    "id": 7,
    "method": 1044,
    "result": {
        "error_code": 0,
        "file_list": [
            {
                "filename": "4.开箱视频",
                "type": "folder"
            },
            {
                "filename": "3.模型",
                "type": "folder"
            },
            {
                "filename": "2.切片软件",
                "type": "folder"
            },
            {
                "filename": "1.使用手册",
                "type": "folder"
            },
            {
                "filename": "System Volume Information",
                "type": "folder"
            },
            {
                "color_map": [
                    {
                        "color": "#FFFFFF",
                        "name": "PLA",
                        "t": 0
                    }
                ],
                "create_time": 1760665400,
                "filename": "ECC2_0.4_The Buddha_Elegoo PLA _0.2_25m47s.gcode",
                "layer": 227,
                "print_time": 1547,
                "size": 5190855,
                "total_filament_used": 9.83,
                "type": "file"
            }
        ],
        "offset": 0,
        "total": 6
    }
}
```

## CMD GetFileThumbnail

**Request**

Payload:
```json
{
    "method": 1045,
    "params": {
        "storage_media": "local",
        "file_name": "ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode"
    },
    "id": 11
}
```
> "storage_media" field has two options.
>- `local`
>- `u-disk`

**Response**

Payload:
```json
{
    "id": 11,
    "method": 1045,
    "result": {
        "error_code": 0,
        "thumbnail": "iVBORw0KGgoAAAANSUhEUgAAAJAAAACQCAYAAADnRuK4AAAEG0lEQVR4Ae3dLU8jURTG8fsNWIciISFBITAoDAaBQyLXIVmDIqEGCwiCJiGVTeg3qK7pVlY0QbWmgo/A5uymSbfpy7zcmTnn3P8veRwI6GR6Ls9hGgIAAAAAAAAAAAAAAAAAAAAAAAAA6HF9ff1LEoA8dnZ2fry8vLzNZrNvSbfb7e3t7e0HYJujo6PjwWDwOb945hmPx18XFxeXAVjn4eHhefnCWY58jdyhAjAnd51er/d728Uzj3ytfE8AZEjOeuEsv6XJ93I3SpS88O/v7x9FLp7FMGAn6PT09EzuIGUvnsW70dXV1c8A/7IMykUjR3/e0pxadzyPHRmwOe47c3t726r6wmHAdkiG2zzH89hhwDZMhtqmLpxVd6MAOxZ7LC2RuxFvacrVNSiXuRsxYCtV5fE8dujTFMnbY2kJfZoCRXssLeG435BYPZaWcNyvUeweS9PdiD6tYpYG5aKhT6uA9uN57NCnRWR9UC4aBuyS5Bcnw6WlF72KMGAXoKXH0hL6tBw09lhaQp+2QWqDctHQp61Q98KXh9CnBbs9lpYk3aelejyPneQGbG89lpYkcdyX263HHktLXA/YKfRYWuJqwGZQbiYuBmwG5WZjtk9jUNYVUwM2PZbOmFhYo8fSH5ULa/RYtqJqYY0ey2YaH7A5nvuIDNi1H/c5nvtKbX3a8gO4ia9UurDGoJxGKunT6LHSS5Q+jUE57ZTq0xiUiST3cZ8ei6xKpj7N64MLSJxs7dPkAur3+5+WfihSX4bD4dfJyclZ2PYW9vT09GbpByPVp91u90Ie5+fnl7ydkclk8l14DeTg4OCYgTrdyDE+yiLa3d3dM3ejtHJzc9MKMckflBiw/UcG5craeRmw5U/c0+nU1C+FZMvj4+NbqIMc90ejEW9pTiKDcu0biqx2+EjjzxySIx4Dts2oeSiDDF2dTqdn6ZeXcuR4fnh4qOu/VhmwbUReo6CZDNhsLuqLjBny2gQL6NN0JXePpQV9WvMx//kb9GnNJFqPpQV9Wn25v7/XPSgXRZ9WbTItfFk3P+5bemEspLYeSwv6tDhppMfSgv8AKRfpsXZ3d/dD6qSTYcDOl+gLX9bxX7DZwseFb0Cftjnqeywt6NP+j6keSwsW1v7FbI+lRcoLa3yOfCSpLaypXPiyTt7SWq2W+z7NbY+lhdcHQCTRY2nhbWHt9fX1I6B+srBmuU8r9eACxGF1Yc3dwpd1lhbW6LGU0t6n0WMZoLVPS27hyzotC2uuP0nZu6b7tMYfXIA4mujT1Dy4AHHU1afRYzk2H7Cruhux8JWI2AtrLHwlKFafxsJX4so8AIIeC3/l7dPosbBSlj6NhS9stO4BECx8IbPlB0DQY6EQOZrTYwEAAAAAAAAAAAAAAAAAAAAAAABQ5g+dXFMuKinx8gAAAABJRU5ErkJggg=="
    }
}
```

> "thumbnail" field can be converted into an image through base64 encoding.

## CMD GetFileDetail

**Request**

Payload:
```json
{
    "method": 1046,
    "params": {
        "storage_media": "local",
        "filename": "ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode"
    },
    "id": 12
}
```

> "storage_media" field has two options.
>- `local`
>- `u-disk`
>
> "filename" field required.

**Response**

Payload:
```json
{
    "id": 12,
    "method": 1046,
    "result": {
        "color_map": [
            {
                "color": "#FFFFFF",
                "name": "PETG",
                "t": 0
            }
        ],
        "create_time": 1764972607,
        "error_code": 0,
        "filename": "ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode",
        "last_print_time": 0,
        "layer": 1,
        "print_time": 1255,
        "size": 65458,
        "total_filament_used": 13.73,
        "total_print_times": 0
    }
}
```

## CMD DeleteFile

**Request**

Payload:
```json
{
    "method": 1047,
    "params": {
        "storage_media": "local",
        "file_path": [
            "ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode"
        ]
    },
    "id": 11
}
```

> "storage_media" field has two options.
>- `local`
>- `u-disk`

**Response**

Payload:
```json
{
    "id": 11,
    "method": 1047,
    "result": {
        "error_code": 0
    }
}
```

## CMD GetCapacity

**Request**

Payload:
```json
{
    "method": 1048,
    "id": 11
}
```

**Response**

Payload:
```json
{
    "id": 11,
    "method": 1048,
    "result": {
        "error_code": 0
    }
}
```

## CMD GetTimeLapseVideoList

> ***UnTest***

**Request**

Payload:
```json
{
    "method": 1051,
    "params": {
        "url": 
    },
    "id": 11
}
```

**Response**

Payload:
```json
TODO
```

## CMD LoadFilamenting

**Request**

Payload:
```json
{
    "method": 2001,
    "params": {
        "canvas_id": 0,
        "tray_id": 0
    },
    "id": 12
}
```
> "canvas_id" field is canvas index number, Currently only `0`.
>
> "tray_id" field is filament spool index number, Currently only support `0-3`.

**Response**

Payload:
```json
{
    "id": 12,
    "method": 2001,
    "result": {
        "error_code": 0
    }
}
```

## CMD UnloadFilamenting

**Request**

Payload:
```json
{
    "method": 2002,
    "params": {
        "canvas_id": 0,
        "tray_id": 0
    },
    "id": 13
}
```
> "canvas_id" field is canvas index number, Currently only `0`.
>
> "tray_id" field is filament spool index number, Currently only support `0-3`.

**Response**

Payload:
```json
{
    "id": 13,
    "method": 2002,
    "result": {
        "error_code": 0
    }
}
```

## CMD SetFilamentInfo

**Request**

Payload:
```json
{
    "method": 2003,
    "params": {
        "canvas_id": 0,
        "tray_id": 0,
        "brand": "Generic",
        "filament_type": "PETG",
        "filament_name": "PETG",
        "filament_code": "0x0100",
        "filament_color": "#FFFFFF",
        "filament_min_temp": 230,
        "filament_max_temp": 260
    },
    "id": 11
}
```
> "brand", "filament_type", "filament_name", "filament_code", "filament_color", "filament_min_temp", "filament_max_temp",  These fields can be queried in the Filaments_V1.2.0.19.json file

**Response**

Payload:
```json
{
    "id": 11,
    "method": 2003,
    "result": {
        "error_code": 0
    }
}
```

## CMD SetAutoFillFilament

**Request**

Payload:
```json
{
    "method": 2004,
    "params": {
        "auto_refill": true
    },
    "id": 12
}
```
> "auto_refill" field has two options, `true` or `false`

**Response**

Payload:
```json
{
    "id": 12,
    "method": 2004,
    "result": {
        "error_code": 0
    }
}
```

## CMD GetCanvasInfo

**Request**

Payload:
```json
{
    "method": 2005,
    "id": 4
}
```

**Response**

Payload:
```json
{
    "id": 4,
    "method": 2005,
    "result": {
        "canvas_info": {
            "active_canvas_id": 0,
            "active_tray_id": -1,
            "auto_refill": true,
            "canvas_list": [
                {
                    "canvas_id": 0,
                    "connected": 1,
                    "tray_list": [
                        {
                            "brand": "Generic",
                            "filament_code": "0x0100",
                            "filament_color": "#FFFFFF",
                            "filament_name": "PETG",
                            "filament_type": "PETG",
                            "max_nozzle_temp": 260,
                            "min_nozzle_temp": 230,
                            "status": 0,
                            "tray_id": 0
                        },
                        {
                            "brand": "Generic",
                            "filament_code": "0x0100",
                            "filament_color": "#FFFFFF",
                            "filament_name": "PETG",
                            "filament_type": "PETG",
                            "max_nozzle_temp": 260,
                            "min_nozzle_temp": 230,
                            "status": 1,
                            "tray_id": 1
                        },
                        {
                            "brand": "",
                            "filament_code": "",
                            "filament_color": "",
                            "filament_name": "",
                            "filament_type": "",
                            "max_nozzle_temp": 0,
                            "min_nozzle_temp": 0,
                            "status": 0,
                            "tray_id": 2
                        },
                        {
                            "brand": "",
                            "filament_code": "",
                            "filament_color": "",
                            "filament_name": "",
                            "filament_type": "",
                            "max_nozzle_temp": 0,
                            "min_nozzle_temp": 0,
                            "status": 0,
                            "tray_id": 3
                        }
                    ]
                }
            ]
        },
        "error_code": 0
    }
}
```

**Response -> "result" -> "canvas_info" -> "canvas_list" -> "tray_list" -> "status"**
```
Empty = 0
preViewLoad = 1
loaded = 2
```

## Send Gcode Files To The Printer 
HTTP TCP Port: `80`
> **The Gcode file will be split into multiple 1MB packets and sent to the printer.**

**Request Header**
```
PUT /upload HTTP/1.1
Content-Type: application/octet-stream
Content-Type: application/octet-stream
Content-Length: 65458
Content-Range: bytes 0-65457/65458
X-File-Name: ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode
X-File-MD5: a766c7a213ae1bd3a89d72f7a1091013
X-Token: 123456
User-Agent: ElegooLink/1.0.4
Accept: application/json
Host: 192.168.31.11
Accept-Encoding: 
```

Payload:
```
ECC2_0.4__PETG 245 70 16_0.2_20m55s.gcode File Content.
```

**Response Header**
```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 40
Content-Type: application/json
Date: Fri, 05 Dec 2025 22:10:06 GMT
Server: libhv/1.3.3
```

Payload:
```json
{
  "error_code": 0,
  "offset": 65457
}
```
