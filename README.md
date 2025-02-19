# CoreAPI
**CoreAPI** - minecraft paper плагин предоставляющий основные функции для разработки и управления плагинами и модулями сервера. CoreAPI разработан исключительно для работы с продуктами компании araclecore.

Основные возможности API:

- Планировщик: Улучшенный способ работы с планировщиком задач от Bukkit: отоложенное, периодическое планирование и планирование с определенной итерацией.
- Валидация: Безопасная валидация из String в другие типы данных.
- Циклы: Система для управления повторяющимися задачами с возможностью: асинхронного выполнения, внедрения в рабочий цикл и исключения задач из рабочего цикла.
- Конфигурации: Удобная система управления конфигурационными YAML-файлами.
- Экземпляры: Централизованное управление экземплярами классов для удобного доступа из разных частей плагинов или модулей. Реализация Singleton паттерна проектирования.
- Интеграция с Redis: Опциональное подключение к Redis для расширенных возможностей хранения данных и межсерверного взаимодействия.
- Интеграция с MongoDB: Опциональное подключение к MongoDB базе данных.
## Планировщик

Используя планировщик CoreAPI вы облегчаете работу с обычным планировщиком от Bukkit, а также у вас появляется возможность создания итеративного планировщика.

**Создать задачу**

```java
public Task task(String name, JavaPlugin instance, boolean asynchronous, Runnable runnable) {
	return new Task(name, instance, asynchronous, runnable)
}
```

**Выполнить задачу**

```java
public void run(Task task) {
	task.run();
}
```

**Выполнить задачу с задержкой**

```java
public void run(Task task, long delay) {
	task.run(delay);
}
```

**Выполнить задачу с задержкой и периодом**

```java
public void run(Task task, long delay, long period) {
	task.run(delay, period);
}
```

**Выполнить задачу с задержкой, периодом и определенным количеством итераций**

```java
public void run(Task task, long delay, long period, int iterations) {
	task.run(delay, period, iterations);
}
```

**Остановить задачу**

```java
public void stop(Task task) {
	task.stop();
}
```

**Получить имя задачи**

```java
public String name(Task task) {
	return task.name();
}
```

**Проверить запущена ли задача**

```java
public boolean running(Task task) {
	return task.running();
}
```
## Валидация

Используя CoreAPI вы можете валидировать String значения в любой тип доступный в одном из методов класса Validation. Доступные типы для валидации: List, Boolean, Integer, Long, Double, Float.

```java
public void validate(String string) {
	Validation.List(string);
}
```
```java
public void validate(String string) {
	Validation.Boolean(string, false);
}
```
```java
public void validate(String string) {
	Validation.Integer(string, 0);
}
```
```java
public void validate(String string) {
	Validation.Long(string, 0L);
}
```
```java
public void validate(String string) {
	Validation.Double(string, 0.0);
}
```
```java
public void validate(String string) {
	Validation.Float(string, 0F);
}
```
## Циклы
#### Работа с главным циклом CoreAPI

**Получение главного цикла CoreAPI**

```java
Cycle cycle = Instances.get("core", Core.class).cycle();
```

Вы можете добавлять любую задачу в главный цикл CoreAPI. Однако, исключить вы сможете все задачи кроме "configuration", т.к. эта задача используется для работы API.

Вы не можете изменять конфигурацию, включать и выключать главный цикл CoreAPI. Перечисленные выше операции могут быть выполнены только под инстанцией главного класса плагина т.к. в нем содержится поле с ключем от цикла.
#### Создание нового цикла

Вы можете создавать 2 вида циклов: защищенные и не защищенные. У защищенных циклов предпологается наличие ключа для внедрения изменений или управления циклом.

**Создание нового не защищенного цикла**

```java
public static Cycle cycle(JavaPlugin instance) {
	return new Cycle(instance);
}
```

**Создание новго защищенного цикла**

```java
public static Cycle cycle(JavaPlugin instance, String key) {
	return new Cycle(instance, key);
}
```
#### Добавление задачи в цикл

**Добавление задачи в не защищенный цикл**

```java
public static void embed(Cycle cycle, String name, JavaPlugin instance, boolean asynchronous, Runnable runnable) {
	cycle.embed(name, instance, asynchronous, runnable);
}
```

