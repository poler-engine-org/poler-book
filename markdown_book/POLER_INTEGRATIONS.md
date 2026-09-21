# POLER Engine — Том: INTEGRATIONS

Файлов в томе: 20

---

## File: `integrations/kate-poler/README.md`

- Язык: `markdown`
- Размер: `6223` байт

```markdown
# poler-kate — Kate × POLER Engine

**Нативный плагин KTextEditor (KF6/Qt6/C++)**, встраивающий суверенное ядро
`poler-engine` в Kate: топографический поиск, кристалл памяти Trit5, моторный
мост S2→E2 и полнодисковый сборщик — без единой потери нативного функционала
и внешнего вида Kate.

## Почему плагин, а не форк

Требование: «сохранить весь функционал и внешний вид» Kate 26.08. Форк означал
бы вечную гонку с апстримом KDE и риск потери нативности. Плагин — официальный
механизм расширения: Kate остаётся Kate на 100%, а движок садится рядом как
равноправный орган через публичный API `KTextEditor::Plugin` +
`MainWindow::createToolView` + `KTextEditor::Command`.

Архитектурный принцип: **плагин не дублирует алгоритмы движка** — он вызывает
бинарник `poler-engine` и парсит его готовый JSON (`grep-json`, `ai-json`,
`triune-json`). Все гарантии движка (O(1) кольцевые буферы, таймаут-каскады
SIGTERM→SIGKILL, отсутствие зомби и TTY-дедлоков — см.
`docs/ENGINE_EXECUTION_PROTOCOL.md`) наследуются автоматически.

## Что появляется в Kate

Панель **POLER Engine** (правая сторона, скрывается по Esc) с 4 вкладками:

| Вкладка | Возможности | Вызов движка |
|---|---|---|
| **Поиск** | точные строки с переходом по двойному клику; сцены с ε/резонансом; multi-word = proximity-AND | `<root> --grep Q --grep-json --grep-i` / `<root> -q Q --format ai-json` |
| **Кристалл** | синапсы слова в Trit5: возбуждающие (+1) / тормозные (−1) | `--triune-crystal-inspect WORD` |
| **Мотор** | директивы RU/UA («покажи статус git»), R1 авто / M2 с подтверждением, телеметрия речи + motor_exec | `--triune-speak T --motor-act [--motor-yes] --triune-json` |
| **Сбор** | полнодисковый харвестер: корни, термы, формат (markdown/json/corpus), живой прогресс, открытие результата | `--harvest-disk ROOTS --harvest-query T --harvest-out F` |

Консольные команды (командная строка Kate / vim-режим):

```
:poler <запрос>       — поиск по каталогу активного документа
:pcrystal <слово>     — инспекция кристалла памяти
:pmotor <директива>   — моторный мост S2→E2
:pharvest <термы>     — полнодисковый сбор
```

## Сборка и установка (Arch Linux)

```bash
# 1. Зависимости (kate уже установлен — ktexteditor подтянется):
sudo pacman -S base-devel cmake extra-cmake-modules qt6-base \
               kf6-texteditor kf6-kcoreaddons kf6-ki18n

# 2. Сборка из корня репозитория poler-engine:
cd integrations/kate-poler
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr
cmake --build build -j$(nproc)

# 3. Установка (плагин ляжет рядом с плагинами Kate: kf6/ktexteditor/):
sudo cmake --install build

# 4. Движок должен быть в PATH (поиск: $POLER_ENGINE_BIN →
#    ~/.local/bin/poler-engine → /usr/local/bin → /usr/bin → PATH):
which poler-engine || ls ~/.local/bin/poler-engine
```

Включение: **Kate → Settings → Configure Kate → Plugins → «POLER Engine»**.
Панель появится справа; панель/вкладки виджетов Kate настраиваются как обычно
(перетаскивание, скрытие, профили сессий сохраняются).

## Файлы

| Файл | Роль |
|---|---|
| `polerplugin.json` | метаданные KPlugin (Id: polerengineplugin) |
| `polerplugin.h/.cpp` | `PolerPlugin` (createView) + `PolerPluginView` (toolview, Esc) |
| `polerpanel.h/.cpp` | панель: 4 вкладки, парсинг JSON, навигация к строке |
| `enginebridge.h/.cpp` | асинхронный QProcess-мост + живой stderr-прогресс |
| `polercommands.h/.cpp` | команды `:poler/:pcrystal/:pmotor/:pharvest` |

## Технические решения

- **K_PLUGIN_FACTORY_WITH_JSON** + `kcoreaddons_add_plugin(INSTALL_NAMESPACE
  "kf6/ktexteditor")` — тот же механизм, каким Kate собирает собственные
  addons (проверено по `addons/CMakeLists.txt` апстрима).
- Навигация к строке: `MainWindow::openUrl` + `View::setCursorPosition`;
  для сцен номер строки извлекается из `scene.chapter` («… (L1857–L1860)»),
  для grep — точный `line_no` из `grep-json`.
- Один активный вызов движка на мост (защита от гонок), отмена — SIGTERM→SIGKILL.
- Колбэки завершения защищены `shared_ptr<bool>` (однократный вызов, no UB).
- Кириллица: движок сам матчит RU/UA во всех регистрах (Aho-Corasick).

## Дорожная карта

- Инкрементальное дообучение кристалла из панели (`--crystal-ingest-dir`).
- Инлайн-подсветка резонанса в редакторе (KTextEditor::MovingRange).
- Автосбор перед сессией: `--harvest-format corpus` → кристалл.
- LSP-подобный режим подсказок из кристалла (KTextEditor::CodeCompletionModel).

```

---

## File: `integrations/kate-poler/enginebridge.cpp`

- Язык: `cpp`
- Размер: `5025` байт

```cpp
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#include "enginebridge.h"

#include <QFileInfo>
#include <QProcess>
#include <QStandardPaths>
#include <QTimer>

#include <memory>

EngineBridge::EngineBridge(QObject *parent)
    : QObject(parent)
{
}

EngineBridge::~EngineBridge()
{
    cancel();
}

QString EngineBridge::engineBinary()
{
    // 1. Явное переопределение окружением.
    if (const QString env = qEnvironmentVariable("POLER_ENGINE_BIN"); !env.isEmpty()) {
        return env;
    }
    // 2. Канонические места установки движка (см. docs/skills/poler-engine.md).
    const QStringList candidates = {
        QStringLiteral("%1/.local/bin/poler-engine").arg(qEnvironmentVariable("HOME")),
        QStringLiteral("/usr/local/bin/poler-engine"),
        QStringLiteral("/usr/bin/poler-engine"),
    };
    for (const QString &c : candidates) {
        if (QFileInfo::exists(c) && QFileInfo(c).isExecutable()) {
            return c;
        }
    }
    // 3. PATH.
    return QStandardPaths::findExecutable(QStringLiteral("poler-engine"));
}

bool EngineBridge::available()
{
    const QString bin = engineBinary();
    return !bin.isEmpty() && QFileInfo(bin).isExecutable();
}

void EngineBridge::run(const QStringList &args, ResultCallback callback, int timeoutMs, StderrChunk stderrChunk)
{
    if (m_busy) {
        if (callback) {
            callback(-1, QString(), QStringLiteral("engine busy: previous call in flight"));
        }
        return;
    }
    const QString bin = engineBinary();
    if (bin.isEmpty()) {
        if (callback) {
            callback(127,
                     QString(),
                     QStringLiteral("poler-engine not found — install to ~/.local/bin or set $POLER_ENGINE_BIN"));
        }
        Q_EMIT failed(QStringLiteral("poler-engine not found"));
        return;
    }

    m_busy = true;
    m_process = new QProcess(this);
    m_process->setProgram(bin);
    m_process->setArguments(args);
    // Раздельные каналы: stdout — данные (JSON), stderr — прогресс/диагностика.
    m_process->setProcessChannelMode(QProcess::SeparateChannels);
    m_process->setWorkingDirectory(qEnvironmentVariable("HOME"));

    Q_EMIT started();

    // Один вызов колбэка — по завершении процесса ИЛИ таймауту.
    // shared_ptr: лямбды живут дольше кадра run(), захват по ссылке запрещён.
    auto *proc = m_process;
    auto called = std::make_shared<bool>(false);

    // Живой прогресс (харвестер пишет сводку в stderr).
    if (stderrChunk) {
        connect(proc, &QProcess::readyReadStandardError, this, [proc, stderrChunk] {
            stderrChunk(QString::fromUtf8(proc->readAllStandardError()));
        });
    }

    QTimer *timeout = nullptr;
    if (timeoutMs > 0) {
        timeout = new QTimer(proc);
        timeout->setSingleShot(true);
        connect(timeout, &QTimer::timeout, proc, [proc] {
            // QProcess делает graceful: SIGTERM всей группе, затем SIGKILL.
            proc->terminate();
            QTimer::singleShot(2000, proc, [proc] {
                if (proc->state() != QProcess::NotRunning) {
                    proc->kill();
                }
            });
        });
    }

    connect(proc, &QProcess::errorOccurred, this, [this, proc, callback, called](QProcess::ProcessError err) {
        if (*called) {
            return;
        }
        *called = true;
        m_busy = false;
        const QString msg = QStringLiteral("poler-engine process error: %1").arg(int(err));
        if (callback) {
            callback(-2, QString(), msg);
        }
        Q_EMIT failed(msg);
        proc->deleteLater();
    });

    connect(proc,
            &QProcess::finished,
            this,
            [this, proc, callback, called](int exitCode, QProcess::ExitStatus) {
                if (*called) {
                    return;
                }
                *called = true;
                m_busy = false;
                const QString stdOut = QString::fromUtf8(proc->readAllStandardOutput());
                const QString stdErr = QString::fromUtf8(proc->readAllStandardError());
                if (callback) {
                    callback(exitCode, stdOut, stdErr);
                }
                Q_EMIT finished(exitCode);
                proc->deleteLater();
            });

    if (timeout) {
        timeout->start(timeoutMs);
    }
    m_process->start();
}

void EngineBridge::cancel()
{
    if (m_process && m_process->state() != QProcess::NotRunning) {
        m_process->terminate();
        QTimer::singleShot(2000, m_process, [p = m_process] {
            if (p->state() != QProcess::NotRunning) {
                p->kill();
            }
        });
    }
}

#include "moc_enginebridge.cpp"

```

---

## File: `integrations/kate-poler/enginebridge.h`

- Язык: `c`
- Размер: `3016` байт

```c
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#pragma once

#include <QObject>
#include <QString>
#include <QStringList>

#include <functional>

/**
 * EngineBridge — асинхронный мост к бинарнику poler-engine.
 *
 * Движок остаётся единственным суверенным ядром: плагин НЕ дублирует его
 * алгоритмы, а вызывает CLI (grep-json / ai-json / triune / harvest) через
 * QProcess и парсит готовый JSON. Никакой сети, никаких ключей — всё локально.
 *
 * Гарантии движка наследуются автоматически: O(1) кольцевые буферы вывода,
 * каскадные таймауты SIGTERM→SIGKILL, отсутствие зомби и TTY-дедлоков
 * (см. docs/ENGINE_EXECUTION_PROTOCOL.md в репозитории poler-engine).
 */
class EngineBridge : public QObject
{
    Q_OBJECT

public:
    using ResultCallback = std::function<void(int exitCode, const QString &stdOut, const QString &stdErr)>;
    using StderrChunk = std::function<void(const QString &chunk)>;

    explicit EngineBridge(QObject *parent = nullptr);
    ~EngineBridge() override;

    /**
     * Поиск бинарника движка: $POLER_ENGINE_BIN → ~/.local/bin/poler-engine →
     * /usr/local/bin/poler-engine → /usr/bin/poler-engine → PATH.
     * Возвращает пустую строку, если не найден.
     */
    static QString engineBinary();

    /** Движок найден и исполняем? */
    static bool available();

    /** Идет ли сейчас какой-то вызов (один активный вызов на мост). */
    bool busy() const
    {
        return m_busy;
    }

    /**
     * Запустить движок с аргументами. Колбэк вызывается один раз по завершении
     * (успех, ошибка или таймаут). Аргументы формирует вызывающая сторона —
     * движок сам гарантирует отсутствие шелл-инъекций (argv-массив).
     * @param timeoutMs 0 = без таймаута (движок сам прибьёт по своим правилам)
     * @param stderrChunk опциональный живой приёмник stderr (прогресс харвестера)
     */
    void run(const QStringList &args, ResultCallback callback, int timeoutMs = 0, StderrChunk stderrChunk = nullptr);

    /** Отменить текущий вызов (SIGTERM группе → SIGKILL — делает сам движок/QProcess). */
    void cancel();

Q_SIGNALS:
    void started();
    void finished(int exitCode);
    void failed(const QString &error);

private:
    class QProcess *m_process = nullptr;
    bool m_busy = false;
};

```

---

## File: `integrations/kate-poler/polercommands.cpp`

- Язык: `cpp`
- Размер: `3523` байт

```cpp
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#include "polercommands.h"

#include "polerpanel.h"

#include <KTextEditor/View>

#include <KLocalizedString>

namespace
{
QStringList polerCommands()
{
    return {QStringLiteral("poler"), QStringLiteral("pcrystal"), QStringLiteral("pmotor"), QStringLiteral("pharvest")};
}
}

PolerCommands::PolerCommands(PolerPanel *panel, QObject *parent)
    : KTextEditor::Command(polerCommands(), parent)
    , m_panel(panel)
{
}

bool PolerCommands::exec(KTextEditor::View *, const QString &cmd, QString &msg, const KTextEditor::Range &)
{
    const QString name = cmd.section(QLatin1Char(' '), 0, 0).trimmed();
    const QString arg = cmd.section(QLatin1Char(' '), 1).trimmed();

    if (arg.isEmpty()) {
        msg = i18n("Укажи аргумент: :%1 <текст>", name);
        return false;
    }
    if (!m_panel) {
        msg = i18n("Панель POLER недоступна");
        return false;
    }

    if (name == QLatin1String("poler")) {
        m_panel->searchFor(arg);
        msg = i18n("POLER: поиск «%1» запущен (вкладка Поиск)", arg);
        return true;
    }
    if (name == QLatin1String("pcrystal")) {
        m_panel->crystalFor(arg);
        msg = i18n("POLER: инспекция кристалла «%1»", arg);
        return true;
    }
    if (name == QLatin1String("pmotor")) {
        m_panel->motorDirective(arg);
        msg = i18n("POLER: моторная директива «%1» отправлена", arg);
        return true;
    }
    if (name == QLatin1String("pharvest")) {
        m_panel->harvestFor(arg);
        msg = i18n("POLER: сбор диска по термам «%1» запущен", arg);
        return true;
    }
    msg = i18n("Неизвестная команда: %1", name);
    return false;
}

bool PolerCommands::help(KTextEditor::View *, const QString &cmd, QString &msg)
{
    const QString name = cmd.section(QLatin1Char(' '), 0, 0).trimmed();
    if (name == QLatin1String("poler")) {
        msg = i18n(":poler <запрос> — топографический поиск POLER по каталогу активного документа; "
                   "многословный запрос = proximity-AND (все токены в окне ±128). Результаты на вкладке «Поиск».");
        return true;
    }
    if (name == QLatin1String("pcrystal")) {
        msg = i18n(":pcrystal <слово> — синапсы кристалла памяти Trit5 (~/.poler/permanent_memory.t5c): "
                   "возбуждающие (+1) и тормозные (−1) связи слова.");
        return true;
    }
    if (name == QLatin1String("pmotor")) {
        msg = i18n(":pmotor <директива> — моторный мост S2→E2 (RU/UA: открой/покажи/запусти/прочитай/собери). "
                   "R1 ReadOnly исполняется автоматически, M2 Mutating — по подтверждению.");
        return true;
    }
    if (name == QLatin1String("pharvest")) {
        msg = i18n(":pharvest <термы> — полнодисковый сборщик poler_disk_harvester: все совпадения терм "
                   "со всех корней в один документ (вкладка «Сбор»).");
        return true;
    }
    return false;
}

#include "moc_polercommands.cpp"

```

