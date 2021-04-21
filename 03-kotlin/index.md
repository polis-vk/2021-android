# Kotlin

Kotlin – это статически типизированный, объектно-ориентированный, современный, кросс-платформенный язык
программирования. Разработка началась в 2010 году и ведется до сих пор.

### Почему появился?

Перед JetBrains встала задача стать более продуктивными с полной совместимостью старых разработок. В первую очередь с
Java, а затем уже с JS, C++.

### Приемущества перед Java

1. Лаконичнее
2. Null safety
3. Smart cast
4. Global functions
5. Extensions
6. Lambdas
7. Нет проверяемых exceptions

### Синтаксис языка

Разработчики языка использовани элементы и заимствовали опыт многих языков программирования, например, таких как Java,
Scala, C#, Groovy, C++, JavaScript.

## Пакеты

Имя пакета указывается в начале файла так же как и в Java, но есть одно удобство: имя пакета может не совпадать со
структурой папок.

Например, мы хотим создать disposableContainer для ViewModel в файле ViewModelExtensions.kt, для этого нам нужны методы
getTag/setTagIfAbsent из класса ViewModel, которые имеют видимость package-only. Тогда мы зададим имя пакета
как `androidx.lifecycle` и мы сможем воспользоваться этими методами.

## Функции

Функция объявляется с помощью ключевого слова `fun`.

```kotlin
fun helloWorld() {
    println("Hello, world!")
}
```

В функции могут быть аргументы (а может и не быть)

```kotlin
fun sendMessage(message: Message) {
    //
}
```

может быть возвращаемое значение (а может и не быть и тогда котлиновский комплятор преобразует возвращаемое значение к
типу Unit, аналог void из Java)

```kotlin
fun sendMessage(message: Message): Boolean {
    // 
    return true
}
```

Аргументы могут иметь дефолтное значение, для того, чтобы вызвать такую функцию из java, нужно добавить аннотацию
@JvmOverloads (об интеропе с джава чуть позже)

```kotlin
fun updateColor(color: Color = Color.RED) {
}
```

При вызове функции с аргументами мы можем явно указывать имя такого параметра.

```kotlin
fun drawView() {
    updateColor(color = Color.BLACK)
}
```

Как и в джаве функции могут выбрасывать исключения (но они непроверяемые!)

```kotlin
fun getFileExtension(fileName: String): String {
    val dotIndex = fileName.indexOf(".")
    return fileName.substring(dotIndex, fileName.length)
}

fun main() {
    val fileName = "" // пустая строка
    val extension = getFileExtension("")
    // будет выкинут java.lang.StringIndexOutOfBoundsException: String index out of range: -1
}
```

Для лаконичности существует такое понятие как expression function

```kotlin
fun isZero(number: Number): Boolean = number == 0
```

Функцию можно объявить в теле другой функции

```kotlin
fun main() {
    fun printHelloWorld() {
        println("Hello, world!")
    }
    printHelloWorld()
}
```

Функции могут быть расширениями

```kotlin
fun Integer.isZero(): Boolean = this == 0
```

## Классы

### open/final

Работа с классами похожа на то как это происходит в Java, так же нет множественного наследования, присутствуют
абстрактные классы, интерфейсы, енумы. Но когда мы объявляем класс в джава от него можно унаследоваться по умолчанию, в
котлин же все классы по умолчанию final. Если мы хотим сделать класс наследуемым, то следует указать ключевое
слово `open`.

```kotlin
open class MyClass {

}

class MySuperClass : MyClass() {

}

class MySecondSuperClass : MySuperClass() // ошибка! MySuperClass является final
```

### Any/java.lang.Object

По аналогии с Java суперклассом для всех объектов в Kotlin по умолчанию является kotlin.Any (как java.lang.Object)

### Конструкторы

Можно создать несколько конструкторов, в которых можно задать дефолтные значения каким-то пропертям.

```kotlin
class Student(
    val firstName: String = "John",
    val lastName: String = "Doe"
) {

    constructor(map: Map<String, String>) : this(
        firstName = map.getValue("firstName"),
        lastName = map["lastName"]
    )

    constructor(jsonObject: JsonObject) : this(
        firstName = jsonObject.getString("firstName"),
        lastName = jsonObject.getString("lastName")
    )
}
```

### Properties

В отличии от Java в котлин классе используются проперти(свойства), к которым можно переопределить getter и setter

Свойства могут быть изменяемые (`var`) и неиз"меняемые (`val`). Так же можно их объявить как `lateinit var`. Это
полезно, когда мы 100% получим значение чуть позже и сможем им воспользоваться. Например, в Activity в методe onCreate()
мы проинициализируем viewModel.

