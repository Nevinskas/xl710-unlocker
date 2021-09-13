# xl710-unlocker

This repo explains how to unlock X710 to accept any SFP module.
Symptom by connecting non-intel SFP module:

	i40e 0000:04:00.1: Rx/Tx is disabled on this device because an unsupported SFP module type was detected.
	i40e 0000:04:00.1: Refer to the Intel(R) Ethernet Adapters and Devices User Guide for a list of supported modules.

This tool was tested on `X710-DA2` card.

### Update firmware

Use intel [NVM utility][1] to upgrade firmware. I was using `nvmupdatepackage_v8_40` and upgraded x710-2DA to `8.64` firmware.

[1]:https://www.intel.com/content/www/us/en/download/18190/24769/non-volatile-memory-nvm-update-utility-for-intel-ethernet-network-adapter-700-series.html
### Reading eeprom and finding registers

First tool to use is `mytool.c`. Replace `FILL_ME_IN` with correct values, build and read eeprom to file:

	./mytool 0 0x8000 > eeprom.txt

Open `eeprom.txt` and find 4 values `000b`: it should be in around range of `0x6800`-`0x7000` and offset between `000b` values should be around `0xb`-`0xf`.

In my case `000b` was at `0x6947` and offset between values `0xe`.

### Poking values

It seems that the important register is floating around in eeprom.
Basically it should be:
 - 11 bit enabled register value
 - The same offset from the `000b` value.
 - 4 identical register values

In my case it was `0x1` form `000b` value, at: `0x6948`, `0x6956`, `0x6964`, `0x6972`. The value was `0x6b0c`.

Replace `FILL_ME_IN` with gathered values, build and run.

After successful poke, reboot the machine.

##### Useful links

[Original repo](https://github.com/terpstra/xl710-unlocker)

[Mailing message explained](https://sourceforge.net/p/e1000/mailman/message/34988514/)

[GH issue](https://github.com/terpstra/xl710-unlocker/issues/4)
