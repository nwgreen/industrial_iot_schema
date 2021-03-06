# ExoSense™️ Channel and Signal Data Types


**Document Status:** Version 3.0

_Note: Originally this information was a part of the ["ExoSense™️ Channel and Signal Data Schema"](channel-signal_io_schema.md) document._

## Introduction
This document defines the **data types and units** supported by the ExoSense™️ solution and related technologies.  

**Who is this for:**
This document is meant for developers building device software to interact with ExoSense, custom Insight transform functions, custom Rule functions, and contributing to the development of ExoSense and related technologies.  

 It is not required for regular use of the ExoSense application itself, although may be helpful for creating end-user documentation.
 
 The data schema and inteface information can be found here: [ExoSense™️ Channel and Signal Data Schema](channel-signal_io_schema.md)
 
 ![ExoSense Channel / Signal Simple Diagram](simple_channel_signal_diagram.png)
 
### Definitions
The reader of this document should have a grasp on the following items or will need to for this document to make sense.

Term|Description|More Information
--|--|---
ExoSense™️|Industrial IoT Application solution created and offered by [Exosite](exosite.com)|
Device/Gateway|An electronic device with an IP Connection sending data to a platform.  In this case is interacing with directly connected sensors, custom protocol connected sensors, or fieldbus connected equipment|
Sensors|Physical sensors connected to a Device/Gateway via wired or wireless protocol or IO (onboard).  Sensors are typically specific to a unit of measure - e.g. temperature, pressure, etc.| 
Channel|An ExoSense concept to identify an **individual stream** of information sent to ExoSense by an **unique device** that is specific to a type and with a specific unit of measure from that local physical environment (e.g. Temperature or Valve 1 status).  Can also be information such as memory on the device, status information, etc.|Typically a device is sending many channels of data for all of the sensors that are connected.
Signal|An ExoSense concept similiar to channel but is the part of a virtual Asset object that describes and stores the data.  Signals can be transformed, exported, visualzied, and have rules ran on them.  The source for a signal is typically a device channel but doesn't have to be.  The signal essentially subscribes to it's source.|
Asset|An ExoSense concept for digitizing an Asset (a machine, system, equipment, etc)|
Fieldbus|Industrial protocols like Modbus TCP or RTU, J1939, CANOpen, etc that allow machines, controls, equipment to have a standard way to talk to each other.|



## Data Type Definition

Each channel requires to be set as a specific type which should be the source of truth and therefore allows for allowing down-stream support for conversions, visualizations, etc that can be trusted. In the ExoSense application, Asset Signals inherit type/unit from the channel.  Type and units should not be changed for a channel/signal once created, instead a new channel or signal should be created to handle the change.  Each of these types stem from one of the four primitive types.

The section below details the "type" categories supported, the specific types under each category, and the unit options for each type.  

There are two type categories for Data Types
* **Generic Types** - Typically unit less or if a unit is provided, is a description rather than physically measured.  Examples include Number, String, etc.
* **Unit Originated Types** - Physically measurable types of data that include a unit of measure (UOM).  Examples include temperature, electrical current, memory, etc.

**Primitive Types** are used to describe the format of the data type and allows visualizations, transforms, and rules to accept multiple types based on the format.   

### Primitive Types

A primitive type describes the actual underlying encoding / format used for values.   Declaring the primitive type in a channel is optional as the primitive type can be derived from the data type. There are four primitive types: `NUMERIC`, `STRING`, `JSON`, `BOOLEAN`. 

_NOTE: Any type that isn’t given a “Type Key Name”, or a table of properties and their related values, is considered to be a future consideration for inclusion._

## Generic Data Types
For data that may not have units, anything that is dimensionless, or no supported unit types exist. Includes numeric, string, and structured data generic types.  Generic types do not support a measureable unit and therefore standard unit conversions are not supported in the application for generic data types.

#### String (unit-less)
Key (`data_type`): STRING<br>
Accepted Units (`data_unit`): Not Used<br>
Primitive Type (`primitive_type`): STRING<br>
UI Unit Abbreviation: na<br>
Notes: Any string

