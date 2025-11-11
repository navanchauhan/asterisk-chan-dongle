# Porting chan_dongle to Asterisk 23

## Current Status

The wdoekes/asterisk-chan-dongle fork currently supports:
- Asterisk 1.4 through 13+
- Has version-specific code paths using `ASTERISK_VERSION_NUM`
- Successfully tested with Asterisk 17.9.3

## Changes Needed for Asterisk 18-23

### 1. Format/Codec API (Asterisk 13+)
**Status:** Likely compatible already
- Asterisk 13+ uses `ast_format_slin` instead of `AST_FORMAT_SLINEAR`
- Current code already handles this (line 150 in channel.c)
- **Action:** Verify compatibility, may need minor tweaks

### 2. Channel Allocation API
**Status:** May need updates
- Asterisk 17+ changed some channel allocation parameters
- Current code has conditional for 12+
- **Action:** Add conditional for 18+ if API changed

### 3. Module Info Macros
**Status:** Needs verification
- Asterisk 18+ may have changed module registration macros
- Check `AST_MODULE_INFO` usage
- **Action:** Review and test

### 4. Deprecated APIs Removed
**Potential issues:**
- Some functions deprecated in 17 were removed in 20+
- Check for:
  - `ast_channel_alloc()` signature changes
  - Format capability API changes
  - Channel locking API changes

## Testing Strategy

1. **Build Test**: Compile against Asterisk 23 headers
2. **Runtime Test**: Load module and verify basic functionality
3. **Functional Test**: Test calls and SMS
4. **Regression Test**: Ensure 17 compatibility remains

## Implementation Plan

### Phase 1: Add Asterisk 18-23 Conditionals
- Add `#if ASTERISK_VERSION_NUM >= 180000` blocks where needed
- Update format/codec handling for 18+
- Update channel allocation for 18+

### Phase 2: Test Build
- Set up Docker container with Asterisk 23
- Compile chan_dongle
- Fix any compilation errors

### Phase 3: Runtime Testing
- Load module
- Test dongle detection
- Test SMS send/receive
- Test voice calls

### Phase 4: Documentation
- Update README with Asterisk 23 support
- Document any breaking changes
- Update build instructions

## Key Files to Modify

1. **ast_compat.h** - Add Asterisk 18-23 compatibility shims
2. **channel.c** - Update format/codec handling
3. **chan_dongle.c** - Update module registration if needed
4. **configure.ac** - Update version detection/validation

## Notes

- Maintain backward compatibility with Asterisk 13-17
- Use version conditionals (#if ASTERISK_VERSION_NUM >= XXXXXX)
- Test on multiple Asterisk versions (17, 20, 23)
- SIM7600G-H tested and working on Asterisk 17

## Build Instructions for Testing

```bash
# For Asterisk 23
./bootstrap
./configure --with-astversion=23.0.0
make
make install
```

## Current Working Configuration

- **Tested**: Asterisk 17.9.3
- **Hardware**: SIM7600G-H USB dongle
- **Status**: SMS send/receive working perfectly
- **Platform**: Unraid/Docker

