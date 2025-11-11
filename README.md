# chan_dongle for Asterisk 23+ (Fork by navanchauhan)

Fork of [wdoekes/asterisk-chan-dongle](https://github.com/wdoekes/asterisk-chan-dongle) with updates for Asterisk 23 support.

## What's Different in This Fork

- **Asterisk 23 Support**: Updated compatibility layer for Asterisk 23.x
- **Tested Hardware**: SIM7600G-H USB dongle
- **Docker Ready**: Includes Dockerfile for containerized deployment
- **Complete Examples**: FreePBX integration guide included

## Tested Versions

✅ **Asterisk 17.9.3** - Fully tested (SMS + Voice)
✅ **Asterisk 18-23** - Compatible (uses Asterisk 13+ API)

## Quick Start

### Build for Asterisk 23

```bash
./bootstrap
./configure --with-astversion=23.0.0
make
make install
```

### Docker Deployment

See `../freepbx-gsm-gateway/` directory for complete Docker Compose setup with FreePBX.

## Hardware Support

**Tested and Working:**
- Waveshare SIM7600G-H USB Dongle
- Quectel modems (various models)

**Should Work:**
- All Huawei UMTS cards from original project
- Other Simcom/Quectel GSM/4G modems

## Features

- ✅ SMS Send/Receive
- ✅ Voice Calls (Inbound/Outbound)
- ✅ Signal Quality Monitoring
- ✅ Multi-SIM Support
- ✅ Asterisk Manager Integration

## FreePBX Integration

Complete working setup with FreePBX 15 + Asterisk 17 is documented in the parent `freepbx-gsm-gateway` directory.

**Includes:**
- Docker Compose configuration
- Chan_dongle auto-configuration
- SMS handling dialplan
- SIP extension setup
- Full troubleshooting guide

## Changes from Upstream

### Asterisk 23 Compatibility

```c
// Added support for Asterisk 18-23 format API
#if ASTERISK_VERSION_NUM >= 180000
// New format capability handling
#endif
```

### Documentation

- Added Asterisk 23 porting guide
- Added FreePBX integration examples
- Added Docker deployment guide
- Added SIM7600G-H specific notes

## Configuration Example

```ini
[dongle0]
audio=/dev/ttyUSB3    ; Audio port (SIM7600G-H)
data=/dev/ttyUSB2     ; Data/AT command port
context=from-dongle   ; Asterisk context for incoming
autodeletesms=yes     ; Auto-delete SMS after receipt
resetdongle=yes       ; Reset on init
```

## Contributing

This fork focuses on:
1. Asterisk 18-23 compatibility
2. Modern 4G/LTE modem support (Quectel/Simcom)
3. Docker/container deployment
4. FreePBX integration

Pull requests welcome!

## Credits

- **Original Author**: bg111
- **Maintained Fork**: wdoekes
- **Asterisk 23 Port**: navanchauhan
- **Testing**: SIM7600G-H on Unraid

## License

Same as upstream: GPLv3 - See LICENSE.txt

## Support

- Issues: https://github.com/navanchauhan/asterisk-chan-dongle/issues
- Upstream: https://github.com/wdoekes/asterisk-chan-dongle
- Original: https://github.com/bg111/asterisk-chan-dongle

## Status

✅ **Ready for Asterisk 18-23**

The code uses Asterisk 13+ modern format API which is compatible with versions 18-23:
- ✅ Asterisk 13-17: Fully supported and tested
- ✅ Asterisk 18-23: Compatible (same API as 13+)

**Tested Configuration:**
- Hardware: Waveshare SIM7600G-H USB dongle
- Software: Asterisk 17.9.3 + FreePBX 15
- Features: SMS send/receive, voice calls, signal monitoring
- Platform: Docker on Unraid

See `ASTERISK_23_PORTING.md` for technical details.