**Example JSON Channel**
```json
{
  "channels": {
    "005": {
      "display_name": "Machine Status",
      "description": "Device reported status",
      "properties": {
        "data_type": "STRING",
        "primitive_type": "STRING"
      },
      "protocol_config": {
        "report_rate": 60000,
        "timeout": 360000
      }
    }
  }
}
```
**Example String Channel Data (data_in) Packet**
```json
{
  "005":"Good"
}
```

#### JSON (unit-less)
Key (`data_type`): JSON<br>
Accepted Units (`data_unit`): Not Used<br>
Primitive Type (`primitive_type`): JSON<br>
UI Unit Abbreviation: na<br>
Notes: Any JSON blob

**Example JSON Channel**
```json
{
  "channels": {
    "017": {
      "display_name": "Machine Stats",
      "description": "Device reported stats",
      "properties": {
        "data_type": "JSON",
        "primitive_type": "JSON"
      },
      "protocol_config": {
        "report_rate": 60000,
        "timeout": 360000
      }
    }
  }
}
```
**Example Number Channel Data (data_in) Packet**
```json
{
  "017":{"network_errors":0,"RAM":"45 MB"}
}
```

#### Number (unit-less)
Key (`data_type`): NUMBER<br>
Accepted Units (`data_unit`): Not Used<br>
Primitive Type (`primitive_type`): NUMERIC<br>
UI Unit Abbreviation: na<br>
Notes: Any Real Number

**Example JSON Channel**
```json
{
  "channels": {
    "134": {
      "display_name": "Pulses Counted",
      "description": "Times a pulse was counted in given time frame",
      "properties": {
        "data_type": "NUMBER",
        "primitive_type": "NUMERIC"
      },
      "protocol_config": {
        "sample_rate": 10,
        "report_rate": 5000,
        "timeout": 60000
      }
    }
  }
}
```
**Example Number Channel Data (data_in) Packet**
```json
{
  "134":43
}
```

#### BOOLEAN (unit-less)
Key (`data_type`): BOOLEAN<br>
Accepted Units (`data_unit`): Not Used<br>
Primitive Type (`primitive_type`): BOOLEAN<br>
UI Unit Abbreviation: na<br>
Notes: 
* True ("Truthy") accepted values: [`true`, `"on"`, `1`, `"yes"`, any  number that is not `0`]
* False ("Falsy") accepted values: [`false`, `"off"`, `0`, `"no"`]
* All values will be converted to `true` and `false` at ingestion in ExoSense for use by transform insights, rules, and UI panels.  

**Example Boolean Channel Configuration**
```json
{
  "channels": {
    "023": {
      "display_name": "Valve 1 Open",
      "description": "Machine Valve 1 Open State Information",
      "properties": {
        "data_type": "BOOLEAN",
        "primitive_type": "BOOLEAN"
      },
      "protocol_config": {
        "sample_rate": 5000,
        "report_rate": 5000,
        "timeout": 60000
      }
    },
    "025": {
      "display_name": "Valve 2 Open",
      "description": "Machine Valve 2 Open State Information",
      "properties": {
        "data_type": "BOOLEAN",
        "primitive_type": "BOOLEAN"
      },
      "protocol_config": {
        "sample_rate": 5000,
        "report_rate": 5000,
        "timeout": 60000
      }
    }
  }
}
```
**Example Boolean Channel Data (data_in) Packet**
```json
{
  "023":true,
  "025":0
}
```



*Note: Generic types without accepted unit types will not be able to take advantage of unit conversion and other unit specific functionality in ExoSense.*

## Unit Originated Data Types
The following data types require a fixed unit type that is specified as a part of the origination channel configuration (`config_io`) and used as source of truth through the application.

