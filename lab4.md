1. Identifying the name of the malicious process helps in understanding the nature of the attack. What is the name of the malicious process?
С помощью инструмента Volatility 3 и плагина malfind, был выполнен анализ дампа памяти с целью выявления аномалий, характерных для вредоносной активности:
python vol.py -f <memory_dump_path> windows.malfind
Результатом анализа стало выявление подозрительного процесса PowerShell с PID 3692. Наличие процесса PowerShell в дампе памяти может быть нормальным, однако в данном случае его запуск выглядит нетипично — он отсутствует в ожидаемом списке активных процессов, что указывает на возможную скрытую активность или запуск с обходом стандартных механизмов.
Идентифицированный процесс powershell.exe с PID 3692 является вредоносным и, вероятно, использовался для исполнения атакующего кода в памяти. Это указывает на эксплуатацию системы через безфайловый (fileless) подход, при котором вредоносный код исполняется напрямую в оперативной памяти без сохранения на диск.
![[Pasted image 20250721103215.png]]
![[Pasted image 20250721103253.png]]
2. Knowing the parent process ID (PPID) of the malicious process aids in tracing the process hierarchy and understanding the attack flow. What is the parent PID of the malicious process?
Для анализа и визуализации иерархии процессов использовался плагин `windows.pstree` из Volatility 3:
![[Pasted image 20250721103337.png]]
Родительский PID (4210) для вредоносного PowerShell-процесса (PID 3692) предоставляет важную информацию об этапах атаки. Несмотря на то, что родительский процесс завершён, его наличие фиксируется в дереве процессов, что даёт возможность частично восстановить цепочку заражения.
Обнаруженная командная строка указывает на удалённую загрузку и запуск DLL — один из типичных признаков fileless-атаки с применением Living off the Land (LotL) техник.
Родительский PID (4210) для вредоносного PowerShell-процесса (PID 3692) предоставляет важную информацию об этапах атаки. Несмотря на то, что родительский процесс завершён, его наличие фиксируется в дереве процессов, что даёт возможность частично восстановить цепочку заражения.
Обнаруженная командная строка указывает на удалённую загрузку и запуск DLL — один из типичных признаков fileless-атаки с применением Living off the Land (LotL) техник.
![[Pasted image 20250721103400.png]]
3. Determining the file name used by the malware for executing the second-stage payload is crucial for identifying subsequent malicious activities. What is the file name that the malware uses to execute the second-stage payload?
Имя файла было выявлено в параметрах командной строки подозрительного процесса powershell.exe (PID 3692), полученных с помощью плагина windows.cmdline в ходе анализа дампа памяти.
Файл 3435.dll загружается с удалённого сервера и исполняется с помощью встроенной системной утилиты rundll32.exe. Использование rundll32 для запуска удалённых DLL-файлов является распространённым приёмом атак, направленным на обход защитных механизмов и выполнение кода в памяти (fileless execution).
![[Pasted image 20250721105140.png]]
![[Pasted image 20250721105212.png]]
4. Identifying the shared directory on the remote server helps trace the resources targeted by the attacker. What is the name of the shared directory being accessed on the remote server?
Имя общей директории на удалённом сервере — davwwwroot.
5. What is the MITRE ATT&CK sub-technique ID that describes the execution of a second-stage payload using a Windows utility to run the malicious file?
Данная под-техника описывает злоупотребление доверенными системными утилитами Windows, такими как rundll32.exe, для выполнения вредоносных DLL-файлов. Использование rundll32 позволяет атакующим запускать вредоносный код, маскируясь под легитимную активность, что помогает обойти антивирусное и поведенческое обнаружение.
![[Pasted image 20250721111620.png]]
6. Identifying the username under which the malicious process runs helps in assessing the compromised account and its potential impact. What is the username that the malicious process runs under?
Для определения имени пользователя и связанных с ним привилегий был использован плагин windows.getsids.GetSIDs из Volatility 3
Пользователь Elon, под чьей учетной записью выполнялся вредоносный процесс, обладает административными правами на локальной системе и принадлежит к доменным пользователям, что существенно расширяет возможный вектор атаки.
Такой уровень доступа может позволить злоумышленнику выполнять критически важные действия на системе.
![[Pasted image 20250721111853.png]]7. Knowing the name of the malware family is essential for correlating the attack with known threats and developing appropriate defenses. What is the name of the malware family?
![[Pasted image 20250721112039.png]]

Семейство вредоносного ПО, связанное с данной атакой, идентифицировано как StrelaStealer. StrelaStealer — это вредоносное ПО, ориентированное на кражу учетных данных электронной почты.
Для установления возможной принадлежности к известному семейству вредоносного ПО, данный IP-адрес был отправлен на проверку в платформу Threat Intelligence (VirusTotal). Результаты анализа показали, что IP-адрес 45.9.74.32 отмечен как вредоносный 14 из 94 поставщиками решений безопасности.
На основе анализа поведения указанных файлов и перекрестной проверки с существующими угрозами, было установлено, что они связаны с семейством StrelaStealer.