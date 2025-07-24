1. What’s the Operating System version of the Server? (two words)
Анализ дампа памяти с использованием утилиты Volatility 3 и плагина windows.info.SysInfo позволил определить версию операционной системы целевого хоста. В поле CurrentVersion была указана версия 6.3, что соответствует версии Windows Server 2012 R2, согласно документации Microsoft и справочному руководству по версиям Windows.
windows server 2012 r2
2. What’s the Operating System of the Desktop? (four words separated by spaces)
![[Pasted image 20250717114900.png]]
3. What was the IP address assigned to the domain controller?
![[Pasted image 20250717103406.png]]
4. What was the timezone of the Server?
![[Pasted image 20250717104526.png]]
5. What was the initial entry vector (how did they get in)?. Provide protocol name.
![[Pasted image 20250717114538.png]]
6. What was the malicious process used by the malware? (one word
![[Pasted image 20250717123449.png]]
7. Which process did malware migrate to after the initial compromise? (one word)
![[Pasted image 20250717140244.png]]
8. Identify the IP Address that delivered the payload.
![[Pasted image 20250717150146.png]]
![[Pasted image 20250717150020.png]]
9. What IP Address was the malware calling to?
10. Where did the malware reside on the disk?
![[Pasted image 20250717154515.png]]
11. What's the name of the attack tool you think this malware belongs to? (one word)
Metasploit
12.  One of the involved malicious IP's is based in Thailand. What was the IP
![[Pasted image 20250717161647.png]]
13. Another malicious IP once resolved to klient-293.xyz . What is this IP?
![[Pasted image 20250717162015.png]]
14. The attacker performed some lateral movements and accessed another system in the environment via RDP. What is the hostname of that system?
Desktop-SDN1RPT
15. Other than the administrator, which user has logged into the Desktop machine? (two words)
Rick Sanchez
16. What was the password for "jerrysmith" account?
![[Pasted image 20250717200826.png]]
17.  What was the original filename for Beth’s secrets?
Secret_Beth.txt
18.  What was the content of Beth’s secret file? ( six words, spaces in between)
Earth Beth is the real Beth
19.  The malware tried to obtain persistence in a similar way to how Carbanak malware obtains persistence. What is the corresponding MITRE technique ID?
![[Pasted image 20250718160401.png]]




http.response.code == 200 && tcp.payload contains ".exe"

![[Pasted image 20250717152817.png]]





![[Pasted image 20250718153533.png]]