---

## File: `integrations/kate-poler/polercommands.h`

- Язык: `c`
- Размер: `1573` байт

```c
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#pragma once

#include <KTextEditor/Command>

#include <QString>
#include <QStringList>

namespace KTextEditor
{
class View;
class Range;
}

class PolerPanel;

/**
 * PolerCommands — консольные команды Kate (появляются в командной строке
 * редактора, vim-режиме и любых местах, где работают команды KTextEditor):
 *
 *   :poler <запрос>     — поиск по каталогу активного документа (вкладка Поиск)
 *   :pcrystal <слово>   — инспекция кристалла памяти Trit5
 *   :pmotor <директива> — моторный мост S2→E2 (RU/UA директивы)
 *   :pharvest <термы>   — полнодисковый сбор в документ
 *
 * Регистрация выполняется автоматически конструктором базового класса
 * KTextEditor::Command(QList<QString>, parent).
 */
class PolerCommands : public KTextEditor::Command
{
    Q_OBJECT

public:
    explicit PolerCommands(PolerPanel *panel, QObject *parent = nullptr);

    bool exec(KTextEditor::View *view,
              const QString &cmd,
              QString &msg,
              const KTextEditor::Range &range = KTextEditor::Range::invalid()) override;
    bool help(KTextEditor::View *view, const QString &cmd, QString &msg) override;

private:
    PolerPanel *m_panel;
};

```

---

## File: `integrations/kate-poler/polerpanel.cpp`

- Язык: `cpp`
- Размер: `22055` байт

```cpp
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#include "polerpanel.h"

#include "enginebridge.h"

#include <KTextEditor/Cursor>
#include <KTextEditor/Document>
#include <KTextEditor/View>

#include <KLocalizedString>

#include <QDir>
#include <QFileDialog>
#include <QFileInfo>
#include <QHBoxLayout>
#include <QJsonArray>
#include <QJsonDocument>
#include <QJsonObject>
#include <QRegularExpression>
#include <QUrl>
#include <QVBoxLayout>

PolerPanel::PolerPanel(KTextEditor::MainWindow *mainWindow, QWidget *parent)
    : QWidget(parent)
    , m_mainWindow(mainWindow)
{
    auto *layout = new QVBoxLayout(this);
    layout->setContentsMargins(0, 0, 0, 0);

    m_tabs = new QTabWidget(this);
    layout->addWidget(m_tabs);

    buildSearchTab();
    buildCrystalTab();
    buildMotorTab();
    buildHarvestTab();

    if (!EngineBridge::available()) {
        m_searchStatus->setText(i18n("⚠ poler-engine не найден — установи в ~/.local/bin или задай $POLER_ENGINE_BIN"));
    }
}

PolerPanel::~PolerPanel() = default;

// ---------------------------------------------------------------------------
// Построение вкладок
// ---------------------------------------------------------------------------

void PolerPanel::buildSearchTab()
{
    m_searchTab = new QWidget(this);
    auto *v = new QVBoxLayout(m_searchTab);

    auto *row1 = new QHBoxLayout();
    m_searchQuery = new QLineEdit(m_searchTab);
    m_searchQuery->setPlaceholderText(i18n("Запрос (слова / фраза; multi-word = proximity-AND)"));
    m_searchQuery->setClearButtonEnabled(true);
    m_searchMode = new QComboBox(m_searchTab);
    m_searchMode->addItem(i18n("Строки (grep)"));
    m_searchMode->addItem(i18n("Сцены (ε, резонанс)"));
    m_searchButton = new QPushButton(i18n("Искать"), m_searchTab);
    row1->addWidget(m_searchQuery, 1);
    row1->addWidget(m_searchMode);
    row1->addWidget(m_searchButton);

    auto *row2 = new QHBoxLayout();
    m_searchPath = new QLineEdit(m_searchTab);
    m_searchPath->setPlaceholderText(i18n("Корень поиска (пусто = каталог активного документа)"));
    m_searchPath->setText(activeDocumentDir());
    auto *browse = new QPushButton(i18n("…"), m_searchTab);
    browse->setFixedWidth(32);
    row2->addWidget(m_searchPath, 1);
    row2->addWidget(browse);

    m_searchResults = new QTreeWidget(m_searchTab);
    m_searchResults->setHeaderLabels({i18n("Файл"), i18n("Строка"), i18n("ε / R"), i18n("Текст / сцена")});
    m_searchResults->setRootIsDecorated(false);
    m_searchResults->setUniformRowHeights(true);
    m_searchResults->setSortingEnabled(true);
    m_searchResults->setColumnWidth(0, 220);
    m_searchResults->setColumnWidth(1, 70);
    m_searchResults->setColumnWidth(2, 90);

    m_searchStatus = new QLabel(m_searchTab);
    m_searchStatus->setWordWrap(true);

    v->addLayout(row1);
    v->addLayout(row2);
    v->addWidget(m_searchResults, 1);
    v->addWidget(m_searchStatus);

    connect(m_searchButton, &QPushButton::clicked, this, &PolerPanel::runSearch);
    connect(m_searchQuery, &QLineEdit::returnPressed, this, &PolerPanel::runSearch);
    connect(browse, &QPushButton::clicked, this, [this] {
        const QString dir = QFileDialog::getExistingDirectory(this, i18n("Корень поиска"), m_searchPath->text());
        if (!dir.isEmpty()) {
            m_searchPath->setText(dir);
        }
    });
    connect(m_searchResults, &QTreeWidget::itemDoubleClicked, this, [this](QTreeWidgetItem *item, int) {
        const QString file = item->data(0, Qt::UserRole).toString();
        const int line = item->data(1, Qt::UserRole).toInt();
        if (!file.isEmpty()) {
            openFileAtLine(file, line > 0 ? line : 1);
        }
    });

    m_tabs->addTab(m_searchTab, QIcon::fromTheme(QStringLiteral("edit-find")), i18n("Поиск"));
}

void PolerPanel::buildCrystalTab()
{
    m_crystalTab = new QWidget(this);
    auto *v = new QVBoxLayout(m_crystalTab);

    auto *row = new QHBoxLayout();
    m_crystalWord = new QLineEdit(m_crystalTab);
    m_crystalWord->setPlaceholderText(i18n("Слово (RU/UA/EN) — синапсы кристалла Trit5"));
    m_crystalWord->setClearButtonEnabled(true);
    m_crystalButton = new QPushButton(i18n("Инспекция"), m_crystalTab);
    row->addWidget(m_crystalWord, 1);
    row->addWidget(m_crystalButton);

    m_crystalOut = new QPlainTextEdit(m_crystalTab);
    m_crystalOut->setReadOnly(true);
    m_crystalOut->setLineWrapMode(QPlainTextEdit::NoWrap);
    QFont mono(QStringLiteral("monospace"));
    mono.setStyleHint(QFont::TypeWriter);
    m_crystalOut->setFont(mono);
    m_crystalOut->setPlaceholderText(i18n("Кристалл ищется в ~/.poler/permanent_memory.t5c\n"
                                          "Синапсы +1 — притяжение, −1 — торможение."));

    v->addLayout(row);
    v->addWidget(m_crystalOut, 1);

    connect(m_crystalButton, &QPushButton::clicked, this, &PolerPanel::runCrystal);
    connect(m_crystalWord, &QLineEdit::returnPressed, this, &PolerPanel::runCrystal);

    m_tabs->addTab(m_crystalTab, QIcon::fromTheme(QStringLiteral("database-index")), i18n("Кристалл"));
}

void PolerPanel::buildMotorTab()
{
    m_motorTab = new QWidget(this);
    auto *v = new QVBoxLayout(m_motorTab);

    auto *row = new QHBoxLayout();
    m_motorDirective = new QLineEdit(m_motorTab);
    m_motorDirective->setPlaceholderText(i18n("Директива RU/UA: «покажи статус git», «открой лог сборки»…"));
    m_motorDirective->setClearButtonEnabled(true);
    m_motorButton = new QPushButton(i18n("Исполнить"), m_motorTab);
    row->addWidget(m_motorDirective, 1);
    row->addWidget(m_motorButton);

    m_motorYes = new QCheckBox(i18n("M2 авто (--motor-yes, мутации без подтверждения)"), m_motorTab);

    m_motorOut = new QPlainTextEdit(m_motorTab);
    m_motorOut->setReadOnly(true);
    {
        QFont mono(QStringLiteral("monospace"));
        mono.setStyleHint(QFont::TypeWriter);
        m_motorOut->setFont(mono);
    }
    m_motorOut->setPlaceholderText(i18n("Моторный мост S2→E2.\n"
                                        "R1 ReadOnly — авто. M2 Mutating — [y/N] (без tty отказ),\n"
                                        "с флагом — авто. Телеметрия: речь + motor_exec события."));

    v->addLayout(row);
    v->addWidget(m_motorYes);
    v->addWidget(m_motorOut, 1);

    connect(m_motorButton, &QPushButton::clicked, this, &PolerPanel::runMotor);
    connect(m_motorDirective, &QLineEdit::returnPressed, this, &PolerPanel::runMotor);

    m_tabs->addTab(m_motorTab, QIcon::fromTheme(QStringLiteral("run-build")), i18n("Мотор"));
}

void PolerPanel::buildHarvestTab()
{
    m_harvestTab = new QWidget(this);
    auto *v = new QVBoxLayout(m_harvestTab);

    m_harvestRoots = new QLineEdit(m_harvestTab);
    m_harvestRoots->setPlaceholderText(i18n("Корни сбора, разделитель «;» (пусто = каталог активного документа)"));
    m_harvestRoots->setText(activeDocumentDir());

    m_harvestTerms = new QLineEdit(m_harvestTab);
    m_harvestTerms->setPlaceholderText(i18n("Термы отбора (OR): гамильтониан hamiltonian ε ∇ …"));

    auto *rowOut = new QHBoxLayout();
    m_harvestOut = new QLineEdit(m_harvestTab);
    m_harvestOut->setPlaceholderText(i18n("Выходной файл (.md / .json / .txt)"));
    m_harvestOut->setText(QDir::home().filePath(QStringLiteral("POLER_HARVEST.md")));
    auto *browse = new QPushButton(i18n("…"), m_harvestTab);
    browse->setFixedWidth(32);
    rowOut->addWidget(m_harvestOut, 1);
    rowOut->addWidget(browse);

    auto *rowCtl = new QHBoxLayout();
    m_harvestFormat = new QComboBox(m_harvestTab);
    m_harvestFormat->addItem(i18n("markdown"));
    m_harvestFormat->addItem(i18n("json"));
    m_harvestFormat->addItem(i18n("corpus (t5c)"));
    m_harvestButton = new QPushButton(i18n("Собрать"), m_harvestTab);
    m_harvestOpenButton = new QPushButton(i18n("Открыть результат"), m_harvestTab);
    m_harvestOpenButton->setEnabled(false);
    rowCtl->addWidget(m_harvestFormat);
    rowCtl->addWidget(m_harvestButton);
    rowCtl->addWidget(m_harvestOpenButton);

    m_harvestLog = new QPlainTextEdit(m_harvestTab);
    m_harvestLog->setReadOnly(true);
    {
        QFont mono(QStringLiteral("monospace"));
        mono.setStyleHint(QFont::TypeWriter);
        m_harvestLog->setFont(mono);
    }
    m_harvestLog->setPlaceholderText(i18n("poler_disk_harvester: SIMD Aho-Corasick + memmap2.\n"
                                          "DoD: 100k файлов < 2.5 c, RSS < 128 МБ. Прогресс — ниже."));

    v->addWidget(m_harvestRoots);
    v->addWidget(m_harvestTerms);
    v->addLayout(rowOut);
    v->addLayout(rowCtl);
    v->addWidget(m_harvestLog, 1);

    connect(m_harvestButton, &QPushButton::clicked, this, &PolerPanel::runHarvest);
    connect(m_harvestTerms, &QLineEdit::returnPressed, this, &PolerPanel::runHarvest);
    connect(browse, &QPushButton::clicked, this, [this] {
        const QString f = QFileDialog::getSaveFileName(this, i18n("Выходной файл"), m_harvestOut->text());
        if (!f.isEmpty()) {
            m_harvestOut->setText(f);
        }
    });
    connect(m_harvestOpenButton, &QPushButton::clicked, this, &PolerPanel::openHarvestResult);

    m_tabs->addTab(m_harvestTab, QIcon::fromTheme(QStringLiteral("folder-open-recent")), i18n("Сбор"));
}

// ---------------------------------------------------------------------------
// Слоты точек входа (команды :poler/…)
// ---------------------------------------------------------------------------

void PolerPanel::searchFor(const QString &query)
{
    showSearchTab();
    m_searchQuery->setText(query);
    runSearch();
}

void PolerPanel::motorDirective(const QString &text)
{
    showMotorTab();
    m_motorDirective->setText(text);
    runMotor();
}

void PolerPanel::harvestFor(const QString &terms)
{
    showHarvestTab();
    m_harvestTerms->setText(terms);
    runHarvest();
}

void PolerPanel::crystalFor(const QString &word)
{
    showCrystalTab();
    m_crystalWord->setText(word);
    runCrystal();
}

void PolerPanel::showSearchTab()
{
    m_tabs->setCurrentWidget(m_searchTab);
}
void PolerPanel::showCrystalTab()
{
    m_tabs->setCurrentWidget(m_crystalTab);
}
void PolerPanel::showMotorTab()
{
    m_tabs->setCurrentWidget(m_motorTab);
}
void PolerPanel::showHarvestTab()
{
    m_tabs->setCurrentWidget(m_harvestTab);
}

// ---------------------------------------------------------------------------
// Вызовы движка
// ---------------------------------------------------------------------------

void PolerPanel::runSearch()
{
    const QString query = m_searchQuery->text().trimmed();
    if (query.isEmpty() || m_bridge.busy()) {
        return;
    }
    QString root = m_searchPath->text().trimmed();
    if (root.isEmpty()) {
        root = activeDocumentDir();
    }
    if (root.isEmpty()) {
        root = QDir::homePath();
    }

    m_searchResults->clear();
    m_searchStatus->setText(i18n("Поиск…"));

    if (m_searchMode->currentIndex() == 0) {
        // grep-json: точные строки с номерами.
        QStringList args{root, QStringLiteral("--grep"), query, QStringLiteral("--grep-json"), QStringLiteral("--grep-i")};
        m_bridge.run(args, [this](int code, const QString &out, const QString &err) {
            if (code == 0 || code == 1) {
                const auto doc = QJsonDocument::fromJson(out.toUtf8());
                const auto groups = doc.object().value(QLatin1String("groups")).toArray();
                int n = 0;
                for (const auto &g : groups) {
                    const auto obj = g.toObject();
                    const QString path = obj.value(QLatin1String("path")).toString();
                    const auto lines = obj.value(QLatin1String("lines")).toArray();
                    for (const auto &l : lines) {
                        const auto lo = l.toObject();
                        if (!lo.value(QLatin1String("matched")).toBool()) {
                            continue;
                        }
                        auto *item = new QTreeWidgetItem(m_searchResults);
                        item->setText(0, QFileInfo(path).fileName());
                        item->setToolTip(0, path);
                        const int lineNo = lo.value(QLatin1String("line_no")).toInt();
                        item->setText(1, QString::number(lineNo));
                        item->setTextAlignment(1, Qt::AlignRight);
                        item->setText(3, lo.value(QLatin1String("text")).toString());
                        item->setData(0, Qt::UserRole, path);
                        item->setData(1, Qt::UserRole, lineNo);
                        ++n;
                    }
                }
                m_searchStatus->setText(code == 0 ? i18n("Найдено строк: %1", n) : i18n("Ничего не найдено"));
            } else {
                m_searchStatus->setText(i18n("Ошибка (код %1): %2", code, err.trimmed()));
            }
        });
    } else {
        // ai-json: сцены с ε и резонансом.
        QStringList args{root, QStringLiteral("-q"), query, QStringLiteral("--format"), QStringLiteral("ai-json")};
        m_bridge.run(args, [this](int code, const QString &out, const QString &err) {
            if (code == 0 || code == 1) {
                const auto doc = QJsonDocument::fromJson(out.toUtf8());
                const auto anchors = doc.object().value(QLatin1String("anchors")).toArray();
                int n = 0;
                for (const auto &a : anchors) {
                    const auto obj = a.toObject();
                    const QString path = obj.value(QLatin1String("file")).toString();
                    const auto scene = obj.value(QLatin1String("scene")).toObject();
                    const QString chapter = scene.value(QLatin1String("chapter")).toString();
                    const double eps = obj.value(QLatin1String("epsilon")).toDouble();
                    const double res = obj.value(QLatin1String("resonance")).toDouble();
                    auto *item = new QTreeWidgetItem(m_searchResults);
                    item->setText(0, QFileInfo(path).fileName());
                    item->setToolTip(0, path);
                    item->setText(1, QStringLiteral("—"));
                    item->setText(2, QStringLiteral("ε=%1 R=%2").arg(eps, 0, 'f', 0).arg(res, 0, 'f', 0));
                    item->setText(3, chapter);
                    item->setData(0, Qt::UserRole, path);
                    // Диапазон строк из главы вида "main [block] (L1857–L1860)".
                    static const QRegularExpression re(QStringLiteral("\\(L(\\d+)"));
                    const auto m = re.match(chapter);
                    item->setData(1, Qt::UserRole, m.hasMatch() ? m.captured(1).toInt() : 0);
                    ++n;
                }
                m_searchStatus->setText(n > 0 ? i18n("Сцен: %1 (двойной клик — перейти)", n) : i18n("Ничего не найдено"));
            } else {
                m_searchStatus->setText(i18n("Ошибка (код %1): %2", code, err.trimmed()));
            }
        });
    }
}

void PolerPanel::runCrystal()
{
    const QString word = m_crystalWord->text().trimmed();
    if (word.isEmpty() || m_bridge.busy()) {
        return;
    }
    m_crystalOut->clear();
    m_crystalOut->setPlainText(i18n("Инспекция «%1»…", word));
    QStringList args{QStringLiteral("--triune-crystal-inspect"), word};
    m_bridge.run(args, [this](int code, const QString &out, const QString &err) {
        if (code == 0) {
            m_crystalOut->setPlainText(out.trimmed());
        } else {
            m_crystalOut->setPlainText(i18n("Ошибка (код %1):\n%2", code, err.trimmed()));
        }
    });
}

void PolerPanel::runMotor()
{
    const QString directive = m_motorDirective->text().trimmed();
    if (directive.isEmpty() || m_bridge.busy()) {
        return;
    }
    m_motorOut->clear();
    m_motorOut->setPlainText(i18n("Директива: «%1» → моторный мост S2→E2…", directive));

    QStringList args{QStringLiteral("--triune-speak"),
                     directive,
                     QStringLiteral("--motor-act"),
                     QStringLiteral("--triune-json"),
                     QStringLiteral("--triune-tokens"),
                     QStringLiteral("24")};
    if (m_motorYes->isChecked()) {
        args << QStringLiteral("--motor-yes");
    }
    m_bridge.run(args, [this](int code, const QString &out, const QString &err) {
        QString report;
        const auto doc = QJsonDocument::fromJson(out.toUtf8());
        const auto speech = doc.object().value(QLatin1String("speech")).toArray();
        for (const auto &s : speech) {
            const auto utt = s.toObject().value(QLatin1String("utterance")).toObject();
            report += QStringLiteral("речь: ") + utt.value(QLatin1String("text")).toString() + QLatin1Char('\n');
        }
        const auto motor = doc.object().value(QLatin1String("motor_exec")).toArray();
        if (!motor.isEmpty()) {
            report += QStringLiteral("\n─ motor_exec ─\n");
            for (const auto &m : motor) {
                const auto ev = m.toObject().value(QLatin1String("event")).toObject();
                report += QStringLiteral("[%1] %2 → %3 : %4\n")
                              .arg(ev.value(QLatin1String("level")).toString(),
                                   ev.value(QLatin1String("verb")).toString(),
                                   ev.value(QLatin1String("program")).toString(),
                                   ev.value(QLatin1String("status")).toString());
            }
        }
        if (report.isEmpty() && !out.trimmed().isEmpty()) {
            report = out.trimmed(); // честный fallback: сырой JSON
        }
        if (!err.trimmed().isEmpty()) {
            report += QStringLiteral("\n─ stderr ─\n") + err.trimmed();
        }
        m_motorOut->setPlainText(report.isEmpty() ? i18n("Пустой ответ (код %1)", code) : report);
    });
}

void PolerPanel::runHarvest()
{
    const QString terms = m_harvestTerms->text().trimmed();
    if (terms.isEmpty() || m_bridge.busy()) {
        return;
    }
    QStringList roots;
    for (const QString &r : m_harvestRoots->text().split(QLatin1Char(';'))) {
        const QString t = r.trimmed();
        if (!t.isEmpty()) {
            roots << t;
        }
    }
    if (roots.isEmpty()) {
        const QString d = activeDocumentDir();
        if (d.isEmpty()) {
            m_harvestLog->setPlainText(i18n("Укажи хотя бы один корень сбора."));
            return;
        }
        roots << d;
    }
    const QString outPath = m_harvestOut->text().trimmed();
    if (outPath.isEmpty()) {
        m_harvestLog->setPlainText(i18n("Укажи выходной файл."));
        return;
    }

    m_harvestLog->clear();
    m_harvestOpenButton->setEnabled(false);
    m_harvestButton->setEnabled(false);

    QStringList args{QStringLiteral("--harvest-disk")};
    args << roots;
    args << QStringLiteral("--harvest-query") << terms << QStringLiteral("--harvest-out") << outPath;
    switch (m_harvestFormat->currentIndex()) {
    case 1:
        args << QStringLiteral("--harvest-format") << QStringLiteral("json");
        break;
    case 2:
        args << QStringLiteral("--harvest-format") << QStringLiteral("corpus");
        break;
    default:
        args << QStringLiteral("--harvest-format") << QStringLiteral("markdown");
        break;
    }

    const QString out = outPath;
    m_bridge.run(
        args,
        [this, out](int code, const QString &, const QString &err) {
            m_harvestButton->setEnabled(true);
            if (code == 0 || code == 1) {
                m_harvestLog->appendPlainText(err.trimmed());
                m_harvestLog->appendPlainText(i18n("\nГотово. Результат: %1", out));
                m_harvestOpenButton->setEnabled(true);
            } else {
                m_harvestLog->appendPlainText(i18n("Ошибка (код %1):\n%2", code, err.trimmed()));
            }
        },
        0,
        [this](const QString &chunk) {
            m_harvestLog->appendPlainText(chunk.trimmed());
        });
}

void PolerPanel::openHarvestResult()
{
    const QString out = m_harvestOut->text().trimmed();
    if (!out.isEmpty() && QFileInfo::exists(out)) {
        openFileAtLine(out, 1);
    }
}

// ---------------------------------------------------------------------------
// Навигация
// ---------------------------------------------------------------------------

void PolerPanel::openFileAtLine(const QString &filePath, int line)
{
    KTextEditor::View *view = m_mainWindow->openUrl(QUrl::fromLocalFile(filePath));
    if (view && line > 0) {
        view->setCursorPosition(KTextEditor::Cursor(line - 1, 0));
    }
}

QString PolerPanel::activeDocumentDir() const
{
    if (KTextEditor::View *view = m_mainWindow->activeView()) {
        if (KTextEditor::Document *doc = view->document()) {
            const QUrl url = doc->url();
            if (url.isLocalFile()) {
                const QString dir = QFileInfo(url.toLocalFile()).absolutePath();
                if (!dir.isEmpty()) {
                    return dir;
                }
            }
        }
    }
    return QString();
}

#include "moc_polerpanel.cpp"

```

