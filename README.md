# AddFree
a app that lets you block wifi from spesific apps on your phone if you want to get rid of those stupid adds on offline games and if you just want to be offline but online on snapchat at the same time. this project is not fully done some core stuff has been added but dosent really work the way i want it to work, but i just dont have the knowlagde right now. i would use this as an app if it did exist or existed the way i want it to, but i need to learn kotlin before it all can work

## Everything i did:
Step by step (clean version)
1. Windows setup

Added C:\Program Files (x86)\platform-tools to PATH
Paired phone wirelessly: adb pair <ip>:<port> → adb connect 10.2.0.105:5555
Mirror phone: scrcpy -e

2. Raspberry Pi — Tailscale
bashcurl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
sudo tailscale up --advertise-exit-node
Then approve exit node at tailscale.com/admin
3. Raspberry Pi — dnsmasq
bashsudo apt install dnsmasq -y
sudo nano /etc/dnsmasq.conf
Add at top:
listen-address=0.0.0.0
bind-interfaces
no-resolv
server=1.1.1.1
server=8.8.8.8
addn-hosts=/etc/dnsmasq.blocklist
bashsudo touch /etc/dnsmasq.blocklist
sudo systemctl restart dnsmasq
4. Download social media blocklist
bashcurl https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/social/hosts -o /tmp/social.hosts
grep "^0.0.0.0" /tmp/social.hosts | sudo tee /etc/dnsmasq.blocklist > /dev/null
sudo systemctl restart dnsmasq
5. Flask app
bashmkdir ~/appblocker && cd ~/appblocker
python3 -m venv venv
source venv/bin/activate
pip install flask
# create app.py and templates/index.html
Run with: sudo venv/bin/python app.py
Access at: http://100.121.241.123:5000
6. Phone setup

Install Tailscale app → log in → set exit node to Pi
Tailscale DNS → Use Tailscale DNS on → resolver 100.121.241.123
In Tailscale admin → DNS → add nameserver 100.121.241.123 → enable Override local DNS

7. Alias shortcut
bashecho "alias LockMeUp='cd ~/appblocker && source venv/bin/activate && sudo venv/bin/python app.py'" >> ~/.bashrc
source ~/.bashrc

Current status

DNS blocking works ✅
Exit node breaks general internet ❌ (iptables issue we haven't solved yet)
Snapchat app bypasses DNS ❌ (uses hardcoded IPs)


## short story
connected my phone to pc
connected my phone to a vpn
blocked dns on the vpn that runs throguth my pi
pi holds python to block and store the blocked apps
websites that are blocked run into dns 0.0.0.0 so it just times out

## what needs to be better
needs to accsualy work, this bans websites not apps
when the vpn is on and everything works there is a noticable delay on other apps
i want this to be a like a small app that can allways be on in the background without being noticable
if this is to be possible everything needs to happen on the phone both vpn, the app, and the blocking
