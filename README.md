
<img width="2560" height="589" alt="Image" src="https://github.com/user-attachments/assets/8ff5cd4a-b4da-4031-80ca-97b5a8a79fdb" />

# ZLT-X28 OpenWrt LuCI Fix

## Project Overview
This project enables full OpenWrt functionality on ZLT-X28 routers while maintaining the original vendor firmware. Unlike standard OpenWrt installations, **your device continues running the manufacturer's firmware and web UI** (accessible on ports 80/443) while simultaneously providing access to OpenWrt's LuCI interface on port 4153. This approach preserves the stable vendor foundation while unlocking advanced OpenWrt features.

## Technical Background
- **Default State**: ZLT-X28 routers ship with vendor firmware containing a limited web UI (ports 80/443)
- **Unofficial OpenWrt**: These devices can run OpenWrt *alongside* vendor firmware, but the LuCI interface is broken in existing builds
- **The Fix**: This solution repairs LuCI without modifying vendor components, enabling dual access:
  - Vendor UI: Port 80/443 (original functionality)
  - OpenWrt LuCI: Port 4153 (advanced features)

# Vendor Webui
![Image](https://github.com/user-attachments/assets/30d24f56-7427-4be5-a1ff-5166e21217ee)

# OpenWrt LuCI Webui
![LuCI Interface Working](https://github.com/user-attachments/assets/976432d9-e9b8-4433-8ad7-e8e724d81912)


## Key Features Enabled
- OpenVPN server/client configuration
- Advanced firewall and network controls
- QoS and bandwidth monitoring
- Etc

## Requirements
1. Telnet access to router (typically enabled via command injection exploit)
2. Root shell access (achieved through initial exploit)
3. Internet connection on router

## Complete Installation Guide

### Step 1: Download Files Directly to Router
Run these commands in router shell:
### uhttpd
```bash
wget https://raw.githubusercontent.com/EngineerMazid/ZLT-X28/main/uhttpd -O /usr/sbin/uhttpd
```
### luci_fixed.tgz 
```
wget https://raw.githubusercontent.com/EngineerMazid/ZLT-X28/main/luci_fixed.tgz -O /tmp/luci_fixed.tgz
chmod +x /usr/sbin/uhttpd
```
### Step 2: Install Fixed LuCI Components
```bash
tar xzf /tmp/luci_fixed.tgz -C /tmp
cp -r /tmp/www/luci-static /www/
cp -r /tmp/usr/lib/lua/luci /usr/lib/lua/
cp -r /tmp/usr/share/luci /usr/share/
rm /tmp/luci_fixed.tgz
```

### Step 3: Launch LuCI Interface
```bash
uhttpd -p 0.0.0.0:4153 -h /www &
```
Access OpenWrt at: http://[ROUTER_IP]:4153

### Step 4: Set a root password
You'll need this to log in to LuCl
```bash
passwd root
```

### Step 5: Fix UI OpenWrt Language and Style Error
```bash
echo "config internal themes" >> /etc/config/luci
echo "    option Bootstrap '/luci-static/bootstrap'" >> /etc/config/luci
uci commit luci
killall uhttpd
uhttpd -p 0.0.0.0:4153 -h /www &
```

## Persistence Across Reboots
Add these lines to your startup script:
```bash
# /etc/rc.local
uhttpd -p 0.0.0.0:4153 -h /www &
exit 0
```
### ⚠️ Critical Warnings
One wrong step can fry your ZLT-X28 router for good. Using the wrong firmware or architecture will break both the LuCI WebUI and the vendor’s original interface. When that happens, your device is totally bricked with no easy way to recover.

The stock vendor firmware works fine for most folks, but this OpenWrt version gives you full access, and full risk. It’s based on an old, unofficial OpenWrt LuCI with known bugs. Because of architecture differences, official OpenWrt packages won’t install or run properly.

If you mess up and brick your router, that’s on you, no backups, no help, no do-overs. The only way to fix it is flashing the entire firmware from a working ZLT-X28. So yeah, you’re basically on your own here.


## Maintained with ✨️ by [EngineerMazid](https://github.com/EngineerMazid)
*Not affiliated with ZLT or OpenWrt project*

## License
This project is licensed under the [MIT License](https://github.com/EngineerMazid/ZLT-X28/blob/main/LICENSE).
