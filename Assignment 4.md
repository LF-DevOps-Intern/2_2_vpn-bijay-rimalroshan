# Assignment 4

Update the repository

```bash
yum update && yum upgrade
```

### enable ip forwarding in /proc and in sysctl  conf file

```
 echo '1' > /proc/sys/net/ipv4/ip_forward
```

For making the changes permanent 

Open the config file /etc/sysctl.conf

then insert the following line

```bash
net.ipv4.ip_forward = 1
```

To enable the changes made in sysctl.conf run  to load the new values from the file

```bash
sysctl -p
```

### Assign static ip address to the systems:

In the system in the internal network

```bash
sudo ip addr 192.168.4.2/24 dev enp0s3
```

In the gateway system

```bash
sudo ip addr 192.168.4.3/24 dev enp0s8
```

### Install openvpn server and easyrsa

```bash
sudo yum update

```

Install easyrsa

```bash
wget https://github.com/OpenVPN/easy-rsa/archive/v3.0.8.tar.gz
tar -xf v3.0.8.tar.gz
mkdir /etc/openvpn/easy-rsa
mv easy-rsa-3.0.8 /etc/openvpn/easy-rsa
```

### Edit the sample server.conf file

```bash
cp /usr/share/doc/openvpn-2.4.9/sample/sample-config-files/server.conf.gz /etc/openvpn
gunzip -c server.conf.gz > server.conf

```

![Untitled](Assignment%204%20fcb51e04df6b43a5a6880717e673dd54/Untitled.png)

Uncomment or Modify or Add the Following lines where ever necessary

```bash
dh dh2048.pem
push "redirect gateway def1 bypass-dhcp"
```

### Generate CA, server certificate and client certificate

Configure the vars file with appropriate values to be supplied during CSR.

![Untitled](Assignment%204%20fcb51e04df6b43a5a6880717e673dd54/Untitled%201.png)

source the vars file using the following command

```bash
source vars
```

Build the CA next using the build CA script

```bash
./build-ca 
```

Modify the values for the CSR if needed.

Generate the keys for the server using

```bash
./build-key-server server
```

Similarly generate DH keys:

```bash
./build-dh 2048
```

Generate the certificate for the client using

```bash
./build-key client
```

Create a folder named client and move the required files necessary for the client to this folder

```bash
cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf ~/client/client.ovpn
cp keys/client* ~/client/
```

Use scp to securely transfer the file to the client computer

```bash
scp -i key.pem -r client tom@192.168.0.12
```

In the Client Config file make the necessary adjustments:

```bash
remote 192.168.4.12 1192
user nobody
group nobody
```

Paste the content of the ca.crt, client.crt and client.key file inside the client.ovpn file.

```bash
echo "<ca>$(cat ca.crt )</ca>" >> client.ovpn
echo "<cert>$(cat client.crt )</client>" >> client.ovpn
echo "<key>$(cat client.key )</key>" >> client.ovpn
```

Check the status of the openvpn 

![Untitled](Assignment%204%20fcb51e04df6b43a5a6880717e673dd54/Untitled%202.png)

# IDS Setup:

Install epel repository on centos using

```bash
yum install epel-release
```

Install suricate using yum

```bash
yum install suricate
```

Install and update suricate threat rules using

```bash
suricata-update
```

Update the HOME_NET and EXTERNAL_NET with appropriate Network IDs. EXTERNAL_NET are the address that you want suricate to monitor for threat.

![Untitled](Assignment%204%20fcb51e04df6b43a5a6880717e673dd54/Untitled%203.png)

Enable it to run at system startup using

```bash
systemctl enable suricate.service
```
