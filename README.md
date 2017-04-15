# SmartThingsSmarterCoffee

* __SmartThings__ - home automation system that communicates with devices over ZigBee, Z-Wave, or HTTP
* __Smarter Coffee__ - A WiFi coffee machine that can be controlled with binary commands over TCP sockets and UDP broadcasts
* __SmartThingsSmarterCoffee__ - A Node.js server that converts REST calls from SmartThings to Smarter Coffee binary packets

### Notes
* Tested on Node.js 10.6.2. It may work on other versions.
* Tested on Mac OS and Raspberry Pi Raspian. Should work on Windows, but I haven't tried it.
* When the server starts, it will discover coffee machine(s) via UDP broadcast. Coffee machine(s) must already be on the same network.
* To control with SmartThings, you must install a [Smart App](https://github.com/petermajor/SmartThings/blob/master/apps/SmarterManager.groovy) and a [Device Handler](https://github.com/petermajor/SmartThings/blob/master/devices/SmarterCoffee.groovy).

### Usage
1. Clone the repository into a folder.
2. Run `npm install` from the top folder of the clone.
3. Run `node app.js` from the top folder of the install.

### API

#### GET /api/device

Map of known coffee machines.

Example response:
```
{
  "18fe34f86975": {
    "id": "18fe34f86975",
    "mac": "18:fe:34:f8:69:75",
    "ip": "192.168.1.15",
    "name": "Smarter Coffee Machine"
  }
}
```

The server supports more than one machine on the network.
Use the `id` property to identify which machine you would like to preform operation for.

#### GET /api/device/{id}

Gets the status of the specified machine.

Example response:
```
{
    "id": "18fe34f86975",
    "mac": "18:fe:34:f8:69:75",
    "ip": "192.168.1.15",
    "name": "Smarter Coffee Machine"
}
```

#### POST /api/device/{id}/cups

Overrides the default setting for the number of cups to brew.

Payload:
```
{ "cups": 3 }
```

`cups` must be a value between 1 and 12 inclusive.

#### POST /api/device/{id}/strength

Overrides the default setting for the strength of coffee to brew.

Payload:
```
{ "strength": 1 }
```

`strength` must be 0 (weak), 1 (medium) or 2 (strong).

#### POST /api/device/{id}/brew/on

Starts brewing coffee. 

If the payload is empty then coffee with default settings is brewed.

If the payload is not empty then the settings in the payload are used.

Payload:
```
{
    "cups": 3,
    "isGrind": true,
    "strength": 2
}
```

`cups` must be a value between 1 and 12 inclusive.
`isGrind` must be true (grind) or false (filter).
`strength` must be 0 (weak), 1 (medium) or 2 (strong).

#### POST /api/device/{id}/brew/off

Stops brewing if one was in progress. 

Payload:
none

#### POST /api/device/{id}/grind

Sets the machine to 'grind' or 'filter' mode. 

Payload:
```
{ "isGrind": true }
```

`isGrind` must be true (grind) or false (filter).

#### POST /api/device/{id}/hotplate/on

Turns on the hotplate only. 

If the payload is empty then hotplate will be turned on for five minutes.

If the payload is not empty then the hotplate will be turned on for the number of mins specified in the payload.

Payload:
```
{ "mins": 10 }
```

`mins` must be a value between 1 and 30 inclusive.

#### POST /api/device/{id}/hotplate/off

Turns off the hotplate. 

Payload:
none