Все мы знаем что в java setter и getter нужно генерировать, в котлин же все происходит без нашего участия

```kotlin
class Student(
    val firstName: String = "John",
    val lastName: String = "Doe"
) {
    var age: Int? = null
        set(value) { // переопределенный сеттер с валидацией
            if (value > 18) {
                field = value
            }
        }
}
```

### Visibility modifiers

Проперти класса могут иметь доступы public, private, internal Но отсутствует такое видимость package-private!

### Object

Класс можно объявить как синглтон при помощи ключевого слова object

```kotlin
object Logger {
    fun log() {

    }
}
```

Больше нельзя создать экземпляр данного класса Logger, он может быть только один.

### Sealed class

В котлин есть чудесная фича под названием sealed class // (что такое) реализовать state рассказать про const val val/var
when/if expression

```kotlin
sealed class State {
    object Loading : State()
    class Success(val code: Int) : Result()
    class Failure(val error: Error) : Result()
}

```

При получении объекта типа State мы можем понять какой конкретно подкласс имеется в виду, среда разработки поможет нам в
этом - она сделает smartcast объекта

```kotlin
when (state) {
    Loading -> println("Loading..")
    is Success -> println("Success ${state.code}") // в данном случае IDE подскажет, что state - это экземпляр State.Success
    is Failure -> println("Failure! ${state.error}") // и здесь
}
```

### Companion object

В джава существуют static final поля, в котлин такого нет, но есть Companion object, в котором можно хранить такие же
свойства

```kotlin
class MyActivity : Activity() {
    companion object {
        private const val TAG = "MyActivity" // аналогично private static final String TAG = "MyActivity";
    }
}
```

### Интерфейсы

Интерфейсы не особо отличаются от джава, они стали немного лаконичнее

```kotlin
interface Cloneable {
    fun clone() = Unit

    companion object {
        const val TAG = "Cloneable"
    }
}
```

### Enum

Используется для перечислений, отличий от java нет.

```kotlin
enum class MimeType(val mimeType: String) {
    JPEG("jpeg"),
    MP4("mp4");
}
```

### Data class

Как следует из названия, предназначен для описания объектов данных (например, запрос из сети, объект из БД).

Data class автоматически генерирует equals/hashCode/toString/componentN/copy по свойствам, объявленных в primary
constructor.

В java нам бы это пришлось все делать вручную.

```kotlin
data class Image(
    val id: Long = 0,
    val width: Int = 0,
    val height: Int = 0,
    val size: Int = 0,
    val path: String? = null
)
```

## Как работать с java и kotlin одновременно

Для этого нам нужно воспользоваться некоторыми аннотациями.

```kotlin
class CustomView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    @AttrRes defStyleAttr: Int = 0
) : FrameLayout(context, attrs, defStyleAttr) {

    @JvmField
    val index: Int = 0

    companion object {
        @JvmStatic
        fun create(context: Context): CustomView = CustomView(context)
    }
}
```

`@JvmOverloads` - помогает сгенерировать для java несколько конструкторов (их здесь будет 3).
`@JvmField` - позволит обращаться к свойству index напрямую, минуя геттер
`@JvmStatic` - даст нам вызвать метод create как статический без обращения к Companion

## Функции/фичи которые могут пригодится

### Полезные глобальные функции `listOf()`, `mapOf()`

```kotlin
val list = listOf("1", "a", "ccc") // помогает создавать список
val map = mapOf(1 to "1", "2" to "2") // создает Map
```

### Форматирование строк

```kotlin
val state = State.Loading
val string = "current state is $state" // символ $ позволяет подставлять переменные для форматирования строки
```

### runCatching

Поможет нам не писать лишний раз обработчик ошибки в try/catch блоке

```kotlin
val text = runCatching {
    readFile("~/Documents/file.txt").readAllLines()
}.getOrNull()

runCatching {
    callVeryDangerousFun() // если этот вызов выкинет exception то ничего не случится, мы его обработали и проигнорировали
}
```

### Scope functions: `apply, let, also, with`

```kotlin
class I {
    var intent: Intent? = null
    val view: TextView

    fun scopeFunction() {
        val intent = Intent().apply { // apply полезен для создания объектов
            putExtra("name", "value")
        }

        with(view) { // полезно для настройки объекта, в данном случае мы задаем свойства нашей вью в ее собственном скоупе
            backgroundColor = Color.BLACK
            text = "Some text"
            textSize = 12
        }

        val i = intent?.let { // let полезен для работы с nullable объектами
            val string = it.extras.getString("1")
            runCathing { string.toInt() }.getOrDefault(5)
        }
    }
}
```

### Pair

Иногда нужно сгруппировать 2 значения, а создавать для этого целый класс не самая лучшая идея. На помощь приходит класс
Pair. Создание экземпляра Pair очень простое и лаконичное:

```kotlin
val (from, to) = 1 to 20 // будет создана Pair<Int, Int>
// а запись выше называется destructuring declaration 
// from == 1
// to == 20
```

### Operators

В примере выше используется инфиксный оператор-функция `to`. Пример создания своей функции для класса Point

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

val point = Point(10, 20)

fun main() {
    println(-point)  // prints "Point(x=-10, y=-20)"
}
```

# Корутины

## Краткая история и зачем нужны

Работа с потоками - это непросто. Так же потоки довольно тяжелая и ресурсозатратная вещь (особенно на мобилках).

Концепция корутин (они же сопрограмм появилась очень давно).

Первая реализация такого подхода была с C# в 2012 году: появились ключевые слова async/await

JetBrains услышали зов и моду на новый подход — асинхронное программирование (очень похоже на js) и решили реализовать
свою концепцию

Как жили без корутин

```kotlin
fun requestToken(onResult: (token: String) -> Unit) {
    api.requestToken(
        onSuccess = { token -> onResult(token) }, onFailure = { error -> println(error) }
    )
}

fun sendMessage(message: String, onSuccess: () -> Unit) {
    requestToken { token ->
        api.sendMessage(token, message) {

        }
    }

}

```

Так вот это называется callback hell. Здесь есть проблема правильной обработки ошибок.

Java попыталась сгладить эту проблему с помощью Future (они же promises).

Так же есть библиотека rxjava. Но эти подходы созданы для джавы и они работают с потоками.

И коллбеки никуда не уходят!

Для решения всех этих проблем в котлине есть корутины, они же легковесные потоки.

```kotlin

suspend fun requestToken(): String {
    return api.requestToken()
}

suspend fun sendMessage(message: String) {
    val token = requestToken()
    val result = api.sendMessage(token, message)
}
```

Добавляя модификатор suspend мы говорим что это функция работает асинхронно. Мы пишем обычный процедурный код, который
проще читать. Мы не отвлекаемся на callback hell или на другой подход. Каждую корутину мы можем запускать в своем
диспетчере (то же самое что и executor в java).

Есть стандартные диспетчеры такие как:

- `Default` – для операций которые лучше выполнять в бэкграунд потоках
- `IO` – для работы с файловой системой и сетью
- `Main` – главный поток приложения для отрисовки ui

Один из главых плюсов корутин является быстрое переключение контекста (контекст - это то в каком потоке выполняется
корутина)

```kotlin
suspend fun getEntitiesFromDb(): List<Entity> = withContext(Dispatchers.IO) {
    // здесь чтение из бд будет выполнено внутри IO диспетчера
    val dbEntities = db.selectAllEntities()

    return withContext(Dispatchers.Default) {
        // а маппинг обхектов в диспетчере Default
        dbEntities.map { it.toEntity() }
    }
}
```

### О том как запускать корутины

Рассмотрим ViewModel.

ViewModel имеет свой скоуп -  `viewModelScope`, который "умрет" когда уничтожится viewmodel.

```kotlin
class MainViewModel : ViewModel() {
    private val api: Api = TODO()

    init {
        val job = viewModelScope.launch(Dispatchers.Default) { // launch создает объект Job и запускает корутину
            println("${Thread.currentThread()}")
            delay(5000)

            withContext(Dispatchers.Main) { // переключение контекста на Main
                if (isActive) { // проверка на активность корутины, вдруг она отменена
                    return@withContext
                }
                println("${Thread.currentThread()}")
            }

            withContext(Dispatchers.IO) { // переключение на IO 
                println("${Thread.currentThread()}")
            }
        }

        job.cancel() // отмена запущенной работы

        println("log")

        fetchData()
    }

    private val _uiData = MutableStateFlow(emptyList<Int>())
    val uiData = _uiData.asStateFlow()

    private fun fetchData() {
        viewModelScope.launch { // запускаемся на Main потоке
            val data = api.get() // api.get – suspend функция которая выполняется в IO, а результат мы получим в Main
            val uiData = withContext(Dispatchers.Default) {
                data.map { it.toInt() } // произведем маппинг объектов внутри default 
            }

            _uiData.value = uiData // и присвоим значения на мейн потоке

        }
    }

    private class Api {
        suspend fun get(): List<Item> = withContext(Dispatchers.IO) {
            return getItems("https://mail.ru/items")
        }

    }
}
```

Запустить корутину можно при помощи:

- launch - возвращает объект Job
- async - возвращаем объект Deferred<T> где T - это результат

Если упрощенно: когда нужно что-то запустить и нам не нужен результат этой операции – используем launch, когда нужен
результат – async.