**Добавление задачи в защищенный цикл**

```java
public static void embed(Cycle cycle, String name, JavaPlugin instance, boolean asynchronous, Runnable runnable, String key) {
	cycle.embed(name, instance, asynchronous, runnable, key);
}
```
#### Исключение задачи из цикла

**Исключение задачи из не защищенного цикла**

```java
public static void exclude(Cycle cycle, String name) {
	cycle.exclude(name);
}
```

**Исключение задачи из защищенного цикла**

```java
public static void exclude(Cycle cycle, String name, String key) {
	cycle.exclude(name, key);
}
```
#### Изменение параметров цикла

**Изменение параметров не защищенного цикла**

```java
public static void refactor(Cycle cycle, boolean asynchronous, long delay) {
	cycle.refactor(asynchronous, delay);
}
```

**Изменение параметров защищенного цикла**

```java
public static void refactor(Cycle cycle, boolean asynchronous, long delay, String key) {
	cycle.refactor(asynchronous, delay, key);
}
```
#### Запуск цикла

При запуске цикла без измзменения его конфигурации присуждаются стандартные параметры для его работы:
- asynchronous: false
- delay: 100
Рекомендуюется перед запуском цикла изменять его параметры методом refactor;

**Запуск не защищенного цикла**

```java
public static void start(Cycle cycle) {
	cycle.start();
}
```

**Запуск защищенного цикла**

```java
public static void start(Cycle cycle, String key) {
	cycle.start(key);
}
```
#### Остановка цикла

**Остановка не защищенного цикла**

```java
public static void stop(Cycle cycle) {
	cycle.stop();
}
```

**Остановка защищенного цикла**

```java
public static void stop(Cycle cycle, String key) {
	cycle.stop(key);
}
```
#### Получение данных о статусе цикла

В данных цикла вы получаете информацию о его конфигурации, количество и название активных задач

```java
public static void status(Cycle cycle, JavaPlugin instance) {
	instance.getLogger().info(cycle.toString());
}
```
## Конфигурации

С помощью CoreAPI вы можете легко работать с YAML-файлами конфигурации. Прежде чем начать работу с файлами конфигурации, создайте ресурс-фаил с ".yml" сигнатурой в папке ресурсов проекта (resources/configuration.yml).
#### Загрузка файла конфигурации

При загрузке файла конфигурации вы создаете среду для работы с этим файлом. По мимо этого, при загрузке конфигурации на сервере, если экземпляра файла нет в папке плагина, он автоматически создается из экзепляра созданного вами в ресурсах.

```java
public void configuration(JavaPlugin instance) {
	Configuration configuration = new Configuration(instance,"configuration.yml");
}
```
#### Перезагрузка файла конфигурации

Если вы внесли какое либо изменение в фаил конфигурации, вам нужно обновить фаил в памяти плагина. Для этого существует метод load.

```java
public void load(Configuration configuration) {
	configuration.load();
}
```
#### Проверка на наличие пути в файле конфигурации

```java
public boolean contains(Configuration configuration, String path) {
	return configuration.contains(path);
}
```
#### Получение данных из файла конфигурации

Получить данные вы можете указав путь к нежному значению и тип данных этого значения. Также, вы можете устанавливать стандартное значение, если вы не уверены что по указанному пути есть какое либо значение.

**Получение данных не безопастным способом**

```java
public <T> void get(Configuration configuration, String path, Class<T> type) {
	configuration.get(path, type);
}
```

**Получение данных безопастным путем**

```java
public <T> void get(Configuration configuration, String path, Class<T> type, T common) {
	configuration.get(path, type, common);
}
```
#### Запись данных в фаил конфигурации

При записи данных в фаил конфигурации вы указываете путь и значение. После запись автоматиески сохраняется в файле и его обновленная версия автоматически перезаписывает существующий фаил.

```java
public <T> void put(Configuration configuration, String path, Object value) {
	configuration.put(path, value);
}
```
#### Получение записей секции

Используя метод entries вы можете получить HashMap<String, Object> с данными секции по указанному вами пути

**Получение записей секции**

```java
public void entries(Configuration configuration, String path) {
	ConcurrentHashMap<String, Object> entries = configuration.entries(path);
}
```
## Экземпляры

