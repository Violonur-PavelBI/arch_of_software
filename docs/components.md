# Компонентная архитектура
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(moderator, "Модератор")
Person(user, "Пользователь")

System_Ext(web_site, "Клиентский веб-сайт", "HTML, CSS, JavaScript, React", "Веб-интерфейс")

System_Boundary(conference_site, "Сайт сервиса конференций") {
   Container(user_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(report_service, "Сервис докладов", "C++", "Сервис управления докладами", $tags = "microService") 
   Container(conference_service, "Сервис конференций", "C++", "Сервис управления конференциями", $tags = "microService")
   ContainerDb(db, "База данных", "MySQL", "Хранение данных о пользователях, докладах и конференциях", $tags = "storage")
   
}

Rel(admin, web_site, "Просмотр, добавление и редактирование информации о пользователях, докладах и конференциях")
Rel(moderator, web_site, "Модерация контента и пользователей")
Rel(user, web_site, "Регистрация, просмотр информации о конференциях и докладах и запись на них")

Rel(web_site, user_service, "Работа с пользователями", "localhost/person")
Rel(user_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, report_service, "Работа с докладами", "localhost/report")
Rel(report_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, conference_service, "Работа с конференциями", "localhost/conf")
Rel(conference_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов  

### Сервис авторизации
**API**:
-	Создание нового пользователя
      - входные параметры: login, пароль, имя, фамилия, email, обращение (г-н/г-жа)
      - выходные параметры: отсутствуют
-	Авторизация пользователя
     - входные параметры:  login, пароль
     - выходные параметры: токен (JWT)
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: имя, фамилия, email, обращение (г-н/г-жа)
-	Поиск пользователя по маске имени и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, имя, фамилия, email, обращение (г-н/г-жа)

### Сервис конференций
**API**:
- Создание конференции
  - Входные параметры: название конференции, категория (всероссийская, международная и т.д.), краткое описание, организатор и дата проведения
  - Выходыне параметры: идентификатор конференции
- Добавление доклада в конференцию
  - Входные параметры: идентификатор доклада, название доклада, автор, конференция, аннотация, содержание доклада, дата создания
  - Выходные параметры: идентификатор доклада в сервисе конференций
- Получение списка докладов в конференции
  - Входные параметры: доклад
  - Выходные параметры: массив с докладами (идентификатор, название доклада, автор, конференция, аннотация, содержание доклада, дата создания)

### Сервис доклада
**API**:
- Создание доклада
  - Входные параметры: название доклада, автор, аннотация, содержание доклада, дата создания
  - Выходные параметры: идентификатор доклада
- Получение доклада
  - Входные параметры: идентификатор доклада
  - Выходные парамтеры: заголовок доклада, автор, аннотация, содержание доклада, дата создания
- Получение списка всех докладов
  - Входные параметры: отсутствуют
  - Выходные параметры: массив из докладов, где для каждого указаны его идентификатор, название, автор, аннотация, содержание и дата создания