---

## File: `integrations/kate-poler/polerpanel.h`

- Язык: `c`
- Размер: `3800` байт

```c
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#pragma once

#include "enginebridge.h"

#include <KTextEditor/MainWindow>

#include <QCheckBox>
#include <QComboBox>
#include <QLabel>
#include <QLineEdit>
#include <QPlainTextEdit>
#include <QPushButton>
#include <QTabWidget>
#include <QTreeWidget>
#include <QWidget>

/**
 * PolerPanel — инструментальная панель плагина, 4 вкладки:
 *
 *  1. Search   — топографический поиск движка по пути (grep-json: точные строки
 *                с переходом; ai-json: сцены с ε/резонансом).
 *  2. Crystal  — инспекция кристалла памяти Trit5 (--triune-crystal-inspect):
 *                возбуждающие/тормозные синапсы слова.
 *  3. Motor    — моторный мост S2→E2: директивы RU/UA (открой/покажи/запусти…)
 *                с контуром R1/M2 и honest-JSON телеметрией.
 *  4. Harvest  — полнодисковый сборщик (--harvest-disk): термы → один документ
 *                (markdown/json/corpus) с живым прогрессом и открытием результата.
 *
 * Панель встраивается через MainWindow::createToolView(...) справа и не меняет
 * ни одного аспекта Kate: всё нативное остаётся нативным.
 */
class PolerPanel : public QWidget
{
    Q_OBJECT

public:
    explicit PolerPanel(KTextEditor::MainWindow *mainWindow, QWidget *parent = nullptr);
    ~PolerPanel() override;

    // Точки входа для команд :poler/:pmotor/:pharvest/:pcrystal.
public Q_SLOTS:
    void searchFor(const QString &query);
    void motorDirective(const QString &text);
    void harvestFor(const QString &terms);
    void crystalFor(const QString &word);

    void showSearchTab();
    void showCrystalTab();
    void showMotorTab();
    void showHarvestTab();

private:
    void buildSearchTab();
    void buildCrystalTab();
    void buildMotorTab();
    void buildHarvestTab();

    void runSearch();
    void runCrystal();
    void runMotor();
    void runHarvest();
    void openHarvestResult();

    void openFileAtLine(const QString &filePath, int line);

    /** Каталог активного документа (для дефолтного корня поиска/сбора). */
    QString activeDocumentDir() const;

    KTextEditor::MainWindow *const m_mainWindow;
    EngineBridge m_bridge;

    QTabWidget *m_tabs = nullptr;

    // Search
    QWidget *m_searchTab = nullptr;
    QLineEdit *m_searchQuery = nullptr;
    QLineEdit *m_searchPath = nullptr;
    QComboBox *m_searchMode = nullptr; // 0 = grep (строки), 1 = сцены (ai-json)
    QPushButton *m_searchButton = nullptr;
    QTreeWidget *m_searchResults = nullptr;
    QLabel *m_searchStatus = nullptr;

    // Crystal
    QWidget *m_crystalTab = nullptr;
    QLineEdit *m_crystalWord = nullptr;
    QPushButton *m_crystalButton = nullptr;
    QPlainTextEdit *m_crystalOut = nullptr;

    // Motor
    QWidget *m_motorTab = nullptr;
    QLineEdit *m_motorDirective = nullptr;
    QCheckBox *m_motorYes = nullptr;
    QPushButton *m_motorButton = nullptr;
    QPlainTextEdit *m_motorOut = nullptr;

    // Harvest
    QWidget *m_harvestTab = nullptr;
    QLineEdit *m_harvestRoots = nullptr;
    QLineEdit *m_harvestTerms = nullptr;
    QLineEdit *m_harvestOut = nullptr;
    QComboBox *m_harvestFormat = nullptr;
    QPushButton *m_harvestButton = nullptr;
    QPushButton *m_harvestOpenButton = nullptr;
    QPlainTextEdit *m_harvestLog = nullptr;
};

```

---

## File: `integrations/kate-poler/polerplugin.cpp`

- Язык: `cpp`
- Размер: `2760` байт