В CoreAPI существует имплементация Singleton паттерна проектирования. Используя класс Instances вы можете добавлять, удалять и получать одиночные экземпляры классов и использовать их во всех проектах в зависимости которых есть CoreAPI.

#### Добавление экземпляра

```java
public static void add(String name, Object instance) {
	Instances.add(name, instance);
}
```
#### Получение экземпляра

```java
public static <T> void get(String name, Class<T> type) {
	Instances.get(name, type);
}
```
#### Удаление экземпляра

Вы можете удалить любой экземпляр кроме тех, что принимают участие в работе CoreAPI

```java
public static void remove(String name) {
	Instances.remove(name);
}
```
#### Проверка на наличие экземпляра

```java
public static boolean contains(String name) {
	return Instances.contains(name);
}
```
#### Получение списка экземпляров

```java
public static void list() {
	List<String> instances = Instances.list();
}
```
## Интеграция c Redis

Для включения интеграции с Redis, в конфигурационном файле **core/settings.yml** вы должны изменить следющие поля:

```yml
redis:  
  flag: false # Use redis instance  
  password: "" # Password for redis connection  
  address: "localhost" # Address for redis connection  
  port: "2000" # Port for redis connection
```

Так как фаил **core/settings.yml** имеет динамический статус и его данные обновляются с проходом главного цикла CoreAPI следует вводить данные в определенной последовательности, чтобы избежать не нужных ошибок в логах. Сначала поменяйте поля **password**, **address** и **port**. Затем, если вы ввели данные верно, значение **flag** измение на true. 

После этого, автоматически начнется процесс подключения к Redis. После успешного подключения в экземплярах появится экземпляр Redis с названием "redis".

**Установить значение**

```java
public void set(String key, String value) {
	Instances.get("redis", Redis.class).set(key, value);
}
```

**Установить временное значение**

```java
public void set(String key, String value, long seconds) {
	Instances.get("redis", Redis.class).set(key, value, seconds);
}
```

**Узнать время до исчезновения временной записи**

```java
public long time(String key) {
	return Instances.get("redis", Redis.class).time(key);
}
```

**Получить значение записи**

```java
public String get(String key) {
	return Instances.get("redis", Redis.class).get(key);
}
```

**Удалить запись

```java
public void remove(String key) {
	return Instances.get("redis", Redis.class).remove(key);
}
```

**Получить список значений**

```java
public ConcurrentHashMap<String, String> entries(int amount) {
	return Instances.get("redis", Redis.class).entries(amount);
}
```
## Интеграция с MongoDB

Для включения интеграции с MongoDB, в конфигурационном файле **core/settings.yml** вы должны изменить следющие поля:

```yml
mongodb:  
  flag: false # Use mongo instance  
  uri: "" # Connection uri for MongoDB  
  database: "" # MongoDB database name
```

Так как фаил **core/settings.yml** имеет динамический статус и его данные обновляются с проходом главного цикла CoreAPI следует вводить данные в определенной последовательности, чтобы избежать не нужных ошибок в логах. Сначала поменяйте поля **uri** и **database**, затем, если вы ввели данные верно, значение **flag** измение на true. 

После этого, автоматически начнется процесс подключения к MongoDB. После успешного подключения в экземплярах появится экземпляр MongoDB с названием "mongodb".

**Создать модель коллекции**

Создавая модель вы указывается название коллекции к которой принаддежит эта модель, информацию о ключевом поле и полей модели.

```java
@Model(collection = "userdata")  
public class Userdata {  
    @Id  
    UUID uuid;  
    String username;  
    ...
}
```

**Получить коллекцию**

Все запросы и сообщения между базой данных и вашим проектом происходят через методы коллекций. Чтобы получить коллекцию вашей модели, используйте метод collection в экземпляре MongoDB.

```java
public MongoCollection<Document> collection() {
	return Instances.get("mongodb", MongoDB.class).collection(Userdata.class);
}
```

**Преобразовать Model в Document**

```java
public Document document(Userdata userdata) {
	return Instances.get("mongodb", MongoDB.class).document(userdata);
}
```

**Преобразовать Document в Model**

```java
public <T> T model(Document document, Class<T> model) {
	return Instances.get("mongodb", MongoDB.class).model(document, model)
}
```

