# Setting Up Your Fork for Asterisk 23 Support

## Step 1: Fork on GitHub

1. Go to https://github.com/wdoekes/asterisk-chan-dongle
2. Click "Fork" button
3. Select your account: **navanchauhan**
4. Wait for fork to complete

## Step 2: Update Your Local Clone

```bash
cd /Users/navan.chauhan/Developer/navanchauhan/experiments/freepbx-gsm-gateway/chan-dongle-ast23

# Add your fork as remote
git remote add fork git@github.com:navanchauhan/asterisk-chan-dongle.git

# Push the asterisk-23-support branch
git push fork asterisk-23-support
```

## Step 3: Test Current Code with Asterisk 23

The existing code likely already works with Asterisk 23 since it supports 13+. To verify:

### Option A: Test with FreePBX/Asterisk 23 Container

Update your Dockerfile to use Asterisk 23:

```dockerfile
FROM debian:bookworm

# Install Asterisk 23
RUN apt-get update && apt-get install -y \
    asterisk \
    asterisk-dev \
    && asterisk -V  # Should show 23.x

# Build chan_dongle
COPY . /usr/src/chan_dongle
WORKDIR /usr/src/chan_dongle
RUN ./bootstrap && \
    ./configure --with-astversion=23.0.0 && \
    make && \
    make install
```

### Option B: Quick Build Test

```bash
# Create test build script
cat > test_build_ast23.sh << 'EOF'
#!/bin/bash
docker run --rm -v $(pwd):/src debian:bookworm bash -c "
apt-get update && apt-get install -y \
    build-essential \
    autoconf \
    automake \
    libtool \
    libsqlite3-dev \
    wget && \
wget -O /tmp/asterisk.tar.gz \
    https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-23-current.tar.gz && \
tar -xzf /tmp/asterisk.tar.gz -C /tmp && \
cd /tmp/asterisk-* && \
./configure --prefix=/usr && \
make install-headers && \
cd /src && \
./bootstrap && \
./configure --with-astversion=23.0.0 && \
make
"
EOF

chmod +x test_build_ast23.sh
./test_build_ast23.sh
```

## Step 4: If Build Fails - Make Fixes

If the build fails with Asterisk 23, common fixes:

### Fix 1: Format API Updates

In `channel.c`, add Asterisk 18-23 specific code:

```c
#if ASTERISK_VERSION_NUM >= 180000 /* 18+ */
// Asterisk 18-23 might need slight adjustments to format handling
// Check compilation errors and add version-specific code here
#elif ASTERISK_VERSION_NUM >= 130000 /* 13-17 */
// Existing code
#endif
```

### Fix 2: Channel Allocation

If `ast_channel_alloc` signature changed:

```c
#if ASTERISK_VERSION_NUM >= 200000 /* 20+ */
// Updated channel allocation for Asterisk 20-23
#elif ASTERISK_VERSION_NUM >= 120000 /* 12-19 */
// Existing code
#endif
```

## Step 5: Update Dockerfile

Once working, update `freepbx-gsm-gateway/Dockerfile`:

```dockerfile
FROM tiredofit/freepbx:latest-ast23  # If available

# Or build Asterisk 23 from source
# Then clone YOUR fork instead:

RUN wget --no-check-certificate -O chan_dongle.tar.gz \
    https://github.com/navanchauhan/asterisk-chan-dongle/archive/refs/heads/asterisk-23-support.tar.gz && \
    tar -xzf chan_dongle.tar.gz && \
    cd asterisk-chan-dongle-asterisk-23-support && \
    ./bootstrap && \
    ./configure --with-astversion=23.0.0 && \
    make && \
    make install
```

## Step 6: Test Everything

1. Build container with Asterisk 23
2. Load chan_dongle module
3. Test dongle detection
4. Test SMS send/receive
5. Test voice calls

## Current Status

✅ **Prepared:**
- Branch `asterisk-23-support` created
- Documentation added
- Ready to push to your fork

🔄 **Next Steps:**
1. Fork repo on GitHub
2. Push branch to fork
3. Test build with Asterisk 23
4. Fix any compilation issues
5. Test runtime functionality

## Quick Commands

```bash
# After forking on GitHub:
git remote add fork git@github.com:navanchauhan/asterisk-chan-dongle.git
git push fork asterisk-23-support

# Create PR to your own fork's main branch or keep as development branch
```

## Notes

- The existing Asterisk 13+ code is quite mature
- May work out-of-the-box with Asterisk 23
- Only test actual compilation will reveal issues
- Maintain backward compatibility with 17

## Resources

- [Asterisk 23 Release Notes](https://wiki.asterisk.org/wiki/display/AST/Asterisk+23+Release+Notes)
- [Asterisk API Changes](https://wiki.asterisk.org/wiki/display/AST/API+Changes)
- [Original chan_dongle](https://github.com/bg111/asterisk-chan-dongle)
- [wdoekes fork](https://github.com/wdoekes/asterisk-chan-dongle)