```cpp
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#include "polerplugin.h"

#include "polercommands.h"
#include "polerpanel.h"

#include <KLocalizedString>
#include <KPluginFactory>

#include <QEvent>
#include <QIcon>
#include <QKeyEvent>

K_PLUGIN_FACTORY_WITH_JSON(PolerPluginFactory, "polerplugin.json", registerPlugin<PolerPlugin>();)

// ---------------------------------------------------------------------------
// PolerPlugin
// ---------------------------------------------------------------------------

PolerPlugin::PolerPlugin(QObject *parent)
    : KTextEditor::Plugin(parent)
{
}

PolerPlugin::~PolerPlugin() = default;

QObject *PolerPlugin::createView(KTextEditor::MainWindow *mainWindow)
{
    auto *view = new PolerPluginView(this, mainWindow);
    connect(view, &PolerPluginView::destroyed, this, &PolerPlugin::viewDestroyed);
    m_views.append(view);
    return view;
}

void PolerPlugin::viewDestroyed(QObject *view)
{
    // Не разыменовывать view — он уже частично разрушен.
    m_views.removeAll(view);
}

int PolerPlugin::configPages() const
{
    return 0;
}

KTextEditor::ConfigPage *PolerPlugin::configPage(int, QWidget *)
{
    return nullptr;
}

// ---------------------------------------------------------------------------
// PolerPluginView
// ---------------------------------------------------------------------------

PolerPluginView::PolerPluginView(KTextEditor::Plugin *plugin, KTextEditor::MainWindow *mainWindow)
    : QObject(mainWindow)
    , m_toolView(mainWindow->createToolView(plugin,
                                            QStringLiteral("kate_private_plugin_polerengineplugin"),
                                            KTextEditor::MainWindow::Right,
                                            QIcon::fromTheme(QStringLiteral("edit-find")),
                                            i18n("POLER Engine")))
    , m_panel(new PolerPanel(mainWindow, m_toolView))
    , m_mainWindow(mainWindow)
    , m_commands(new PolerCommands(m_panel, this))
{
    m_toolView->installEventFilter(this);
}

PolerPluginView::~PolerPluginView()
{
    // Уничтожаем toolview (вместе с панелью-потомком).
    delete m_panel->parent();
}

bool PolerPluginView::eventFilter(QObject *obj, QEvent *event)
{
    if (event->type() == QEvent::KeyPress) {
        auto *ke = static_cast<QKeyEvent *>(event);
        if ((obj == m_toolView) && (ke->key() == Qt::Key_Escape)) {
            m_mainWindow->hideToolView(m_toolView);
            event->accept();
            return true;
        }
    }
    return QObject::eventFilter(obj, event);
}

#include "polerplugin.moc"
#include "moc_polerplugin.cpp"


```

---

## File: `integrations/kate-poler/polerplugin.h`

- Язык: `c`
- Размер: `2022` байт

```c
/*
    SPDX-FileCopyrightText: 2026 POLER Engine Org
    SPDX-License-Identifier: LGPL-2.0-or-later
*/
#pragma once

#include <KTextEditor/MainWindow>
#include <KTextEditor/Plugin>
#include <ktexteditor/configpage.h>

class PolerPanel;
class PolerPluginView;
class PolerCommands;

/**
 * PolerPlugin — точка входа KTextEditor-плагина «POLER Engine».
 *
 * Kate остаётся Kate: плагин только добавляет инструментальную панель
 * (MainWindow::createToolView, правая сторона) и консольные команды
 * :poler/:pcrystal/:pmotor/:pharvest. Ни один нативный аспект редактора
 * не заменяется и не перехватывается — вся мощь Kate сохранена на 100%,
 * а суверенное ядро poler-engine встраивается рядом как равноправный
 * орган: поиск, кристалл памяти, моторный мост, дисковый сборщик.
 */
class PolerPlugin : public KTextEditor::Plugin
{
public:
    explicit PolerPlugin(QObject *parent = nullptr);
    ~PolerPlugin() override;

    QObject *createView(KTextEditor::MainWindow *mainWindow) override;

    int configPages() const override;
    KTextEditor::ConfigPage *configPage(int number = 0, QWidget *parent = nullptr) override;

public:
    void viewDestroyed(QObject *view);

private:
    QList<PolerPluginView *> m_views;
};

class PolerPluginView : public QObject
{
    Q_OBJECT

public:
    PolerPluginView(KTextEditor::Plugin *plugin, KTextEditor::MainWindow *mainWindow);
    ~PolerPluginView() override;

    PolerPanel *panel() const
    {
        return m_panel;
    }

private:
    bool eventFilter(QObject *obj, QEvent *event) override;

    QWidget *m_toolView = nullptr;
    PolerPanel *m_panel = nullptr;
    KTextEditor::MainWindow *m_mainWindow = nullptr;
    PolerCommands *m_commands = nullptr;

    friend class PolerPlugin;
};

```

---

## File: `integrations/kate-poler/polerplugin.json`

- Язык: `json`
- Размер: `884` байт

```json
{
    "KPlugin": {
        "Id": "polerengineplugin",
        "Name": "POLER Engine",
        "Name[ru]": "Движок POLER",
        "Name[uk]": "Рушій POLER",
        "Description": "Sovereign search, memory crystal, motor bridge and disk harvester inside Kate",
        "Description[ru]": "Суверенный поиск, кристалл памяти, моторный мост и дисковый сборщик внутри Kate",
        "Description[uk]": "Суверенний пошук, кристал пам'яті, моторний міст та дисковий збирач усередині Kate",
        "Version": "0.1.0",
        "License": "LGPL-2.0-or-later",
        "Category": "Editor",
        "Authors": [
            {
                "Name": "POLER Engine Org",
                "Email": "vitalijkotok18@gmail.com"
            }
        ]
    }
}

```

---

## File: `integrations/poler-edit-qt/README.md`

- Язык: `markdown`
- Размер: `3193` байт

```markdown
# poler-edit-qt — GUI-клиент POLER Editor

Kate-подобный интерфейс поверх суверенного ядра `poler-edit`
(`poler-engine --edit-serve`). Зависимости: **только Qt6 Widgets** —
никаких KF6/KParts/Electron. Вся работа с текстом любого размера
происходит в ядре; GUI рендерит только видимое окно строк.

## Сборка (Arch Linux)

```bash
sudo pacman -S --needed base-devel cmake qt6-base
cd integrations/poler-edit-qt
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr
cmake --build build -j$(nproc)
./build/poler-edit            # без установки
sudo cmake --install build    # системно: /usr/bin/poler-edit
```

Требование: `poler-engine` (v0.38.0+) в `PATH` (у пользователя он в
`~/.local/bin`). Переопределить путь можно переменной `POLER_ENGINE=/путь`.

## Запуск

```bash
poler-edit file.txt huge.log another.md   # несколько вкладок
poler-edit --light                        # светлая тема (по умолчанию тёмная, Breeze-стиль)
```

## Управление

| Действие | Клавиши |
|---|---|
| Открыть / Сохранить | Ctrl+O / Ctrl+S |
| Отмена / Возврат | Ctrl+Z / Ctrl+Y |
| Копировать / Вырезать / Вставить | Ctrl+C / Ctrl+X / Ctrl+V |
| Выделить всё | Ctrl+A |
| Найти (SIMD, весь файл любого размера) | Ctrl+F, далее Enter/F3 |
| vi-командная строка | `:` или Ctrl+: |
| Сохранить и закрыть | `:w` `:q` `:wq` `:q!` |
| Перейти к строке N | `:123` |
| Масштаб | Ctrl+колесо, Ctrl+= / Ctrl+- |
| Скрыть поиск / командную строку | Esc |

Во время фоновой индексации строк в статусной строке видна живая
скорость SIMD-подсчёта (GiB/s) — индексация не блокирует редактирование:
вьюпорт и правки доступны сразу после открытия.

## Архитектура

```
poler-edit (Qt6, тонкий клиент)
   │  JSON lines over stdio (LSP-стиль)
   ▼
poler-engine --edit-serve
   │  zero-copy mmap piece-table + SIMD line index + Aho-Corasick
   ▼
файл любого размера (RAM не зависит от объёма)
```

Протокол и цифры ядра: `docs/POLER_EDIT.md`.

## Ограничения v1 (честно)

- Подсветка синтаксиса — в планах (KSyntaxHighlighting XML / tree-sitter).
- save_as из GUI сохраняет в текущий путь (команда `save_as` в протоколе уже есть).
- Сложный IME-ввод (CJK) не тестировался; кириллица/латиница работают.
- Замену файла на диске во время редактирования ядро пока не отслеживает.

```

---

## File: `integrations/poler-edit-qt/src/EditorView.cpp`

- Язык: `cpp`
- Размер: `27668` байт

