
1. Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?
В результате выявления подозрительной активности на веб-сервере был проведён анализ сетевого трафика с использованием PCAP-файла. Анализ показал серию запросов, направленных на различные порты сервера, что свидетельствует о возможной попытке сканирования.
Для определения источника данной активности была выполнена  группировка и сортировка запросов по IP-адресам отправителей для выявления наиболее активного источника.
После чего был выявлен IP-адрес, с которого поступило наибольшее количество запросов на разные порты сервера, что является характерным признаком сканирования.

В результате анализа источником подозрительной активности на сервер был идентифицирован IP-адрес: 14.0.0.120.
![[Pasted image 20250715165242.png]]

2. Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?
В ходе расследования инцидента информационной безопасности был идентифицирован IP-адрес, с которого осуществлялась активность, указывающая на возможное сканирование портов и попытку разведки серверной инфраструктуры. Адрес источника: 14.0.0.120..

Методика определения:
Для установления местоположения источника использован открытый сервис IP-геолокации:
https://en.ipshu.com/ipv4/14.0.0.120.
Источник сканирования портов и подозрительной сетевой активности географически расположен в стране China.
![[Pasted image 20250715165129.png]]
3. From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?
В ходе анализа был применён фильтр:
ip.src == 14.0.0.120 && http
Данный фильтр позволяет отобразить только HTTP-пакеты, отправленные с IP-адреса злоумышленника на сервер, что помогает выявить попытки доступа к веб-интерфейсам.
Было установлено, что HTTP-запрос к административной панели был направлен на порт 8080
![[Pasted image 20250715170912.png]]
4. Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?
В HTTP-запросах, направленных на сервер, был обнаружен заголовок User-Agent: gobuster/3.x. Предположительно, что злоумышленник использовал инструмент Gobuster для перечисления директорий и файлов на веб-сервере. 

![[Pasted image 20250715172959.png]]
5. After the effort to enumerate directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which specific directory related to the admin panel did the attacker uncover?
Применён фильтр в Wireshark для изоляции успешных HTTP-запросов http && http.response.code == 200
Среди успешных HTTP-запросов был выявлен доступ к  директории /manager.
![[Pasted image 20250721135633.png]]
6. After accessing the admin panel, the attacker tried to brute-force the login credentials. Can you determine the correct username and password that the attacker successfully used for login?
В Wireshark применён фильтр:
http.authbasic
Это позволяет отобразить все HTTP-запросы, содержащие заголовок авторизации Basic Authentication.
Злоумышленник успешно провёл атаку перебора учетных данных и получил доступ к административной панели Apache Tomcat, расположенной по адресу /manager/html, используя следующие учетные данные:
Имя пользователя: admin
Пароль: tomcat
![[Pasted image 20250715174622.png]]
7. Once inside the admin panel, the attacker attempted to upload a file with the intent of establishing a reverse shell. Can you identify the name of this malicious file from the captured data?
В Wireshark применён фильтр для отображения всех HTTP POST-запросов от IP-адреса атакующего:
ip.src == 14.0.0.120 && http.request.method == POST
В результате анализа установлено, что злоумышленник загрузил на сервер вредоносный файл с именем JXQOZY.war. 
![[Pasted image 20250715175156.png]]
8. After successfully establishing a reverse shell on our server, the attacker aimed to ensure persistence on the compromised machine. From the analysis, can you determine the specific command they are scheduled to run to maintain their presence?
Для одного из соединений был выполнен анализ с помощью функции Follow TCP Stream, что позволило восстановить весь диалог между клиентом (сервером) и атакующим, включая отправленные команды и ответы.
![[Pasted image 20250715180029.png]]