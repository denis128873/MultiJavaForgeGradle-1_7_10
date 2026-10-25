# MultiJavaForgeGradle-1.7.10

Gradle плагин для сборки Forge 1.7.10 модов от LuxinfineTeam с поддержкой нескольких версий Java (Java 8 и Java 25) и автоматическим распилом на клиент/сервер через JarSplitter.

## Возможности

- Компиляция под Java 8 и Java 25 из одного исходного кода
- Распил на клиент/сервер/dev версии через JarSplitter
- Два режима работы:
  - **Приватный JarSplitter** (платный) - полная поддержка j8 и j25 билдов
  - **Публичный плагин** (бесплатный) - автоматическое подключение через JitPack, только j8
- Единая команда сборки `multiBuild`
- **Поддержка `replace`** - подстановка шаблонов в исходном коде (версия, мод ID и т.д.)

## Gradle таски

### Основные таски

- **`multiBuild`** - Полная сборка:
  - С приватным JarSplitter: собирает и обфусцирует j8 и j25, распиливает оба
  - С публичным плагином: собирает и обфусцирует только j8, распиливает его
  
- **`jar`** - Сборка Java 8 jar (classifier: `j8`)
- **`jarJava25`** - Сборка Java 25 jar (classifier: `j25`) *(только с приватным JarSplitter)*
- **`compileJava25`** - Компиляция Java 25 классов *(только с приватным JarSplitter)*

## Режимы работы JarSplitter

**Как получить:** Приватный JarSplitter является платным инструментом. Свяжитесь с LuxinfineTeam для получения доступа.

**Результат сборки:**
```
build/libs/
├── YourMod-1.0-j8.jar          # Obfuscated Java 8
├── YourMod-1.0-j8-client.jar   # Client-side only
├── YourMod-1.0-j8-server.jar   # Server-side only
├── YourMod-1.0-j8-dev.jar      # Dev build (deobf)
├── YourMod-1.0-j25.jar         # Obfuscated Java 25
├── YourMod-1.0-j25-client.jar  # Client-side only
├── YourMod-1.0-j25-server.jar  # Server-side only
└── YourMod-1.0-j25-dev.jar     # Dev build (deobf)
```

### Публичный плагин (бесплатный, fallback)

Если приватный JarSplitter не найден, плагин автоматически подключает бесплатный публичный JarSplitter с JitPack.

**Ограничения:**
- ⚠️ Поддерживается только Java 8 билд
- ⚠️ Java 25 билд пропускается
- ⚠️ Только базовые возможности распила

**Результат сборки:**
```
build/libs/
├── YourMod-1.0-j8.jar          # Obfuscated Java 8
├── YourMod-1.0-j8-client.jar   # Client-side only
├── YourMod-1.0-j8-server.jar   # Server-side only
└── YourMod-1.0-j8-dev.jar      # Dev build (deobf)
```

## Пример использования

```bash
# Полная сборка
./gradlew multiBuild

# Только Java 8
./gradlew jar useSplitterJ8

# Только Java 25 (требует приватный JarSplitter)
./gradlew jarJava25 useSplitterJ25
```

## Конфигурация Java

Плагин автоматически настраивает:
- **Java 8**: `options.release = 8`, `encoding = 'UTF-8'`
- **Java 25**: `options.release = 25`, `encoding = 'UTF-8'`, через Java Toolchain

Убедитесь, что у вас установлена JDK 25 для сборки Java 25 версии.

## Подстановка шаблонов (Replace)

Плагин поддерживает параметр `replace` для подстановки значений в исходный код перед компиляцией (аналогично ForgeGradle-1_2).

### Базовое использование

```groovy
ext.replace = [
    '@VERSION@': project.version,
    '@MC_VERSION@': '1.7.10'
]

// Указать конкретные файлы (опционально)
ext.replaceIn = ['com/yourmod/YourMod.java']
```

### Пример

**Исходный код:**
```java
@Mod(modid = "yourmod", version = "@VERSION@", acceptedMinecraftVersions = "@MC_VERSION@")
public class YourMod {
    public static final String VERSION = "@VERSION@";
}
```

**После сборки:**
```java
@Mod(modid = "yourmod", version = "1.0.0", acceptedMinecraftVersions = "1.7.10")
public class YourMod {
    public static final String VERSION = "1.0.0";
}
```

## Зависимости

- [ForgeGradle 1.2](https://github.com/LuxinfineTeam/ForgeGradle-1_2) - Сборка Forge модов c доработками от LuxinfineTeam
- [JarSplitterGradle](https://github.com/LuxinfineTeam/JarSplitterGradle) - Распил на клиент/сервер
- Приватный JarSplitter - Расширенная версия с поддержкой Java 25 (платный) (опционально)