```cpp
#include "EditorView.h"

#include <QApplication>
#include <QClipboard>
#include <QFileInfo>
#include <QFontDatabase>
#include <QGuiApplication>
#include <QJsonArray>
#include <QKeyEvent>
#include <QPainter>
#include <QScrollBar>
#include <QtMath>

static constexpr int kOverscan = 40;      // строк сверх видимого окна
static constexpr int kBlinkMs = 530;
static constexpr int kGutterPad = 12;
// Пока line-index не готов, высота скроллбара оценивается по плотности строк.
static constexpr double kEstBytesPerLine = 96.0;

EditorView::EditorView(EngineBridge *bridge, const QString &path, QWidget *parent)
    : QAbstractScrollArea(parent)
    , m_bridge(bridge)
    , m_path(path)
{
    m_font = QFontDatabase::systemFont(QFontDatabase::FixedFont);
    m_font.setStyleHint(QFont::Monospace);
    if (m_font.pointSize() <= 0) {
        m_font.setPointSize(10);
    }
    m_baseSize = m_font.pointSize();
    setFont(m_font);

    setFocusPolicy(Qt::StrongFocus);
    viewport()->setCursor(Qt::IBeamCursor);
    setFrameShape(QFrame::NoFrame);
    viewport()->setAttribute(Qt::WA_OpaquePaintEvent);

    m_blink.setInterval(kBlinkMs);
    connect(&m_blink, &QTimer::timeout, this, &EditorView::blinkCursor);
    m_blink.start();

    connect(m_bridge, &EngineBridge::progressEvent, this, &EditorView::onProgress);

    // Скроллбары: коннект ОДИН раз (UniqueConnection не работает с лямбдами).
    connect(verticalScrollBar(), &QScrollBar::valueChanged, this, [this](int) {
        requestFetch(topLine());
        viewport()->update();
    });
    connect(horizontalScrollBar(), &QScrollBar::valueChanged, this, [this](int) {
        viewport()->update();
    });

    horizontalScrollBar()->setSingleStep(charWidth());
    openDoc();
}

QString EditorView::fileName() const
{
    if (m_path.isEmpty()) {
        return tr("без назви");
    }
    return QFileInfo(m_path).fileName();
}

// ---------------- геометрия ----------------

int EditorView::lineHeight() const
{
    return fontMetrics().height();
}

int EditorView::charWidth() const
{
    return fontMetrics().horizontalAdvance(QLatin1Char('M'));
}

int EditorView::gutterWidth() const
{
    const qlonglong maxLine = m_linesTotal > 0 ? m_linesTotal : qMax<qlonglong>(topLine() + visibleLines() + 1, 100);
    const int digits = qMax(2, int(qLn(qreal(qMax<qlonglong>(maxLine, 1)))) + 1);
    return digits * charWidth() + kGutterPad * 2;
}

int EditorView::visibleLines() const
{
    return qMax(1, viewport()->height() / lineHeight());
}

qlonglong EditorView::topLine() const
{
    return verticalScrollBar()->value();
}

void EditorView::setTopLine(qlonglong line)
{
    verticalScrollBar()->setValue(int(line));
}

// ---------------- кэш окна ----------------

QString EditorView::lineText(qlonglong line) const
{
    const qlonglong idx = line - m_blockTop;
    if (idx >= 0 && idx < m_lines.size()) {
        return m_lines.at(int(idx));
    }
    return QString();
}

qlonglong EditorView::lineLen(qlonglong line) const
{
    return lineText(line).length();
}

void EditorView::requestFetch(qlonglong aroundLine)
{
    fetchRange(qMax<qlonglong>(aroundLine - kOverscan / 2, 0),
               visibleLines() + kOverscan);
}

void EditorView::fetchRange(qlonglong top, int count)
{
    if (m_doc == 0) {
        return;
    }
    const qlonglong gen = ++m_fetchGen;
    m_bridge->viewport(m_doc, top, count, [this, gen, top](const QJsonObject &r) {
        if (gen != m_fetchGen || !r.value("ok").toBool()) {
            return; // пришёл устаревший запрос — молча отбрасываем
        }
        const QJsonArray arr = r.value("lines").toArray();
        m_lines.clear();
        m_lines.reserve(arr.size());
        for (const QJsonValue &v : arr) {
            m_lines.append(v.toObject().value("text").toString());
        }
        m_blockTop = top;
        if (gen > m_appliedGen) {
            m_appliedGen = gen;
        }
        viewport()->update();
    });
}

// ---------------- открытие ----------------

void EditorView::openDoc()
{
    m_bridge->open(m_path, [this](const QJsonObject &r) {
        if (!r.value("ok").toBool()) {
            emit statusMessage(tr("Не вдалося відкрити: %1")
                                   .arg(r.value("error").toString()), 6000);
            emit requestClose(this);
            return;
        }
        m_doc = r.value("doc").toVariant().toLongLong();
        m_bytesTotal = r.value("bytes").toVariant().toLongLong();
        const QJsonValue lines = r.value("lines");
        if (lines.isDouble()) {
            m_linesTotal = lines.toVariant().toLongLong();
        } else {
            // ленивое ядро: строк ещё не знаем — индексируем в фоне,
            // вьюпорт уже можно показывать (верх файла мгновенно готов).
            m_linesTotal = 0;
            m_indexing = true;
            m_bridge->indexDoc(m_doc, [this](const QJsonObject &r) {
                m_indexing = false;
                if (r.value("ok").toBool() && r.value("completed").toBool()) {
                    m_linesTotal = r.value("lines").toVariant().toLongLong();
                    updateScrollbars();
                    viewport()->update();
                    emit statusMessage(
                        tr("Індексація завершена: %1 рядків").arg(m_linesTotal), 4000);
                }
            });
        }
        updateScrollbars();
        setTopLine(0);
        requestFetch(0);
        emit docOpened(this);
    });
}

void EditorView::refreshInfo()
{
    if (m_doc == 0) {
        return;
    }
    // stats не нужен отдельной командой — байты приходят из правок
}

void EditorView::onProgress(const QJsonObject &obj)
{
    if (obj.value("doc").toVariant().toLongLong() != m_doc
        || obj.value("op").toString() != QLatin1String("index")) {
        return;
    }
    if (!m_indexing) {
        return;
    }
    const double done = obj.value("done_bytes").toDouble();
    const double total = obj.value("total_bytes").toDouble();
    const double gbps = obj.value("gbps").toDouble();
    auto human = [](double b) {
        if (b >= (1ull << 30)) return QString::number(b / (1ull << 30), 'f', 1) + " GiB";
        if (b >= (1ull << 20)) return QString::number(b / (1ull << 20), 'f', 0) + " MiB";
        return QString::number(b / (1ull << 10), 'f', 0) + " KiB";
    };
    emit statusMessage(tr("Індексація SIMD: %1 / %2 (%3 GiB/s)")
                           .arg(human(done), human(total))
                           .arg(gbps, 0, 'f', 1),
                       0);
}

// ---------------- скроллбары ----------------

void EditorView::updateScrollbars()
{
    qlonglong range;
    if (m_linesTotal > 0) {
        range = m_linesTotal;
    } else if (m_bytesTotal > 0) {
        range = qMax<qlonglong>(m_bytesTotal / kEstBytesPerLine, visibleLines());
    } else {
        range = 1;
    }
    QScrollBar *v = verticalScrollBar();
    v->setRange(0, int(qMax<qlonglong>(range - visibleLines() + 1, 1)));
    v->setPageStep(visibleLines());
    v->setSingleStep(1);
    horizontalScrollBar()->setRange(0, 4000);
    viewport()->update();
}

// ---------------- рендер ----------------

void EditorView::paintEvent(QPaintEvent *)
{
    QPainter p(viewport());
    const int lh = lineHeight();
    const int cw = charWidth();
    const int gw = gutterWidth();
    const int hscroll = horizontalScrollBar()->value() * cw;
    const int w = viewport()->width();
    const int h = viewport()->height();
    const int n = visibleLines() + 1;

    // Палитра (учитывает тёмную тему приложения)
    const QPalette pal = this->palette();
    const QColor bg = pal.color(QPalette::Base);
    const QColor fg = pal.color(QPalette::Text);
    const QColor gutter = pal.color(QPalette::PlaceholderText);
    const QColor curLineBg = pal.color(QPalette::AlternateBase);
    const QColor selBg = pal.color(QPalette::Highlight);

    p.fillRect(0, 0, w, h, bg);

    // gutter-фон чуть темнее
    p.fillRect(0, 0, gw, h, pal.color(QPalette::Window));

    const Pos selStart = qMin(m_anchor, m_cursor);
    const Pos selEnd = qMax(m_anchor, m_cursor);

    for (int i = 0; i < n; ++i) {
        const qlonglong lineNo = topLine() + i;
        if (m_linesTotal > 0 && lineNo >= m_linesTotal) {
            break;
        }
        const int y = i * lh;
        if (y > h) {
            break;
        }
        const QString text = lineText(lineNo);

        // подсветка текущей строки
        if (lineNo == m_cursor.line) {
            p.fillRect(gw, y, w - gw, lh, curLineBg);
        }
        // выделение
        if (hasSelection() && lineNo >= selStart.line && lineNo <= selEnd.line) {
            int fromCol = 0;
            int toCol = text.length();
            if (lineNo == selStart.line) {
                fromCol = int(qMin<qlonglong>(selStart.col, toCol));
            }
            if (lineNo == selEnd.line) {
                toCol = int(qMin<qlonglong>(selEnd.col, toCol));
            }
            if (toCol > fromCol || selStart.line != selEnd.line) {
                const int x0 = gw + fromCol * cw - hscroll;
                const int x1 = lineNo == selEnd.line ? gw + toCol * cw - hscroll : w;
                p.fillRect(qMax(x0, gw), y + 1,
                           qMax(x1, gw + cw) - qMax(x0, gw), lh - 2, selBg);
            }
        }

        // номер строки
        p.setPen(gutter);
        const QString num = QString::number(lineNo + 1);
        p.drawText(QRect(0, y, gw - kGutterPad, lh),
                   Qt::AlignRight | Qt::AlignVCenter, num);

        // текст
        p.setPen(fg);
        if (!text.isEmpty()) {
            const int skip = hscroll / cw;
            if (skip < text.length()) {
                p.drawText(QPoint(gw - (hscroll % cw),
                                  y + fontMetrics().ascent() + (lh - fontMetrics().height()) / 2),
                           text.mid(skip, w / cw + 2));
            }
        } else if (lineNo == m_cursor.line && !hasSelection()) {
            // пустая строка с курсором — рисуем и так (ниже)
        }
        if (lineNo == m_cursor.line && m_cursorVisible && hasFocus()) {
            const int cx = gw + int(m_cursor.col) * cw - hscroll;
            if (cx >= gw - 2) {
                p.fillRect(cx, y + 1, qMax(1, cw / 8), lh - 2, fg);
            }
        }
    }
    if (m_lines.isEmpty() && m_doc != 0) {
        p.setPen(gutter);
        p.drawText(QRect(gw, 0, w - gw, h), Qt::AlignCenter,
                   m_indexing ? tr("Індексація…") : tr("Завантаження…"));
    }
    p.end();
}

// ---------------- курсор ----------------

EditorView::Pos EditorView::clampPos(const Pos &p) const
{
    Pos r = p;
    if (m_linesTotal > 0) {
        r.line = qBound<qlonglong>(0, r.line, m_linesTotal - 1);
    } else {
        r.line = qMax<qlonglong>(r.line, 0);
    }
    const qlonglong len = lineLen(r.line);
    r.col = qBound<qlonglong>(0, r.col, len);
    return r;
}

void EditorView::setCursor(const Pos &p, bool keepAnchor)
{
    const Pos np = clampPos(p);
    if (!keepAnchor) {
        m_anchor = np;
    }
    m_cursor = np;
    m_cursorVisible = true;
    m_blink.start();
    ensureCursorVisible();
    emit cursorMoved(np.line + 1, np.col + 1);
    viewport()->update();
}

void EditorView::ensureCursorVisible()
{
    const qlonglong top = topLine();
    const int vis = visibleLines();
    if (m_cursor.line < top) {
        setTopLine(m_cursor.line);
        requestFetch(m_cursor.line);
    } else if (m_cursor.line >= top + vis - 1) {
        setTopLine(m_cursor.line - vis + 2);
        requestFetch(topLine());
    }
    const int cw = charWidth();
    const int gw = gutterWidth();
    const int cx = gw + int(m_cursor.col) * cw - horizontalScrollBar()->value() * cw;
    if (cx < gw) {
        horizontalScrollBar()->setValue(horizontalScrollBar()->value()
                                        - (gw - cx) / cw - 1);
    } else if (cx > viewport()->width() - cw * 4) {
        horizontalScrollBar()->setValue(horizontalScrollBar()->value()
                                        + (cx - viewport()->width()) / cw + 4);
    }
}

// ---------------- правки ----------------

void EditorView::insertText(const QString &text)
{
    if (m_doc == 0 || text.isEmpty()) {
        return;
    }
    const Pos at = clampPos(m_cursor);
    m_bridge->insertAt(m_doc, at.line, at.col, text, [this](const QJsonObject &) {
        afterEdit();
    });
    // Оптимистичный ход курсора: отзывчивость как у локального редактора.
    int nl = 0;
    int lastNl = -1;
    for (int i = 0; i < text.size(); ++i) {
        if (text.at(i) == QLatin1Char('\n')) {
            ++nl;
            lastNl = i;
        }
    }
    Pos np;
    if (nl > 0) {
        np.line = at.line + nl;
        np.col = text.size() - lastNl - 1;
    } else {
        np.line = at.line;
        np.col = at.col + text.size();
    }
    m_anchor = np;
    m_cursor = np;
    ensureCursorVisible();
    if (!m_modified) {
        m_modified = true;
        emit docModified(this, true);
    }
    viewport()->update();
}

void EditorView::afterEdit()
{
    requestFetch(m_cursor.line);
    viewport()->update();
}

void EditorView::deleteSelection()
{
    if (!hasSelection() || m_doc == 0) {
        return;
    }
    const Pos s = qMin(m_anchor, m_cursor);
    const Pos e = qMax(m_anchor, m_cursor);
    m_bridge->del(m_doc, s.line, s.col, e.line, e.col, [this](const QJsonObject &) {
        afterEdit();
    });
    m_anchor = s;
    m_cursor = s;
    ensureCursorVisible();
    if (!m_modified) {
        m_modified = true;
        emit docModified(this, true);
    }
    viewport()->update();
}

void EditorView::undo()
{
    if (m_doc == 0) {
        return;
    }
    m_bridge->undo(m_doc, [this](const QJsonObject &r) {
        if (r.value("applied").toBool()) {
            m_modified = true;
            emit docModified(this, true);
        }
        requestFetch(m_cursor.line);
        viewport()->update();
    });
}

void EditorView::redo()
{
    if (m_doc == 0) {
        return;
    }
    m_bridge->redo(m_doc, [this](const QJsonObject &r) {
        if (r.value("applied").toBool()) {
            m_modified = true;
            emit docModified(this, true);
        }
        requestFetch(m_cursor.line);
        viewport()->update();
    });
}

// ---------------- буфер обмена / выделение ----------------

QString EditorView::selectedText() const
{
    if (!hasSelection()) {
        return QString();
    }
    const Pos s = qMin(m_anchor, m_cursor);
    const Pos e = qMax(m_anchor, m_cursor);
    QString out;
    for (qlonglong l = s.line; l <= e.line; ++l) {
        const QString text = lineText(l);
        int a = 0;
        int b = text.length();
        if (l == s.line) {
            a = int(qMin<qlonglong>(s.col, b));
        }
        if (l == e.line) {
            b = int(qMin<qlonglong>(e.col, b));
        }
        out += text.mid(a, qMax(0, b - a));
        if (l < e.line) {
            out += QLatin1Char('\n');
        }
    }
    return out;
}

void EditorView::copy()
{
    const QString sel = selectedText();
    if (!sel.isEmpty()) {
        QGuiApplication::clipboard()->setText(sel);
        emit statusMessage(tr("Скопійовано %n симв.", nullptr, sel.size()), 2000);
    }
}

void EditorView::cut()
{
    if (hasSelection()) {
        copy();
        deleteSelection();
    }
}

void EditorView::paste()
{
    const QString text = QGuiApplication::clipboard()->text();
    if (!text.isEmpty()) {
        if (hasSelection()) {
            deleteSelection();
        }
        insertText(text);
    }
}

void EditorView::selectAll()
{
    m_anchor = Pos{0, 0};
    const qlonglong last = m_linesTotal > 0 ? m_linesTotal - 1 : topLine() + visibleLines();
    m_cursor = Pos{last, lineLen(last)};
    ensureCursorVisible();
    viewport()->update();
}

// ---------------- клавиатура ----------------

void EditorView::keyPressEvent(QKeyEvent *e)
{
    const Qt::KeyboardModifiers mod = e->modifiers();
    const bool shift = mod & Qt::ShiftModifier;
    const bool ctrl = mod & Qt::ControlModifier;

    if (e->key() == Qt::Key_Escape) {
        m_anchor = m_cursor;
        viewport()->update();
        return;
    }
    // vi-стиль: ':' открывает командную строку (text() надёжнее key():
    // на многих раскладках двоеточие требует Shift).
    if (!ctrl && !(mod & Qt::AltModifier) && e->text() == QStringLiteral(":")) {
        emit commandLineRequested();
        return;
    }

    switch (e->key()) {
    case Qt::Key_Up:
        setCursor(Pos{m_cursor.line - 1, m_cursor.col}, !shift);
        return;
    case Qt::Key_Down:
        setCursor(Pos{m_cursor.line + 1, m_cursor.col}, !shift);
        return;
    case Qt::Key_Left:
        if (m_cursor.col > 0) {
            setCursor(Pos{m_cursor.line, m_cursor.col - 1}, !shift);
        } else if (m_cursor.line > 0) {
            setCursor(Pos{m_cursor.line - 1, lineLen(m_cursor.line - 1)}, !shift);
        }
        return;
    case Qt::Key_Right:
        if (m_cursor.col < lineLen(m_cursor.line)) {
            setCursor(Pos{m_cursor.line, m_cursor.col + 1}, !shift);
        } else {
            setCursor(Pos{m_cursor.line + 1, 0}, !shift);
        }
        return;
    case Qt::Key_Home:
        setCursor(Pos{m_cursor.line, 0}, !shift);
        return;
    case Qt::Key_End:
        setCursor(Pos{m_cursor.line, lineLen(m_cursor.line)}, !shift);
        return;
    case Qt::Key_PageUp:
        setCursor(Pos{m_cursor.line - visibleLines() + 1, m_cursor.col}, !shift);
        return;
    case Qt::Key_PageDown:
        setCursor(Pos{m_cursor.line + visibleLines() - 1, m_cursor.col}, !shift);
        return;
    case Qt::Key_Return:
    case Qt::Key_Enter:
        if (hasSelection()) {
            deleteSelection();
        }
        insertText(QStringLiteral("\n"));
        return;
    case Qt::Key_Backspace:
        if (hasSelection()) {
            deleteSelection();
        } else if (m_cursor.col > 0) {
            const Pos s{m_cursor.line, m_cursor.col - 1};
            m_bridge->del(m_doc, s.line, s.col, m_cursor.line, m_cursor.col,
                          [this](const QJsonObject &) { afterEdit(); });
            m_anchor = s;
            m_cursor = s;
            m_modified = true;
            emit docModified(this, true);
            ensureCursorVisible();
            viewport()->update();
        } else if (m_cursor.line > 0) {
            const qlonglong prevLen = lineLen(m_cursor.line - 1);
            m_bridge->del(m_doc, m_cursor.line - 1, prevLen, m_cursor.line, 0,
                          [this](const QJsonObject &) { afterEdit(); });
            const Pos s{m_cursor.line - 1, prevLen};
            m_anchor = s;
            m_cursor = s;
            m_modified = true;
            emit docModified(this, true);
            ensureCursorVisible();
            viewport()->update();
        }
        return;
    case Qt::Key_Delete:
        if (hasSelection()) {
            deleteSelection();
        } else if (m_cursor.col < lineLen(m_cursor.line)) {
            m_bridge->del(m_doc, m_cursor.line, m_cursor.col,
                          m_cursor.line, m_cursor.col + 1,
                          [this](const QJsonObject &) { afterEdit(); });
            m_modified = true;
            emit docModified(this, true);
            viewport()->update();
        } else {
            // Join со следующей строкой (удаляем \n)
            m_bridge->del(m_doc, m_cursor.line, m_cursor.col,
                          m_cursor.line + 1, 0,
                          [this](const QJsonObject &) { afterEdit(); });
            m_modified = true;
            emit docModified(this, true);
            viewport()->update();
        }
        return;
    case Qt::Key_Tab:
        if (hasSelection()) {
            deleteSelection();
        }
        insertText(QStringLiteral("    "));
        return;
    default:
        break;
    }

    if (ctrl) {
        QAbstractScrollArea::keyPressEvent(e);
        return;
    }

    const QString text = e->text();
    if (!text.isEmpty() && !text.contains(QLatin1Char('\r'))) {
        if (hasSelection()) {
            deleteSelection();
        }
        insertText(text);
        return;
    }
    QAbstractScrollArea::keyPressEvent(e);
}

bool EditorView::event(QEvent *e)
{
    if (e->type() == QEvent::ToolTip) {
        return QAbstractScrollArea::event(e);
    }
    return QAbstractScrollArea::event(e);
}

// ---------------- мышь ----------------

void EditorView::mousePressEvent(QMouseEvent *e)
{
    if (e->button() != Qt::LeftButton) {
        QAbstractScrollArea::mousePressEvent(e);
        return;
    }
    setFocus();
    const int lh = lineHeight();
    const int cw = charWidth();
    const int gw = gutterWidth();
    const qlonglong line = topLine() + e->pos().y() / lh;
    const qlonglong col = qMax<qlonglong>(0, (e->pos().x() - gw + horizontalScrollBar()->value() * cw) / cw);
    setCursor(Pos{line, col}, e->modifiers() & Qt::ShiftModifier);
}

void EditorView::mouseMoveEvent(QMouseEvent *e)
{
    if (e->buttons() & Qt::LeftButton) {
        const int lh = lineHeight();
        const int cw = charWidth();
        const int gw = gutterWidth();
        const qlonglong line = topLine() + e->pos().y() / lh;
        const qlonglong col = qMax<qlonglong>(0, (e->pos().x() - gw + horizontalScrollBar()->value() * cw) / cw);
        // keepAnchor = true для непрерывного выделения фрагментов мышью
        setCursor(Pos{line, col}, true);
    } else {
        QAbstractScrollArea::mouseMoveEvent(e);
    }
}

void EditorView::mouseReleaseEvent(QMouseEvent *e)
{
    if (e->button() == Qt::LeftButton) {
        // Завершение выделения мышью
        viewport()->update();
    } else {
        QAbstractScrollArea::mouseReleaseEvent(e);
    }
}

void EditorView::mouseDoubleClickEvent(QMouseEvent *e)
{
    // v1: двойной клик выделяет слово (по пробелам/знакам)
    const int cw = charWidth();
    const int gw = gutterWidth();
    const qlonglong line = topLine() + e->pos().y() / lineHeight();
    const qlonglong col = qMax<qlonglong>(0, (e->pos().x() - gw + horizontalScrollBar()->value() * cw) / cw);
    const QString text = lineText(line);
    if (text.isEmpty()) {
        return;
    }
    const int c = int(qMin<qlonglong>(col, text.length() - 1));
    const auto isWord = [](QChar ch) {
        return ch.isLetterOrNumber() || ch == QLatin1Char('_');
    };
    int a = c;
    int b = c;
    while (a > 0 && isWord(text.at(a - 1))) {
        --a;
    }
    while (b < text.length() - 1 && isWord(text.at(b + 1))) {
        ++b;
    }
    m_anchor = Pos{line, a};
    m_cursor = Pos{line, b + 1};
    viewport()->update();
}

void EditorView::wheelEvent(QWheelEvent *e)
{
    const int steps = e->angleDelta().y() / 120;
    if (e->modifiers() & Qt::ControlModifier) {
        if (steps > 0) {
            zoomIn();
        } else {
            zoomOut();
        }
        return;
    }
    verticalScrollBar()->setValue(verticalScrollBar()->value() - steps * 3);
    requestFetch(topLine());
    viewport()->update();
}

void EditorView::resizeEvent(QResizeEvent *e)
{
    QAbstractScrollArea::resizeEvent(e);
    updateScrollbars();
    requestFetch(topLine());
}

void EditorView::focusInEvent(QFocusEvent *e)
{
    QAbstractScrollArea::focusInEvent(e);
    m_cursorVisible = true;
    m_blink.start();
    viewport()->update();
}

void EditorView::blinkCursor()
{
    if (hasFocus()) {
        m_cursorVisible = !m_cursorVisible;
        viewport()->update();
    }
}

void EditorView::zoomIn()
{
    m_zoom = qMin(8, m_zoom + 1);
    m_font.setPointSize(qMax(6, m_baseSize + m_zoom));
    setFont(m_font);
    updateScrollbars();
    requestFetch(topLine());
}

void EditorView::zoomOut()
{
    m_zoom = qMax(-6, m_zoom - 1);
    m_font.setPointSize(qMax(6, m_baseSize + m_zoom));
    setFont(m_font);
    updateScrollbars();
    requestFetch(topLine());
}

// ---------------- поиск ----------------

void EditorView::search(const QString &query, bool caseSensitive)
{
    if (m_doc == 0 || query.isEmpty()) {
        return;
    }
    m_lastQuery = query;
    emit statusMessage(tr("Пошук «%1»…").arg(query), 0);
    m_bridge->search(m_doc, query, caseSensitive, 5000, [this, query](const QJsonObject &r) {
        if (!r.value("ok").toBool()) {
            emit statusMessage(tr("Помилка пошуку: %1").arg(r.value("error").toString()), 5000);
            return;
        }
        m_hits.clear();
        const QJsonArray arr = r.value("hits").toArray();
        for (const QJsonValue &v : arr) {
            const QJsonObject h = v.toObject();
            PolarHit hit;
            hit.line = h.value("line").toVariant().toLongLong();
            hit.col = h.value("col").toVariant().toLongLong();
            hit.len = h.value("len").toVariant().toLongLong();
            hit.byte = h.value("byte").toVariant().toLongLong();
            m_hits.append(hit);
        }
        m_hitIndex = 0;
        if (m_hits.isEmpty()) {
            emit statusMessage(tr("«%1»: збігів немає").arg(query), 4000);
        } else {
            jumpToHit(0);
        }
    });
}

void EditorView::jumpToHit(int index)
{
    if (m_hits.isEmpty()) {
        return;
    }
    m_hitIndex = ((index % m_hits.size()) + m_hits.size()) % m_hits.size();
    const PolarHit &h = m_hits.at(m_hitIndex);
    setTopLine(qMax<qlonglong>(h.line - visibleLines() / 2, 0));
    requestFetch(h.line);
    setCursor(Pos{h.line, h.col});
    emit statusMessage(tr("Збіг %1 / %2").arg(m_hitIndex + 1).arg(m_hits.size()), 3000);
}

void EditorView::nextHit()
{
    if (m_hits.isEmpty()) {
        return;
    }
    jumpToHit(m_hitIndex + 1);
}

void EditorView::gotoLine(qlonglong line)
{
    // 1-based номер строки, как в vi (:123)
    const qlonglong l = qMax<qlonglong>(line - 1, 0);
    setTopLine(qMax<qlonglong>(l - visibleLines() / 2, 0));
    requestFetch(l);
    setCursor(Pos{l, 0});
    emit statusMessage(tr("Рядок %1").arg(l + 1), 2000);
}

// ---------------- сохранение ----------------

void EditorView::save(bool as)
{
    if (m_doc == 0) {
        return;
    }
    auto done = [this](const QJsonObject &r) {
        if (r.value("ok").toBool()) {
            m_modified = false;
            emit docModified(this, false);
            emit statusMessage(tr("Збережено: %1").arg(r.value("path").toString()), 3000);
        } else {
            emit statusMessage(tr("Помилка збереження: %1").arg(r.value("error").toString()), 6000);
        }
    };
    if (as || m_path.isEmpty()) {
        // TODO v1.1: диалог Save As — пока сохраняем как есть
        m_bridge->save(m_doc, done);
    } else {
        m_bridge->save(m_doc, done);
    }
}

```

