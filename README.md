## Тестовые задания для DevOps Engineer

1. На Linux-сервере с 100GB диском закончилось место, но команда du показывает что все файлы вместе занимают только 70GB. В чём может быть проблема?
  
  Может быть что удален какой-то большой файл, который до сих пор открыт приложением, такое бывает когда удаляешь лог-файл и не рестартуешь процесс, который продолжает
  записывать в удаленный файл.
  Просмотр списка таких файлов:
  lsof | grep '(deleted)'  
  Решение - перезагрузить процесс , который занял этот удаленный файл.

2. Веб-сервис возвращает ответ если обращаться к нему с помощью curl http://site, но не отвечает если проверять работоспособность с помощью ping, чем это может быть вызвано?
   Если ответ возвращаентся по http, то значит на уровне сети все работает, скорее всего ping не работает изза правил фаерволов на пути следования пакетов.

3. Как определить открытые порты на удаленном сервере?
    - Если есть доступ по ssh,  то  
	  для linux: netstat -lpn | more 
	           либо lsof -i
	  для windows (powershell): netstat -an | select-string LISTENING		   
			   
    - С помощью сканера портов , например
   nmap -v -A remote_host

4. Какой самый быстрый способ посчитать примерное число файлов в директории?
   для Linux: ls -l | wc -l . Рекурсивно включая вложенные папки: ls -R | wc -l
   для windows: в свойствах папки
   
5. Каким образом можно отслеживать что приложение работающее на сервере имеет проблемы с исходящими tcp-соединениями.
   - мониторить количество сессии c ip source сервера и в состоянии ESTABLISHED 

6. Какие виды кэширования есть в PostgreSQL? Опишите их отличие.

7. Чем отличается "доверенный" SSL сертификат от "самоподписного".
   - доверенный сертификат подписан доверенным центром сертификации (CA). в системах уже предустановлены и постоянно обновляются корневые CA. 
   - самоподписанный , это сертификат на основе сгенерированного на сервере либо другом устройстве CA. Сертификаты такого CA могут быть тоже доверенными , если
   добавить в систему сертификат CA этого сервера.
    Если установить такой самоподписанный сертификат на свой сервер, например web-сервер, то при подключении из интернет будет предепреждение о небезопасноcти соединения.

8. Есть лог-файл в котором на каждое посещение пользователя записывается три колонки: ip,"uri",response_time. Пример строчки из файла: "1.2.3.4,/shop/checkout,0.650". 
  Напишите две shell команды: а) команда получает этот лог файл в stdin, а на выходе отображает top-15 ip-адресов, с которых были запрошены страницы, 
  содержащие в адресе слово 'checkout'. б) команда находит 90-й перцентиль по времени ответа.
   1)  cat <input.log | grep checkout  | awk -F "," '{print $1}' | sed 's/"//' | head -n 15
   2)  cat <input.log | grep checkout  | awk -F "," '{print $3}' | sed 's/"//' | sort | awk '{all[NR] = $0} END{print all[int(NR*0.9 - 0.5)]}' 

9. Представьте себе сервис работающий на однотипных виртуальных машинах в облаке. В нем есть API с помощью которого можно удалить или добавить одну уже 
настроенную виртуальную машину. Для экономии средств стоит задача автоматически менять число запущенных виртуальных машин, например чтобы ночью,
 когда пользователи спят, было запущенно существенно меньше серверов, чем днём, когда нагрузка возрастает. Допустим, раз в 5 минут запускается ваш скрипт
 которому надо решить - добавить виртуальную машину, удалить, или ничего не делать. На основании каких метрик вы бы принимали это решение?

  В зависимости от типа приложения, возможно по количеству сессии и нагрузке на ЦПУ, расходу памяти и времени отклика.