Many of these types will represent base physical measurements (temperature, length, etc), or derived measurements (velocity), as noted in this [Wikipedia article](https://en.wikipedia.org/wiki/List_of_physical_quantities).  The list is assumed to never be complete and future types / units could be added.  Some physical quantities have simply been left off the list for simplicity and assumed lack of need for Industrial IoT applications.  


#### Acceleration
Key (`data_type`): ACCELERATION<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METER_PER_SEC2`: m/s²
* `STANDARD_GRAVITY` : g₀
* `FEET_PER_SEC2`: ft/s²
* `IN_PER_SEC2` : in/s²

Notes: --

#### Amount of Substance
Key (`data_type`): AMOUNT<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `MOLE`: mol

Notes: --

#### Angular acceleration
Key (`data_type`): ANGULAR_ACCEL<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `RAD_PER_SEC2`: rad/s²
* `ROTATIONS_PER_MIN2`: r/min²
* `DEG_PER_SEC2`: deg/s²

Notes: --

#### Angular Velocity / Speed
Key (`data_type`): ANGULAR_VEL<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `RAD_PER_SEC`: rad/s
* `RAD_PER_MIN`: rad/m
* `RAD_PER_HOUR`: rad/hr
* `ROTATIONS_PER_MIN`: rpm
* `DEG_PER_SEC`: deg/s
* `DEG_PER_MIN`: deg/m
* `DEG_PER_HOUR`: deg/hr

Notes: --

#### Area
Key (`data_type`): AREA<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METER2`: m²
* `KILOMETER2`: km²
* `MM2`: mm²
* `FEET2`: ft²
* `INCH2`: in²
* `MILE2`: mi²
* `ACRE`: ac

Notes: --

#### Battery Percentage
Key (`data_type`): BATTERY_PERCENTAGE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PERCENT`: %

Notes: Device diagnostic

#### Capacitance
Key (`data_type`): CAPACITANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `FARAD`: F
* `MILLIFARAD`: mF
* `MICROFARAD`: µF
* `NANOFARAD`: nF
* `PICOFARAD`: pF

Notes: --

#### Currency
Key (`data_type`): CURRENCY<br>
Accepted Units (`data_unit`):
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
`AFN`, `ALL`, `DZD`, `USD`, `EUR`, `AOA`, `XCD`, `ARS`, `AMD`, `AWG`, `AUD`, `AZN`, `BSD`, `BHD`, `BDT`, `BBD`, `BYR`, `BZD`, `XOF`, `BMD`, `BTN`, `INR`, `BOB`, `BOV`, `BAM`, `BWP`, `NOK`, `BRL`, `BND`, `BGN`, `BIF`, `CVE`, `KHR`, `XAF`, `CAD`, `KYD`, `CLF`, `CLP`, `CNY`, `COP`, `COU`, `KMF`, `CDF`, `NZD`, `CRC`, `HRK`, `CUC`, `CUP`, `ANG`, `CZK`, `DKK`, `DJF`, `DOP`, `EGP`, `SVC`, `ERN`, `ETB`, `FKP`, `FJD`, `XPF`, `GMD`, `GEL`, `GHS`, `GIP`, `GTQ`, `GBP`, `GNF`, `GYD`, `HTG`, `HNL`, `HKD`, `HUF`, `ISK`, `IDR`, `XDR`, `IRR`, `IQD`, `ILS`, `JMD`, `JPY`, `JOD`, `KZT`, `KES`, `KPW`, `KRW`, `KWD`, `KGS`, `LAK`, `LBP`, `LSL`, `ZAR`, `LRD`, `LYD`, `CHF`, `MOP`, `MKD`, `MGA`, `MWK`, `MYR`, `MVR`, `MRU`, `MUR`, `XUA`, `MXN`, `MXV`, `MDL`, `MNT`, `MAD`, `MZN`, `MMK`, `NAD`, `NPR`, `NIO`, `NGN`, `OMR`, `PKR`, `PAB`, `PGK`, `PYG`, `PEN`, `PHP`, `PLN`, `QAR`, `RON`, `RUB`, `RWF`, `SHP`, `WST`, `STN`, `SAR`, `RSD`, `SCR`, `SLL`, `SGD`, `XSU`, `SBD`, `SOS`, `SSP`, `LKR`, `SDG`, `SRD`, `SZL`, `SEK`, `CHE`, `CHW`, `SYP`, `TWD`, `TJS`, `TZS`, `THB`, `TOP`, `TTD`, `TND`, `TRY`, `TMT`, `UGX`, `UAH`, `AED`, `USN`, `UYI`, `UYU`, `UZS`, `VUV`, `VEF`, `VND`, `YER`, `ZMW`, `ZWL`<br>

Notes: 
* Currency codes (based on list found here: https://www.iban.com/currency-codes.html) 
* No Unit Abbreviations
* Conversions between currency is complicated and always changing based on world-wide markets.  ExoSense and related technology **will not support currency conversions**.  

#### Data
Key (`data_type`): DATA<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `BYTE`: B
* `KILOBYTE`: KB
* `MEGABYTE`: MB
* `GIGABYTE`: GB
* `TERABYTE`: TB

Notes: --

#### Density
Key (`data_type`): DENSITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `KG_PER_M3`: kg/m³

Notes: --

#### Displacement
Key (`data_type`): DISPLACEMENT<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
*	`METERS`: m
* `CENTIMETERS`: cm
*	`KILOMETERS`: km
*	`MILLIMETERS`: mm
*	`MICRONS`: µ (Micrometer)
*	`FEET`: ft
*	`INCH`: in
*	`YARD`: yd
*	`MILES`: mi

Notes: --

#### Dynamic Viscosity
Key (`data_type`): DYNAMIC_VISCOSITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PASCAL_SEC`: Pa·s
* `MILLIPASCAL_SEC`: mPa·s
* `POISE`: P

Notes: --

#### Kinematic Viscosity
Key (`data_type`): KINEMATIC_VISCOSITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `CENTISTOKES`: cSt
* `STOKES`: St
* `METERS2_PER_SEC`: m2/s

Notes: --


#### Electric Current
Key (`data_type`): ELEC_CURRENT<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `AMPERE`: A
* `MILLIAMP`: mA
* `MICROAMP`: µA
* `KILOAMP`: kA

Notes: --

#### Electrical Conductance
Key (`data_type`): ELEC_CONDUCTANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `SIEMENS`: S
Notes: --

#### Electrical Potential (Voltage)
Key (`data_type`): ELEC_POTENTIAL<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `VOLT`: V
* `MILLIVOLT`: mV
* `MICROVOLT`: µV
* `KILOVOLT`: kV
* `MEGAVOLT`: MV

Notes: --

#### Electrical Resistance
Key (`data_type`): ELEC_RESISTANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `OHM`: Ω
* `MILLIOHM`: mΩ
* `MICROOHM`: µΩ
* `KILOOHM`: kΩ
* `MEGAOHM`: MΩ

Notes: --

#### Electrical Resistivity
Key (`data_type`): ELEC_RESISTIVITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `OHM_METER`: Ω⋅m

Notes: --

#### Energy
Key (`data_type`): ENERGY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `JOULE`: J

Notes:

#### Field Level
Key (`data_type`): FIELD_LEVEL<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `DECIBEL`: dB

Notes: Used for logarithmic measures. Reference: [https://en.wikipedia.org/wiki/Level_(logarithmic_quantity)]


#### Flow (Volumetric)
Key (`data_type`): FLOW<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METERS3_PER_SEC`: m³/s
* `METERS3_PER_HOUR`: m³/h
* `PERCENT`: %
* `SCFM`: SCFM (Standard cubic feet per minute)
* `SCFH`: SCFH (Standard cubic feet per hour)
* `SLPS`: SLPS (Standard Liters Per Second)
* `SLPM`: SLPM (Standard Liters Per Minute)
* `NCMH`: NCMH (Normal Cubic Meter Per Hour)
* `NCMM`: NCMM (Normal Cubic Meter Per Minute)
* `NLPS`: NLPS (Normal Liter Per Second)
* `NLPM`: NLPM (Normal Liter Per Minute)
* `LITERS_PER_SEC`: LPS
* `LITERS_PER_MIN`: LPM
* `LITERS_PER_HOUR`: LPH
* `GALLONS_PER_SEC`: GPS
* `GALLONS_PER_MIN`: GPM
* `GALLONS_PER_HOUR`: GPH

Notes: 
* Flow as PERCENT will not support conversions


#### Flow (Mass)
Key (`data_type`): FLOW_MASS<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `KG_PER_SEC`: kg/s
* `LBS_PER_SEC`: lb/s

Notes: 

#### Force
Key (`data_type`): FORCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `NEWTON`: N

Notes: --

#### Frequency
Key (`data_type`): FREQUENCY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `HERTZ`: Hz
* `KHZ`:KHz
* `MHZ`: MHz

Notes: --


#### GPS / Location
Key (`data_type`): LOCATION<br>
Primitive Type (`primitive_type`): JSON<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `LAT_LONG`
* `LAT_LONG_ALT`

Notes: 
* LAT_LONG contains both latitude and longitude in a JSON structured
* LAT_LONG_ALT unit includes optionally sending altitude and acceleration.  

**JSON payload example:**
```json
{"lat": "{value}","lng":"{value}"}
```
```json
{"lat": "{value}","lng":"{value}","alt":"{value}","acc":"{value}"}
```

#### Heat
Key (`data_type`): HEAT<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `JOULE`: J
* `BTU`: Btu
* `CALORIE`: cal
* `KILOCALORIE`: kcal

Notes: 

#### Humidity
Key (`data_type`): HUMIDITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PERCENT`: %
Notes: --

#### Illuminance
Key (`data_type`): ILLUMINANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `LUX`: lx
* `FOOT_CANDLES`: fc

Notes: 

#### Inductance
Key (`data_type`): INDUCTANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `HENRY`: H
Notes: --

#### Jerk
Key (`data_type`): JERK<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METER_PER_SEC3`: m/s³
Notes: --

#### Length
Key (`data_type`): LENGTH<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
*	`METERS`: m
* `CENTIMETERS`: cm
*	`KILOMETERS`: km
*	`MILLIMETERS`: mm
*	`MICRONS`: µ (Micrometer)
*	`FEET`: ft
*	`INCH`: in
*	`YARD`: yd
*	`MILES`: mi

Notes: --

#### Linear density
Key (`data_type`): LINEAR_DENSITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `KILOGRAM_PER_METER`: kg/m

Notes: --

#### Luminance
Key (`data_type`): LUMINANCE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `CANDELA_PER_METER2`: cd/m2

Notes: --

#### Luminous Flux
Key (`data_type`): LUMINANCE_FLUX<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `LUMENS`: lm

Notes: --

#### Luminous Intensity
Key (`data_type`): LUMINOUS_INTENSITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `CANDELA`: cd

Notes: --

#### Mass
Key (`data_type`): MASS<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `MILLIGRAM`: mg
* `GRAM`: g
* `KILOGRAM`: kg
* `POUND`: lb
* `OZ`: oz
* `TON`: Ton 
* `METRIC_TON`: t
* `SLUG`: slug
* `DRAM`: dr
* `POUNDMASS`: lbm

Notes: --

#### Particles
Key (`data_type`): PARTICLES<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PPM`: ppm
* `PPB`: ppb
* `PPT`: ppt
Notes: Psuedo-units to describe particles as a quantity of quantity. 

#### Percentage
Key (`data_type`): PERCENTAGE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PERCENT: %`

Notes: --

#### pH
Key (`data_type`): PH<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PH`: pH

Notes: --

#### Plane angle
Key (`data_type`): ANGLE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `RADIAN`: rad
* `DEGREE`: °
* `ARCMINUTE`: '
* `ARCSECOND`: "

Notes: --

#### Power
Key (`data_type`): POWER<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `WATT`: W
* `MILLIWATT`: mW
* `KILOWATT` kW
* `MEGAWATT`: MW
* `MICROWATT`: µW
*	`VOLTAMPS`: VA
*	`KILOVOLTAMPS`: KVA
*	`VOLTAMPS_REACTIVE`: var
*	`VOLTAMPS_REACTIVE`: kvar	
*	`HORSEPOWER`: hp

Notes: --

#### Power Level
Key (`data_type`): POWER_LEVEL<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `DECIBEL`: dB

Notes: Used for logarithmic measures. Reference: [https://en.wikipedia.org/wiki/Level_(logarithmic_quantity)]


#### Pressure
Key (`data_type`): PRESSURE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PASCAL`: Pa
* `KILOPASCAL`: kPa
* `BAR`: bar
* `MBAR`: mbar
* `PSI`: psi
* `MBAR`: mbar
* `TORR`: Torr
* `ATMOSPHERE`: atm
* `NEWTON_PER_METER_2`: N/m2
* `MM_HG`: mmHg (Millimetres of Mercury at 0 deg C)
* `IN_H2O`: inH2O (Inches of Water Column at 4 deg C)

Notes: --

#### Signal Strength as Percent
Key (`data_type`): SIGNAL_STRENGTH_PERCENTAGE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `PERCENT`: %

Notes: Device diagnostic

#### Solid angle
Key (`data_type`): SOLID_ANGLE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `STERADIAN`: sr

Notes: --

#### Speed
Key (`data_type`): SPEED<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METER_PER_SEC`: m/s
* `KPH`: km/h
* `MPH`: mph
* `IN_PER_SEC`: ips
* `FEET_PER_SEC`: fps
* `KNOTS`: kn

Notes: --

#### Temperature
Key (`data_type`): TEMPERATURE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `KELVIN`: K
* `DEG_CELSIUS`: °C
* `DEG_FAHRENHEIT`: °F 
* `RANKINE`: °R

Notes: --

#### Time
Key (`data_type`): TIME<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `SECONDS`: s
* `MILLISECOND`: msec
* `MINUTE`: min
* `HOUR`: h
* `DAY`: d
* `YEAR`: yr

Notes: --

#### Torque
Key (`data_type`): TORQUE<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `NEWTON_METER`:  N⋅m
* `FOOT_POUNDS`: lbf⋅ft
* `INCH_POUNDS`: lbf⋅in

Notes: --

#### URL 
Key (`data_type`): URL<br>
Primitive Type (`primitive_type`): JSON<br>
Accepted Units (`data_unit`) with UI unit abbreviation: _Not Used_<br>

Notes: A JSON object that contains at minimum a key `url`.  The url value must be a string that consists of a proper URL address scheme starting with `https`, etc.  Optional `title` key to provide a title that would be displayed in the UI instead of the full URL address. The application (example ExoSense) will define what URL types are actually supported (click-able in the UI).
**_Validation and security checks are not provided by the platform or application.  Recommend only for use of encrypted and password protected URLs.  OEMs implementing device or custom Insight support to generate URL values, do so at their own risk to their users._**

**reference:**
```json
{"url": "{proper_url_value_here}","title":"{optional_title_value_here}"}
```

**example:**
```json
{"url": "https://exosite.com","title":"Device Link"}
```

#### Velocity
Key (`data_type`): VELOCITY<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `METER_PER_SEC`: m/s
* `MILLMETERS_PER_SEC`: mm/s
* `MPH`: mph
* `INCH_PER_SEC`: ips
* `FEET_PER_SEC`: fps

Notes: --

#### Volume
Key (`data_type`): VOLUME<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
*	`METER3`: m³
*	`LITRE`: L
*	`MILLILITRES`: mL
*	`GALLON`: gal
*	`QUARTS`: qt
*	`PINT`: pt
*	`FEET3`: ft³
*	`INCH3`: in³
*	`CENTIMETER3`: cm³
*	`MILLIMETERS3`: mm³
*	`IMPERIAL_GALLONS`: Igal
*	`FLUID_OZ`: fl oz

Notes: --

#### Weight
Key (`data_type`): WEIGHT<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `NEWTON`: N
* `POUND`: lb
* `OUNCE`: oz
* `TON`: Ton (US Short Ton)
* `METRIC_TON`: t

Notes: --

#### Work
Key (`data_type`): WORK<br>
Primitive Type (`primitive_type`): NUMERIC<br>
Accepted Units (`data_unit`) with UI unit abbreviation: <br>
* `JOULE`: J
* `FOOT_POUND`: ft⋅lb

Notes: --


## Change log
### v3.0
* Created this document as seperate from "ExoSense™️ Channel and Signal Data Schema", using v3.0 since the other document is also moving to 3.0.  Will track version history seperately moving forward. 
* Added many new Numeric measurement types and units
* Changed format to show the accepted units and the abbreviations used in the application
* Added BOOLEAN type
* Added URL type
* Added a new inheritied element `primitive_type` to specify the format for each data type
* Removed non-supported data types