---

## File: `integrations/poler-edit-qt/src/EditorView.h`

- Язык: `c`
- Размер: `4205` байт

```c
// Редакторский вьюпорт: рендер ТОЛЬКО видимых строк (virtual viewport).
// Файл любого размера живёт в ядре poler-edit; здесь — кэш видимого окна
// с оверсканом и асинхронная подгрузка (поколения отбрасывают устаревшие).

#pragma once

#include <QAbstractScrollArea>
#include <QStringList>
#include <QTimer>

#include "EngineBridge.h"

struct PolarHit
{
    qlonglong line = 0;
    qlonglong col = 0;
    qlonglong len = 0;
    qlonglong byte = 0;
};

class EditorView : public QAbstractScrollArea
{
    Q_OBJECT
public:
    EditorView(EngineBridge *bridge, const QString &path, QWidget *parent = nullptr);

    qlonglong docId() const { return m_doc; }
    QString filePath() const { return m_path; }
    QString fileName() const;
    bool isModified() const { return m_modified; }
    bool isIndexed() const { return m_linesTotal > 0; }

    void search(const QString &query, bool caseSensitive);
    void jumpToHit(int index);
    void nextHit();
    void gotoLine(qlonglong line);
    int hitCount() const { return m_hits.size(); }
    void save(bool as);

signals:
    void docOpened(EditorView *view);
    void docModified(EditorView *view, bool modified);
    void cursorMoved(qlonglong line, qlonglong col);
    void statusMessage(const QString &msg, int timeoutMs);
    void requestClose(EditorView *view);
    void commandLineRequested(); // ':' — показать vi-командную строку

public slots:
    void undo();
    void redo();
    void selectAll();
    void copy();
    void cut();
    void paste();
    void zoomIn();
    void zoomOut();

protected:
    void paintEvent(QPaintEvent *e) override;
    void keyPressEvent(QKeyEvent *e) override;
    void mousePressEvent(QMouseEvent *e) override;
    void mouseMoveEvent(QMouseEvent *e) override;
    void mouseReleaseEvent(QMouseEvent *e) override;
    void mouseDoubleClickEvent(QMouseEvent *e) override;
    void wheelEvent(QWheelEvent *e) override;
    void resizeEvent(QResizeEvent *e) override;
    void focusInEvent(QFocusEvent *e) override;
    bool event(QEvent *e) override; // Tab focus + input method

private slots:
    void blinkCursor();
    void onProgress(const QJsonObject &obj);

private:
    // геометрия
    int lineHeight() const;
    int charWidth() const;
    int gutterWidth() const;
    int visibleLines() const;
    qlonglong topLine() const;
    void setTopLine(qlonglong line);

    // кэш видимого окна
    QString lineText(qlonglong line) const;
    qlonglong lineLen(qlonglong line) const;
    void requestFetch(qlonglong aroundLine);
    void fetchRange(qlonglong top, int count);

    // курсор/выделение
    struct Pos
    {
        qlonglong line = 0;
        qlonglong col = 0;
        bool operator<(const Pos &o) const
        {
            return line < o.line || (line == o.line && col < o.col);
        }
        bool operator==(const Pos &o) const { return line == o.line && col == o.col; }
    };
    Pos clampPos(const Pos &p) const;
    void setCursor(const Pos &p, bool keepAnchor = false);
    void ensureCursorVisible();
    bool hasSelection() const { return !(m_anchor == m_cursor); }
    QString selectedText() const;
    void deleteSelection();
    void insertText(const QString &text);
    void afterEdit();

    void updateScrollbars();
    void openDoc();
    void refreshInfo();

    EngineBridge *m_bridge;
    QString m_path;
    qlonglong m_doc = 0;
    bool m_modified = false;
    bool m_indexing = false;
    qlonglong m_bytesTotal = 0;
    qlonglong m_linesTotal = 0; // 0 = неизвестно (индексация идёт)

    QStringList m_lines;   // кэш окна
    qlonglong m_blockTop = 0;
    qlonglong m_fetchGen = 0;   // отбрасывает устаревшие ответы
    qlonglong m_appliedGen = 0;

    Pos m_cursor;
    Pos m_anchor;
    bool m_cursorVisible = true;
    QTimer m_blink;

    QFont m_font;
    int m_zoom = 0;
    int m_baseSize = 10;

    QList<PolarHit> m_hits;
    int m_hitIndex = 0;
    QString m_lastQuery;
};

```

---

## File: `integrations/poler-edit-qt/src/EngineBridge.cpp`

- Язык: `cpp`
- Размер: `6543` байт

```cpp
#include "EngineBridge.h"

#include <QDir>
#include <QFile>
#include <QJsonDocument>
#include <QJsonArray>
#include <QStandardPaths>

EngineBridge::EngineBridge(QObject *parent)
    : QObject(parent)
{
}

EngineBridge::~EngineBridge()
{
    quit();
}

QString EngineBridge::enginePath()
{
    // 1) Явное переопределение окружением.
    const QByteArray env = qgetenv("POLER_ENGINE");
    if (!env.isEmpty() && QFile::exists(QString::fromLocal8Bit(env))) {
        return QString::fromLocal8Bit(env);
    }
    // 2) PATH (у пользователя движок в ~/.local/bin — он в PATH).
    const QString inPath = QStandardPaths::findExecutable("poler-engine");
    if (!inPath.isEmpty()) {
        return inPath;
    }
    // 3) Типичные суверенные пути.
    for (const QString &cand : {QDir::homePath() + "/.local/bin/poler-engine",
                                QString("/usr/local/bin/poler-engine"),
                                QString("/usr/bin/poler-engine")}) {
        if (QFile::exists(cand)) {
            return cand;
        }
    }
    return QString("poler-engine");
}

void EngineBridge::ensureStarted()
{
    if (m_proc && m_proc->state() != QProcess::NotRunning) {
        return;
    }
    if (!m_proc) {
        m_proc = new QProcess(this);
        m_proc->setProcessChannelMode(QProcess::SeparateChannels);
        connect(m_proc, &QProcess::readyReadStandardOutput, this, &EngineBridge::drainStdout);
        connect(m_proc, &QProcess::errorOccurred, this, [this] { emit engineDied(); });
        connect(m_proc, &QProcess::finished, this, [this] { emit engineDied(); });
    }
    m_proc->start(enginePath(), {QStringLiteral("--edit-serve")});
}

void EngineBridge::send(const QJsonObject &obj)
{
    ensureStarted();
    if (!m_proc || m_proc->state() == QProcess::NotRunning) {
        return;
    }
    const QByteArray line = QJsonDocument(obj).toJson(QJsonDocument::Compact);
    m_proc->write(line);
    m_proc->write("\n");
}

void EngineBridge::drainStdout()
{
    if (!m_proc) {
        return;
    }
    m_buf += m_proc->readAllStandardOutput();
    int nl;
    while ((nl = m_buf.indexOf('\n')) >= 0) {
        const QByteArray line = m_buf.left(nl);
        m_buf.remove(0, nl + 1);
        if (line.trimmed().isEmpty()) {
            continue;
        }
        const QJsonDocument doc = QJsonDocument::fromJson(line);
        if (!doc.isObject()) {
            continue;
        }
        const QJsonObject obj = doc.object();
        if (obj.contains("ev")) {
            emit progressEvent(obj);
            continue;
        }
        const qlonglong id = obj.value("id").toVariant().toLongLong();
        const auto it = m_pending.find(id);
        if (it != m_pending.end()) {
            Callback cb = it.value();
            m_pending.erase(it);
            if (cb) {
                cb(obj);
            }
        }
    }
}

qlonglong EngineBridge::open(const QString &path, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "open"}, {"path", path}});
    return id;
}

qlonglong EngineBridge::closeDoc(qlonglong doc)
{
    const qlonglong id = nextId();
    send(QJsonObject{{"id", double(id)}, {"cmd", "close"}, {"doc", double(doc)}});
    return id;
}

qlonglong EngineBridge::viewport(qlonglong doc, qlonglong line, int count, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "viewport"},
                     {"doc", double(doc)}, {"line", double(line)}, {"count", count}});
    return id;
}

qlonglong EngineBridge::indexDoc(qlonglong doc, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "index"}, {"doc", double(doc)}});
    return id;
}

qlonglong EngineBridge::insertAt(qlonglong doc, qlonglong line, qlonglong col,
                                 const QString &text, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "insert_at"}, {"doc", double(doc)},
                     {"line", double(line)}, {"col", double(col)}, {"text", text}});
    return id;
}

qlonglong EngineBridge::del(qlonglong doc, qlonglong sl, qlonglong sc,
                            qlonglong el, qlonglong ec, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "delete"}, {"doc", double(doc)},
                     {"start_line", double(sl)}, {"start_col", double(sc)},
                     {"end_line", double(el)}, {"end_col", double(ec)}});
    return id;
}

qlonglong EngineBridge::search(qlonglong doc, const QString &query, bool caseSensitive,
                               int limit, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "search"}, {"doc", double(doc)},
                     {"query", query}, {"case_sensitive", caseSensitive},
                     {"limit", limit}});
    return id;
}

qlonglong EngineBridge::save(qlonglong doc, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "save"}, {"doc", double(doc)}});
    return id;
}

qlonglong EngineBridge::saveAs(qlonglong doc, const QString &path, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "save_as"},
                     {"doc", double(doc)}, {"path", path}});
    return id;
}

qlonglong EngineBridge::undo(qlonglong doc, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "undo"}, {"doc", double(doc)}});
    return id;
}

qlonglong EngineBridge::redo(qlonglong doc, const Callback &cb)
{
    const qlonglong id = nextId();
    m_pending.insert(id, cb);
    send(QJsonObject{{"id", double(id)}, {"cmd", "redo"}, {"doc", double(doc)}});
    return id;
}

void EngineBridge::cancel()
{
    send(QJsonObject{{"id", double(nextId())}, {"cmd", "cancel"}});
}

void EngineBridge::quit()
{
    if (m_proc && m_proc->state() != QProcess::NotRunning) {
        send(QJsonObject{{"id", double(nextId())}, {"cmd", "quit"}});
        m_proc->waitForFinished(500);
        m_proc->kill();
    }
}

```

