#pattern #architecture #php 
**Pipeline** — это архитектурный шаблон проектирования, который используется для прохождения данных через последовательность этапов (или обработчиков). Каждый этап берет на вход данные, обрабатывает их и передает результат следующему этапу. Этот паттерн широко используется для создания гибкой и легко расширяемой архитектуры.

---

#### Когда использовать паттерн Pipeline
Этот паттерн полезен, если: 
* 🔹 Есть последовательность шагов обработки данных, которую нужно реализовать.
* 🔹 Необходима возможность добавлять, убирать или изменять порядок обработки шагов без изменения основного кода.
* 🔹 Нужно сгруппировать обработку данных и сделать код более читаемым и модульным.
* 🔹 Требуется переиспользование шагов (обработчиков) в разных местах программы.

#### Преимущества паттерна Pipelin
* 🔹 **Повышенная читаемость кода**: Шаги обработки четко структурированы, каждый шаг инкапсулирует свою логику.
* 🔹 **Расширяемость**: Чтобы добавить новый этап обработки, достаточно просто подключить его в конвейер.
* 🔹 **Модульность**: Каждый обработчик изолирован и легко тестируется отдельно.
* 🔹 **Переиспользуемость**: Обработчики можно использовать в других конвейерах или сценариях.
	
#### Суть паттерн
🔹 **Шаги обработки (Stages/Handlers)**: Каждый обработчик реализует определенную операцию над данными.
🔹 **Конвейер (Pipeline)**: Проход по цепочке обработчиков, где результат работы одного шага передается следующему.
🔹 **Гибкость**: Легко добавлять, изменять или удалять обработчики, не вмешиваясь в основной процесс обработки.

#### Пример:

**простой конвейерной обработки данных**

```php
// каждый обработчик(этап обработки) должен реализовывать интерфейс Handler
interface Handler
{
    public function handle($payload);
}

// Классы-обработчики (Concrete Handlers), конкретная реализация
class AddPrefixHandler implements Handler
{
    public function handle($payload)
    {
        return "Prefix: " . $payload;
    }

}

class AddSuffixHandler implements Handler
{
    public function handle($payload)
    {
        return $payload . " :Suffix";
    }
}

// Сам Pipeline здесь содержится логика последовательного выполнения обработчиков.
class Pipeline
{
    private array $handlers = [];
    public function pipe(Handler $handler): self
    {
        $this->handlers[] = $handler;
        return $this;
    }

    public function process($payload)
    {
        foreach ($this->handlers as $handler) {
            $payload = $handler->handle($payload);
        }

        return $payload;
    }
}

// использования Pipeline
$pipeline = new Pipeline();

$pipeline
    ->pipe(new AddPrefixHandler())
    ->pipe(new AddSuffixHandler());

$result = $pipeline->process("Data");
echo $result; // "Prefix: Data :Suffix"
```


**Пример реализации обработки текста**
*здесь создается payload с данными о тексте* 

```php
namespace App\Services\TextProcess;

final class TextPayload
{
    public string $rawText;
    public string $processedText;
    public array $links = [];
    public array $blocks = [];

    public function __construct(string $text)
    {
        $this->rawText = $text;
        $this->processedText = '';
    }
}
```

*Интерфейс для обработчиков*

```php
namespace App\Services\TextProcess;

interface TextProcessorHandler
{
    public function handle(TextPayload $payload): TextPayload;
}
```

*Обработчики*

```php
namespace App\Services\TextProcess\Handlers;

use App\Services\TextProcess\TextPayload;
use App\Services\TextProcess\TextProcessorHandler;

final class GetLinks implements TextProcessorHandler
{
    public function handle(TextPayload $payload): TextPayload
    {
        // в текст добавляем линкам класс
        $payload->processedText = $this->addClassIntoText($payload->originalText);
        // получаем все линки из текста
        $payload->links = $this->getLinks($payload->originalText);

        return $payload;
    }

    public function getLinks(string $text): array
    {
        // логика
        return [];
    }

    public function addClassIntoText(string $text): string
    {
        // логика
        return $text;
    }
}


namespace App\Services\TextProcess\Handlers;

use App\Services\TextProcess\TextPayload;
use App\Services\TextProcess\TextProcessorHandler;

final class BreakTextIntoBlocks implements TextProcessorHandler
{
    public function handle(TextPayload $payload): TextPayload
    {
        $text = $payload->originalText;

        $payload->blocks = $this->run($text);

        return $payload;
    }

    // здесь логика по разбиваем текст на блоки и возвращаем их
    public function run(string $text): array
    {
        return [];
    }
}
```

*Pipeline*

```php
namespace App\Services\TextProcess;

final class TextProcessingPipeline
{
    private array $handlers = [];

    public function addHandler(TextProcessorHandler $handler): self
    {
        $this->handlers[] = $handler;
        return $this;
    }

    public function process(TextPayload $payload): TextPayload
    {
        foreach ($this->handlers as $handler) {
            $payload = $handler->handle($payload);
        }

        return $payload;
    }
}
```

*Использование*

```php
$pipeline = new TextProcessingPipeline();
$pipeline
    ->addHandler(new GetLinks())
    ->addHandler(new BreakTextIntoBlocks());

$text = "&lt;h2&gt;Title&lt;/h2&gt;";
$payload = new TextPayload($text);

$result = $pipeline->process($payload);
```

#### Реальные примеры использования
* 🔹	Очистка и фильтрация данных
* 🔹	Последовательная обработка файлов (например, изображений).
* 🔹	Создание обработчиков запросов или событий. 
* 🔹	Обработка данных пользователя перед их сохранением.