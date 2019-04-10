## LoRaWAN compressing variables to packets 

To send data via LoRaWAN network, we want to send as small packets as possible and capture all the information that we want to send.

* Each variable can be limited to the maximum and minimum value (for example -20.0 to 40.0) and fixed precision based on the number of bytes (8, 16, custom(5, 6, ...)).	
* Convert the value to the specified precision and save it to uint8_t, uint16_t or as appropriate concatenate.
```uint32_t get_bits(float x, float min, float max, int precision)```
  * for example our variable temperature from sensor is in the range of -20 deg C to 40 deg C with known precision of 0.5 degree, giving us at best 120 differnte values, thus using 8 bit resolution is more then enough. `get_bits(temperature,-20,60,8)`
* STRUCT: Define the structure and enter all the limited variables that you want in the package.
* UNION: Make a union between the structure and a byte array (length equal to STRUCT length).

Example of the packet definition and union:

```
typedef struct XYZ_packet{
  uint8_t   x; 
  uint8_t   y;
  uint8_t   z; 
}__attribute__((packed));

typedef union LoRaWAN_Packet{
  XYZ_packet data;
  uint8_t bytes[sizeof(XYZ_packet)];
};

LoRaWAN_Packet packet_xyz;
 ```

The variable is then set as follows: `packet_xyz.data.x=get_bits(temperature,-20,60,8)` if we use the above temperature example. LoraWAN packet is then sent as `LoRaWAN.sendPacket( &packet_data.bytes[0], sizeof(XYZ_packet));`


## Encoder function get_bits (C)
```
// Function for scaling values of sensor
// Adjusting parameters:
// SET:          variable| min range| max range| precision (number of bits)
uint32_t get_bits(float x, float min, float max, int precision){
  int range = max - min;
  if (x <= min){
    x = 0;
  }
  else if (x >= max){
    x = max - min;
  }
  else{
    x -= min;
  }
  double new_range = (pow(2, precision) - 1) / range;
  uint32_t out_x = x * new_range;
  return out_x;
}

```

## Decoder function get_bits (javascript)

```
function get_num(x, min, max, precision, round) {
  
	var range = max - min;
	var new_range = (Math.pow(2, precision) - 1) / range;
	var back_x = x / new_range;
	
	if (back_x===0) {
		back_x = min;
	}
	else if (back_x === (max - min)) {
		back_x = max;
	}
	else {
		back_x += min;
	}
	return Math.round(back_x*Math.pow(10,round))/Math.pow(10,round);
}
```
use it as `decoded.temp = get_num(bytes[0],-20,80,6,1);` with the same values as before, additionally you specify the rounding precision