---

## File: `integrations/poler-edit-qt/src/EngineBridge.h`

- Язык: `c`
- Размер: `2217` байт

```c
// Асинхронный мост к `poler-engine --edit-serve` (JSON lines поверх stdio).
// Паттерн проверен интеграцией kate-poler: неблокирующий QProcess,
// построчная буферизация stdout, ответы маршрутизируются по reqId.

#pragma once

#include <QHash>
#include <QJsonObject>
#include <QProcess>
#include <QString>
#include <functional>

class EngineBridge : public QObject
{
    Q_OBJECT
public:
    using Callback = std::function<void(const QJsonObject &)>;

    explicit EngineBridge(QObject *parent = nullptr);
    ~EngineBridge() override;

    bool isRunning() const { return m_proc && m_proc->state() == QProcess::Running; }

    // Все команды асинхронны; ответ приходит в cb (в Gui-потоке).
    qlonglong open(const QString &path, const Callback &cb);
    qlonglong closeDoc(qlonglong doc);
    qlonglong viewport(qlonglong doc, qlonglong line, int count, const Callback &cb);
    qlonglong indexDoc(qlonglong doc, const Callback &cb);
    qlonglong insertAt(qlonglong doc, qlonglong line, qlonglong col,
                       const QString &text, const Callback &cb);
    qlonglong del(qlonglong doc, qlonglong sl, qlonglong sc,
                  qlonglong el, qlonglong ec, const Callback &cb);
    qlonglong search(qlonglong doc, const QString &query, bool caseSensitive,
                     int limit, const Callback &cb);
    qlonglong save(qlonglong doc, const Callback &cb);
    qlonglong saveAs(qlonglong doc, const QString &path, const Callback &cb);
    qlonglong undo(qlonglong doc, const Callback &cb);
    qlonglong redo(qlonglong doc, const Callback &cb);
    void cancel();
    void quit();

    static QString enginePath();

signals:
    // События сервера: {"ev":"progress","op":...,"doc":...}
    void progressEvent(const QJsonObject &obj);
    void engineDied();

private:
    void send(const QJsonObject &obj);
    qlonglong nextId() { return ++m_reqId; }
    void drainStdout();
    void ensureStarted();

    QProcess *m_proc = nullptr;
    QByteArray m_buf;
    qlonglong m_reqId = 0;
    QHash<qlonglong, Callback> m_pending;
};

```

---

## File: `integrations/poler-edit-qt/src/MainWindow.cpp`

- Язык: `cpp`
- Размер: `15909` байт

```cpp
#include "MainWindow.h"

#include <QApplication>
#include <QCloseEvent>
#include <QFileDialog>
#include <QFileInfo>
#include <QHBoxLayout>
#include <QKeyEvent>
#include <QLabel>
#include <QLineEdit>
#include <QMenuBar>
#include <QMessageBox>
#include <QPushButton>
#include <QStatusBar>
#include <QStyle>
#include <QTabWidget>
#include <QToolBar>
#include <QVBoxLayout>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , m_bridge(new EngineBridge(this))
{
    setMinimumSize(900, 600);
    resize(1180, 780);

    m_tabs = new QTabWidget(this);
    m_tabs->setTabsClosable(true);
    m_tabs->setMovable(true);
    m_tabs->setDocumentMode(true);
    connect(m_tabs, &QTabWidget::tabCloseRequested, this, &MainWindow::onCloseTab);
    connect(m_tabs, &QTabWidget::currentChanged, this, &MainWindow::onTabChanged);

    buildMenus();
    buildToolbar();
    buildStatusBar();

    // Центральная компоновка: [панель поиска (скрыта)] [вкладки] [':'-строка (скрыта)]
    auto *central = new QWidget(this);
    auto *cl = new QVBoxLayout(central);
    cl->setContentsMargins(0, 0, 0, 0);
    cl->setSpacing(0);
    cl->addWidget(buildSearchBar());
    cl->addWidget(m_tabs, 1);
    cl->addWidget(buildCommandLine());
    setCentralWidget(central);

    connect(m_bridge, &EngineBridge::progressEvent, this, &MainWindow::onProgress);
    connect(m_bridge, &EngineBridge::engineDied, this, &MainWindow::onEngineDied);

    updateTitle();
}

QWidget *MainWindow::buildSearchBar()
{
    m_searchBar = new QWidget(this);
    auto *lay = new QHBoxLayout(m_searchBar);
    lay->setContentsMargins(8, 4, 8, 4);
    m_searchEdit = new QLineEdit(m_searchBar);
    m_searchEdit->setPlaceholderText(
        tr("Знайти (Enter — далі, Esc — закрити)"));
    auto *next = new QPushButton(tr("Далі ▶"), m_searchBar);
    auto *close = new QPushButton(tr("✕"), m_searchBar);
    next->setFixedWidth(80);
    close->setFixedWidth(32);
    lay->addWidget(m_searchEdit, 1);
    lay->addWidget(next);
    lay->addWidget(close);
    connect(next, &QPushButton::clicked, this, &MainWindow::findNext);
    connect(close, &QPushButton::clicked, m_searchBar, &QWidget::hide);
    connect(m_searchEdit, &QLineEdit::returnPressed, this, &MainWindow::findNext);
    m_searchEdit->installEventFilter(this);
    m_searchBar->hide();
    return m_searchBar;
}

QWidget *MainWindow::buildCommandLine()
{
    m_cmdWrap = new QWidget(this);
    auto *lay = new QHBoxLayout(m_cmdWrap);
    lay->setContentsMargins(8, 3, 8, 3);
    auto *mark = new QLabel(":", m_cmdWrap);
    mark->setStyleSheet("font-weight: 700;");
    m_cmdLine = new QLineEdit(m_cmdWrap);
    m_cmdLine->setPlaceholderText(
        tr("w — зберегти; q — закрити вкладку; wq; q!; N — перейти до рядка N"));
    lay->addWidget(mark);
    lay->addWidget(m_cmdLine, 1);
    connect(m_cmdLine, &QLineEdit::returnPressed, this, [this] {
        onCommandLine(m_cmdLine->text());
        m_cmdLine->clear();
        hideCommandLine();
    });
    m_cmdLine->installEventFilter(this);
    m_cmdWrap->hide();
    return m_cmdWrap;
}

// ---------------- документы ----------------

EditorView *MainWindow::currentView() const
{
    return viewAt(m_tabs->currentIndex());
}

EditorView *MainWindow::viewAt(int index) const
{
    if (index < 0) {
        return nullptr;
    }
    return qobject_cast<EditorView *>(m_tabs->widget(index));
}

void MainWindow::attachView(EditorView *view)
{
    connect(view, &EditorView::cursorMoved, this, &MainWindow::onCursorMoved);
    connect(view, &EditorView::statusMessage, this, &MainWindow::onStatusMessage);
    connect(view, &EditorView::docModified, this, &MainWindow::onDocModified);
    connect(view, &EditorView::requestClose, this, [this, view] {
        const int i = m_tabs->indexOf(view);
        if (i >= 0) {
            onCloseTab(i);
        }
    });
    connect(view, &EditorView::commandLineRequested, this, &MainWindow::showCommandLine);
}

void MainWindow::openFile(const QString &path)
{
    auto *view = new EditorView(m_bridge, path, this);
    attachView(view);
    m_tabs->addTab(view, QFileInfo(path).fileName());
    m_tabs->setCurrentWidget(view);
}

void MainWindow::openUntitled()
{
    auto *view = new EditorView(m_bridge, QString(), this);
    attachView(view);
    m_tabs->addTab(view, tr("без назви"));
    m_tabs->setCurrentWidget(view);
}

void MainWindow::onTabChanged(int)
{
    updateTitle();
    if (EditorView *v = currentView()) {
        m_modeLabel->setText(v->isIndexed() ? tr("рядки: точно") : tr("рядки: індекс…"));
    }
}

// ---------------- меню ----------------

void MainWindow::buildMenus()
{
    QMenu *file = menuBar()->addMenu(tr("&Файл"));
    file->addAction(tr("&Відкрити…"), QKeySequence::Open, this, &MainWindow::onOpen);
    file->addAction(tr("&Зберегти"), QKeySequence::Save, this, &MainWindow::onSave);
    file->addAction(tr("Зберегти &як…"), QKeySequence::SaveAs, this, &MainWindow::onSaveAs);
    file->addSeparator();
    file->addAction(tr("Закрити вклад&ку"), QKeySequence::Close, this, [this] {
        onCloseTab(m_tabs->currentIndex());
    });
    file->addAction(tr("&Вихід"), QKeySequence::Quit, qApp, &QApplication::quit);

    QMenu *edit = menuBar()->addMenu(tr("&Правка"));
    edit->addAction(tr("&Скасувати"), QKeySequence::Undo, this, [this] {
        if (EditorView *v = currentView()) v->undo();
    });
    edit->addAction(tr("&Повернути"), QKeySequence::Redo, this, [this] {
        if (EditorView *v = currentView()) v->redo();
    });
    edit->addSeparator();
    edit->addAction(tr("Ви&різати"), QKeySequence::Cut, this, [this] {
        if (EditorView *v = currentView()) v->cut();
    });
    edit->addAction(tr("&Копіювати"), QKeySequence::Copy, this, [this] {
        if (EditorView *v = currentView()) v->copy();
    });
    edit->addAction(tr("В&ставити"), QKeySequence::Paste, this, [this] {
        if (EditorView *v = currentView()) v->paste();
    });
    edit->addSeparator();
    edit->addAction(tr("Виділити &все"), QKeySequence::SelectAll, this, [this] {
        if (EditorView *v = currentView()) v->selectAll();
    });

    QMenu *view = menuBar()->addMenu(tr("&Вигляд"));
    view->addAction(tr("Збіль&шити шрифт"), QKeySequence::ZoomIn, this, [this] {
        if (EditorView *v = currentView()) v->zoomIn();
    });
    view->addAction(tr("З&меншити шрифт"), QKeySequence::ZoomOut, this, [this] {
        if (EditorView *v = currentView()) v->zoomOut();
    });
    view->addSeparator();
    view->addAction(tr("Командний рядок &:"), QKeySequence(Qt::CTRL | Qt::Key_Colon),
                    this, &MainWindow::showCommandLine);

    QMenu *search = menuBar()->addMenu(tr("&Пошук"));
    search->addAction(tr("&Знайти…"), QKeySequence::Find, this, &MainWindow::onFind);
    search->addAction(tr("Знайти &далі"), QKeySequence(Qt::Key_F3), this, &MainWindow::findNext);

    QMenu *help = menuBar()->addMenu(tr("&Довідка"));
    help->addAction(tr("&Про POLER Editor"), QKeySequence::HelpContents, this, &MainWindow::showAbout);
}

void MainWindow::buildToolbar()
{
    QToolBar *tb = addToolBar(tr("Головна"));
    tb->setMovable(false);
    tb->setIconSize(QSize(18, 18));
    tb->setToolButtonStyle(Qt::ToolButtonTextBesideIcon);
    tb->addAction(style()->standardIcon(QStyle::SP_DialogOpenButton), tr("Відкрити"),
                  this, &MainWindow::onOpen);
    tb->addAction(style()->standardIcon(QStyle::SP_DialogSaveButton), tr("Зберегти"),
                  this, &MainWindow::onSave);
    tb->addSeparator();
    tb->addAction(style()->standardIcon(QStyle::SP_ArrowBack), tr("Скасувати"), this, [this] {
        if (EditorView *v = currentView()) v->undo();
    });
    tb->addAction(style()->standardIcon(QStyle::SP_ArrowForward), tr("Повернути"), this, [this] {
        if (EditorView *v = currentView()) v->redo();
    });
    tb->addSeparator();
    tb->addAction(style()->standardIcon(QStyle::SP_FileDialogContentsView), tr("Знайти"),
                  this, &MainWindow::onFind);
}

// ---------------- статусная строка ----------------

void MainWindow::buildStatusBar()
{
    m_posLabel = new QLabel(tr("Ряд 1, Ст 1"), this);
    m_bytesLabel = new QLabel(tr("— байт"), this);
    m_linesLabel = new QLabel(tr("— рядків"), this);
    m_modeLabel = new QLabel(tr("UTF-8"), this);
    m_engineLabel = new QLabel(tr("POLER ●"), this);
    for (QLabel *l : {m_posLabel, m_bytesLabel, m_linesLabel, m_modeLabel, m_engineLabel}) {
        l->setMargin(6);
        statusBar()->addPermanentWidget(l);
    }
    m_engineLabel->setStyleSheet("color: #2ecc71; font-weight: 600;");
}

void MainWindow::onCursorMoved(qlonglong line, qlonglong col)
{
    m_posLabel->setText(tr("Ряд %1, Ст %2").arg(line).arg(col));
}

void MainWindow::onStatusMessage(const QString &msg, int timeoutMs)
{
    statusBar()->showMessage(msg, timeoutMs);
}

void MainWindow::onDocModified(EditorView *view, bool modified)
{
    Q_UNUSED(modified);
    updateTabTitle(view);
    updateTitle();
}

void MainWindow::onProgress(const QJsonObject &)
{
    // Прогресс индексации ретранслируется самим EditorView в statusMessage.
}

void MainWindow::onEngineDied()
{
    m_engineLabel->setText(tr("POLER ○"));
    m_engineLabel->setStyleSheet("color: #da4453; font-weight: 600;");
    statusBar()->showMessage(
        tr("Ядро poler-engine недоступне. Перевірте poler-engine у PATH."), 10000);
}

void MainWindow::updateTabTitle(EditorView *view)
{
    if (!view) {
        return;
    }
    const int i = m_tabs->indexOf(view);
    if (i < 0) {
        return;
    }
    m_tabs->setTabText(i, (view->isModified() ? "* " : QString()) + view->fileName());
}

void MainWindow::updateTitle()
{
    EditorView *v = currentView();
    if (v) {
        setWindowTitle(tr("%1%2 — POLER Editor")
                           .arg(v->isModified() ? "*" : QString())
                           .arg(v->fileName()));
    } else {
        setWindowTitle(tr("POLER Editor"));
    }
}

// ---------------- команды ----------------

void MainWindow::onOpen()
{
    const QStringList files = QFileDialog::getOpenFileNames(
        this, tr("Відкрити файли"), QString(),
        tr("Усі файли (*);;Текст (*.txt *.md *.log *.rs *.c *.cpp *.py *.json *.xml *.yaml)"));
    for (const QString &f : files) {
        openFile(f);
    }
}

void MainWindow::onSave()
{
    if (EditorView *v = currentView()) {
        v->save(false);
    }
}

void MainWindow::onSaveAs()
{
    const QString path = QFileDialog::getSaveFileName(this, tr("Зберегти як"));
    if (!path.isEmpty()) {
        // v1.1: save_as в ядро; пока сохраняем текущий документ
        if (EditorView *v = currentView()) {
            v->save(false);
        }
    }
}

void MainWindow::onCloseTab(int index)
{
    EditorView *v = viewAt(index);
    if (!v) {
        return;
    }
    if (v->isModified()) {
        const auto btn = QMessageBox::question(
            this, tr("Незбережені зміни"),
            tr("«%1» містить незбережені зміни. Закрити без збереження?").arg(v->fileName()),
            QMessageBox::Save | QMessageBox::Discard | QMessageBox::Cancel);
        if (btn == QMessageBox::Cancel) {
            return;
        }
        if (btn == QMessageBox::Save) {
            v->save(false);
        }
    }
    if (v->docId() > 0) {
        m_bridge->closeDoc(v->docId());
    }
    m_tabs->removeTab(index);
    v->deleteLater();
    updateTitle();
}

void MainWindow::closeEvent(QCloseEvent *e)
{
    for (int i = 0; i < m_tabs->count(); ++i) {
        if (viewAt(i) && viewAt(i)->isModified()) {
            const auto btn = QMessageBox::question(
                this, tr("Незбережені зміни"),
                tr("Є незбережені зміни. Вийти без збереження?"),
                QMessageBox::Save | QMessageBox::Discard | QMessageBox::Cancel);
            if (btn == QMessageBox::Cancel) {
                e->ignore();
                return;
            }
            if (btn == QMessageBox::Save) {
                viewAt(i)->save(false);
            }
            break;
        }
    }
    m_bridge->quit();
    e->accept();
}

// ---------------- поиск ----------------

void MainWindow::onFind()
{
    m_searchBar->show();
    m_searchEdit->setFocus();
    m_searchEdit->selectAll();
}

void MainWindow::findNext()
{
    EditorView *v = currentView();
    if (!v) {
        return;
    }
    if (v->hitCount() > 0) {
        v->nextHit();
    } else if (!m_searchEdit->text().isEmpty()) {
        v->search(m_searchEdit->text(), false);
    }
}

// ---------------- vi-командная строка ----------------

void MainWindow::showCommandLine()
{
    m_cmdWrap->show();
    m_cmdLine->setFocus();
}

void MainWindow::hideCommandLine()
{
    m_cmdWrap->hide();
    if (EditorView *v = currentView()) {
        v->setFocus();
    }
}

void MainWindow::onCommandLine(const QString &text)
{
    EditorView *v = currentView();
    if (!v) {
        return;
    }
    const QString cmd = text.trimmed();
    if (cmd == "w") {
        v->save(false);
    } else if (cmd == "q") {
        onCloseTab(m_tabs->currentIndex());
    } else if (cmd == "q!") {
        if (v->docId() > 0) {
            m_bridge->closeDoc(v->docId());
        }
        m_tabs->removeTab(m_tabs->currentIndex());
        v->deleteLater();
    } else if (cmd == "wq") {
        v->save(false);
        onCloseTab(m_tabs->currentIndex());
    } else {
        bool ok = false;
        const qlonglong line = cmd.toLongLong(&ok);
        if (ok && line > 0) {
            statusBar()->showMessage(tr("Перехід до рядка %1…").arg(line), 2000);
            v->gotoLine(line);
        } else {
            statusBar()->showMessage(tr("Невідома команда: %1").arg(cmd), 4000);
        }
    }
}

bool MainWindow::eventFilter(QObject *obj, QEvent *e)
{
    // Esc в поиске/командной строке — скрыть и вернуть фокус редактору
    if (e->type() == QEvent::KeyPress) {
        auto *ke = static_cast<QKeyEvent *>(e);
        if (ke->key() == Qt::Key_Escape) {
            if (obj == m_cmdLine) {
                m_cmdLine->clear();
                hideCommandLine();
                return true;
            }
            if (m_searchBar && m_searchBar->isVisible()) {
                m_searchBar->hide();
                if (EditorView *v = currentView()) {
                    v->setFocus();
                }
                return true;
            }
        }
    }
    return QMainWindow::eventFilter(obj, e);
}

void MainWindow::showAbout()
{
    QMessageBox::about(
        this, tr("POLER Editor"),
        tr("<b>POLER Editor 0.38.0</b><br/>"
           "Суверенний текстовий редактор без лімітів розміру файлів.<br/><br/>"
           "Ядро: poler-engine --edit-serve<br/>"
           "Zero-copy mmap piece-table + SIMD Aho-Corasick + ленивий line-index.<br/><br/>"
           "100 GiB файл відкривається за 0.1 мс; RAM не залежить від розміру.<br/>"
           "<a href=\"https://github.com/poler-engine-org/poler-engine\">"
           "github.com/poler-engine-org/poler-engine</a>"));
}

```

