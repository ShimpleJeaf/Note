# nmap

* 端口扫描时默认扫描最常用的1000个tcp端口

* 扫描时回车会显示扫描进度

## 主机发现

| 参数         | 说明                                                              |
| ------------ | ----------------------------------------------------------------- |
| -sn          | ping扫描                                                          |
| -Pn          | 强制认为主机在线                                                  |
| -PE/PP/PM    | ICMP echo, timestamp, and network request discovery probs         |
| -PR          | ARP扫描                                                           |
| -PS/PA/PU/PY | TCP SYN Ping，TCP ACK Ping，UDP Ping，SCTP，均可加端口-PS80,443。 |
| -v           | 显示详细信息                                                      |
| -vv          | 显示更详细的信息                                                  |
| -n/-R        | 不进行/总是进行DNS解析                                            |

* 主机发现

  ```bash
  # 如果虚拟机使用nat模式，-sn可能出现所有整个网段host均为up的误报，
  # 要切换成桥接模式，如果桥接后无法自动获取到ipv4，则手动设置ipv4地址。
  sudo nmap -sn -n 192.168.1.0/24
  ```

## 扫描

扫描端口时经常也需要加-Pn，不使用ping探测

| 参数            | 说明                                         |
| --------------- | -------------------------------------------- |
| -p              | 端口，默认tcp。混合扫描：U:53,111,T:21-25,80 |
| -sS/sT/sA/sW/sM | TCP SYN/Connect()/ACK/Window/Maimon scans    |
| -sN/sF/sX       | TCP Null, FIN, and Xmas scans                |
| -sU             | 扫描udp端口                                  |
| -sV             | 扫描端口的服务和版本信息                     |

```bash
# 先粗略扫描，可以先不用-p-扫描常用端口，如果没有有用的，再加上扫描全端口
sudo nmap -sS -Pn -p- 192.168.1.1
# 指定端口扫描
sudo nmap -sV -A -Pn -p 80 192.168.1.1
```

## 工具

| 参数 | 说明                                    |
| ---- | --------------------------------------- |
| -O   | 启用os探测                              |
| -A   | 启动OS、version、script、traceroute检测 |

## 漏洞探测

* 更新脚本数据库

  ```bash
  sudo nmap --script-updatedb
  ```

* 使用漏洞探测脚本

  ```bash
  # 使用vuln类别的脚本，-p-表示所有端口
  # 另外还有个vulners
  sudo nmap -p- -sV --script vuln <目标IP>
  # 指定特定的脚本
  sudo nmap -p 445 --script smb-vuln-ms10-061 <目标IP>
  ```

* 使用vulscan扩展

  ```bash
  git clone --depth 1 https://github.com/scipag/vulscan/ /usr/share/nmap/scripts/vulscan
  sudo nmap -sV --script=vulscan/vulscan.nse <目标IP>
  ```

# Metasploit Framework

* 搜索cve

  ```bash
  search type:exploit port:80 platform:windows
  ```

* 检查

  ```bash
  use exploit/multi/http/freescout_htaccess_rce
  set RHOSTS 192.168.1.8
  check
  ```

# aircrack-ng

1. 停止干扰服务

   ```bash
   sudo airmon-ng check kill
   ```

2. 开启网卡监听模式

   ```bash
   sudo airmon-ng start wlan0
   ```

3. 粗略扫描

   ```bash
   # 扫描信号大于-80db的
   sudo airodump-ng --min-power -80 wlan0
   ```

4. 选定对象后开始抓包

   ```bash
   # 必须要限定信道，不然aireply-ng会有问题
   sudo airodump-ng -w output --essid 3-6 -c 11 wlan0
   ```

   等待一段时间，等到下面的Station栏有显示了，就说明有用户连接了这个wifi，就可以进行攻击了，攻击需要bssid和station mac地址，都在Station栏有显示

   攻击时4中的命令要持续进行抓包，不要关闭

5. 重连攻击

   ```bash
   # -0表示攻击模式，后一个0攻击次数，0表示不间断攻击，也可以设置成其他数字
   sudo aireplay-ng -0 0 -a bssid地址 -c staticMac地址 wlan0
   ```

   攻击时持续查看4中的命令输出，只到最上方出现handshake包捕获成功，就可以进行下一步了。注意：如果攻击时aireplay-ng的输出后面一直是[0/64]，也就是没有回包，说明没有成功，可能是有防护措施。

   攻击完成后，Ctrl-C停止airodump-ng和aireplay-ng。

6. 破解密码

   密码本：[conwnet/wpa-dictionary: WPA/WPA2 密码字典，用于 wifi 密码暴力破解](https://github.com/conwnet/wpa-dictionary)

   ```bash
   aircrack-ng mima.txt output.cap
   ```

   如果字典中有密码，将会在匹配到时显示出来。至此破解完成。

   * 另外也可使用GPU进行跑字典。

   * 如果字典没有，也可使用电话号码进行跑字典。