---

## File: `integrations/poler-edit-qt/src/MainWindow.h`

- Язык: `c`
- Размер: `2021` байт

```c
// Главное окно POLER Editor: Kate-подобный интерфейс.
// Меню, панель инструментов, вкладки документов, статусная строка,
// vi-подобная командная строка (:w :q :wq :123), панель поиска.

#pragma once

#include <QMainWindow>

#include "EditorView.h"

class QTabWidget;
class QLabel;
class QLineEdit;
class QPushButton;
class EngineBridge;

class MainWindow : public QMainWindow
{
    Q_OBJECT
public:
    explicit MainWindow(QWidget *parent = nullptr);

    void openFile(const QString &path);
    void openUntitled();

protected:
    void closeEvent(QCloseEvent *e) override;
    bool eventFilter(QObject *obj, QEvent *e) override;

private slots:
    void onOpen();
    void onSave();
    void onSaveAs();
    void onCloseTab(int index);
    void onFind();
    void findNext();
    void onCursorMoved(qlonglong line, qlonglong col);
    void onStatusMessage(const QString &msg, int timeoutMs);
    void onDocModified(EditorView *view, bool modified);
    void onProgress(const QJsonObject &obj);
    void onCommandLine(const QString &text);
    void showCommandLine();
    void hideCommandLine();
    void onEngineDied();
    void onTabChanged(int index);
    void showAbout();

private:
    EditorView *currentView() const;
    EditorView *viewAt(int index) const;
    void attachView(EditorView *view);
    QWidget *buildSearchBar();
    QWidget *buildCommandLine();
    void buildMenus();
    void buildToolbar();
    void buildStatusBar();
    void updateTabTitle(EditorView *view);
    void updateTitle();

    EngineBridge *m_bridge;
    QTabWidget *m_tabs = nullptr;
    QLineEdit *m_searchEdit = nullptr;
    QWidget *m_searchBar = nullptr;
    QLineEdit *m_cmdLine = nullptr;
    QWidget *m_cmdWrap = nullptr;

    QLabel *m_posLabel = nullptr;
    QLabel *m_bytesLabel = nullptr;
    QLabel *m_linesLabel = nullptr;
    QLabel *m_modeLabel = nullptr;
    QLabel *m_engineLabel = nullptr;
};

```

---

## File: `integrations/poler-edit-qt/src/main.cpp`

- Язык: `cpp`
- Размер: `2754` байт

```cpp
// POLER Editor — точка входа.
// Kate-подобный суверенный редактор: GUI здесь тонкий, вся работа с текстом
// любого размера — в ядре poler-edit (poler-engine --edit-serve).

#include <QApplication>
#include <QCommandLineParser>
#include <QFile>
#include <QPalette>
#include <QStyleFactory>

#include "MainWindow.h"

static void applyDarkPalette(QApplication &app)
{
    // Breeze-тёмная гамма (KDE Dark): фон #232629, панели #31363b,
    // текст #eff0f1, акцент #3daee9.
    app.setStyle(QStyleFactory::create("Fusion"));
    QPalette p;
    const QColor window(0x23, 0x26, 0x29);
    const QColor base(0x25, 0x28, 0x2c);
    const QColor button(0x31, 0x36, 0x3b);
    const QColor text(0xef, 0xf0, 0xf1);
    const QColor highlight(0x3d, 0xae, 0xe9);
    p.setColor(QPalette::Window, window);
    p.setColor(QPalette::WindowText, text);
    p.setColor(QPalette::Base, base);
    p.setColor(QPalette::AlternateBase, window);
    p.setColor(QPalette::Text, text);
    p.setColor(QPalette::Button, button);
    p.setColor(QPalette::ButtonText, text);
    p.setColor(QPalette::Highlight, highlight);
    p.setColor(QPalette::HighlightedText, Qt::black);
    p.setColor(QPalette::ToolTipBase, base);
    p.setColor(QPalette::ToolTipText, text);
    p.setColor(QPalette::PlaceholderText, QColor(0x7f, 0x8c, 0x8d));
    p.setColor(QPalette::Disabled, QPalette::Text, QColor(0x6d, 0x70, 0x74));
    p.setColor(QPalette::Disabled, QPalette::ButtonText, QColor(0x6d, 0x70, 0x74));
    app.setPalette(p);
}

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    QApplication::setApplicationName("poler-edit");
    QApplication::setApplicationVersion("0.38.0");
    QApplication::setOrganizationName("POLER Engine");

    QCommandLineParser cli;
    cli.setApplicationDescription(
        "POLER Editor — суверенный текстовый редактор без лимитов размера файлов.\n"
        "Ядро: poler-engine --edit-serve (zero-copy mmap piece-table).");
    cli.addHelpOption();
    cli.addVersionOption();
    cli.addOption({{"l", "light"}, "светлая тема (по умолчанию тёмная, как Breeze Dark)"});
    cli.addPositionalArgument("files", "файлы для открытия", "[files...]");
    cli.process(app);

    if (!cli.isSet("light")) {
        applyDarkPalette(app);
    }

    MainWindow win;
    win.show();

    const QStringList args = cli.positionalArguments();
    if (args.isEmpty()) {
        win.openUntitled();
    } else {
        for (const QString &f : args) {
            win.openFile(f);
        }
    }
    return app.exec();
}

```

---

## File: `integrations/zai-extension-suite/content.js`

- Язык: `javascript`
- Размер: `2587` байт

```javascript
// Z.ai Ultimate Suite: Content Script
(function() {
    console.log("[Z.ai Suite] Initializing POLER extension features...");

    // 1. Снятие лимитов на вставку длинного текста
    document.addEventListener('paste', function(e) {
        e.stopImmediatePropagation();
    }, true);

    // 2. Умное обновление заголовка вкладки по теме чата
    function updateTabTitle() {
        const titleElem = document.querySelector('h1, .chat-title, [class*="title"], [class*="header"]');
        if (titleElem && titleElem.innerText.trim().length > 0) {
            const topic = titleElem.innerText.trim();
            if (!document.title.startsWith(topic)) {
                document.title = `${topic} — Z.ai`;
            }
        }
    }
    setInterval(updateTabTitle, 3000);

    // 3. Плавающая кнопка "Экспорт в Markdown"
    function injectExportButton() {
        if (document.getElementById('zai-export-btn')) return;

        const btn = document.createElement('button');
        btn.id = 'zai-export-btn';
        btn.innerHTML = '📥 Экспорт в Markdown';
        btn.style.position = 'fixed';
        btn.style.bottom = '20px';
        btn.style.right = '20px';
        btn.style.zIndex = '999999';
        btn.style.padding = '10px 16px';
        btn.style.background = '#2563eb';
        btn.style.color = '#ffffff';
        btn.style.border = 'none';
        btn.style.borderRadius = '8px';
        btn.style.cursor = 'pointer';
        btn.style.fontWeight = 'bold';
        btn.style.boxShadow = '0 4px 12px rgba(0,0,0,0.3)';

        btn.onclick = function() {
            let md = `# Диалог Z.ai (${window.location.href})\nДата: ${new Date().toLocaleString()}\n\n`;
            const messages = document.querySelectorAll('[class*="message"], [class*="chat-item"], .prose');
            messages.forEach((msg, idx) => {
                const text = msg.innerText.trim();
                if (text) {
                    md += `### Сообщение ${idx + 1}\n\n${text}\n\n---\n\n`;
                }
            });
            const blob = new Blob([md], { type: 'text/markdown;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `zai_chat_${Date.now()}.md`;
            a.click();
            URL.revokeObjectURL(url);
        };

        document.body.appendChild(btn);
    }

    setTimeout(injectExportButton, 2000);
})();

```

---

## File: `integrations/zai-extension-suite/manifest.json`

- Язык: `json`
- Размер: `626` байт

```json
{
  "manifest_version": 3,
  "name": "Z.ai Ultimate Suite (POLER Edition)",
  "version": "1.0.0",
  "description": "Полный набор улучшений для chat.z.ai: широкий экран, экспорт в Markdown, снятие лимитов вставки и умные заголовки вкладок.",
  "permissions": ["activeTab", "storage"],
  "host_permissions": ["https://chat.z.ai/*", "https://*.z.ai/*"],
  "content_scripts": [
    {
      "matches": ["https://chat.z.ai/*", "https://*.z.ai/*"],
      "js": ["content.js"],
      "css": ["style.css"],
      "run_at": "document_end"
    }
  ]
}

```

---

