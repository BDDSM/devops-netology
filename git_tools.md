# Домашнее задание к занятию «2.4. Инструменты Git»

Для выполнения заданий в этом разделе давайте склонируем репозиторий с исходным кодом терраформа https://github.com/hashicorp/terraform 

В виде результата напишите текстом ответы на вопросы и каким образом эти ответы были получены.

1. Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`.

    - Команда: `git show aefea`
    - Результат вывода: aefead2207ef7e2aa5dc81a34aedf0cad4c32545 etc...

2. Какому тегу соответствует коммит `85024d3`?

    - Команда: `git show 85024d3`
    - Результат вывода: commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)

3. Сколько родителей у коммита `b8d720`? Напишите их хеши.

    - Команда: `git show b8d720`
    - Результат вывода: commit b8d720f8340221f2146e4e4870bf2ee0bc48f2d5
                        Merge: 56cd7859e 9ea88f22f
                        Затем: `git show 56cd7859e`; `git show ffbcf5581`

4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами  v0.12.23 и v0.12.24.

    - Команда: `git log v0.12.23..v0.12.24 --pretty=oneline`
    - Результат вывода: 33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24) v0.12.24
                        b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
                        3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
                        6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable
                        5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location
                        06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
                        d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
                        4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
                        dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
                        225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release

5. Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит так `func providerSource(...)` (вместо ... перечислены аргументы).

    Вариант 1.
    - Команда: `git log -S 'providerSource(' --pretty=oneline`

    - Результат вывода: 8c928e83589d90a031f811fae52a81be7153e82f main: Consult local directories as potential mirrors of providers
                        18dd1bb4d6134b9f8e15b9cea7fae0c878d0a8ba Mildwonkey/tfconfig upgrade (#23670)
    
    - Проверяем оба коммита и находим, что файл `provider_source.go` вместе со всем своим содержимым (в том числе нашей функцией был добавлен в коммите 8c928e83589d90a031f811fae52a81be7153e82f)

    Вариант 2.
    - Команда: `git blame -L 19,36 provider_source.go` (19-36 - диапазон строк тела функции, provider_source.go - файл, в котором содержится определение функции, найден полнотекстовым поиском средствами IDE)

    - Результат вывода: 8c928e8358 (Martin Atkins 2020-04-02 18:04:39 -0700 19) // providerSource constructs a provider source based on a combination of the
                        8c928e8358 (Martin Atkins 2020-04-02 18:04:39 -0700 20) // CLI configuration and some default search locations. This will be the
                        8c928e8358 (Martin Atkins 2020-04-02 18:04:39 -0700 21) // provider source used for provider installation in the "terraform init"
                        8c928e8358 (Martin Atkins 2020-04-02 18:04:39 -0700 22) // command, unless overridden by the special -plugin-dir option.
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 23) func providerSource(configs []*cliconfig.ProviderInstallation, services *disco.Disco) (getproviders.Source, tfdiags.Diagnostics) {
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 24)         if len(configs) == 0 {
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 25)                 // If there's no explicit installation configuration then we'll build
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 26)                 // up an implicit one with direct registry installation along with
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 27)                 // some automatically-selected local filesystem mirrors.
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 28)                 return implicitProviderSource(services), nil
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 29)         }
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 30)
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 31)         // There should only be zero or one configurations, which is checked by
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 32)         // the validation logic in the cliconfig package. Therefore we'll just
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 33)         // ignore any additional configurations in here.
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 34)         config := configs[0]
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 35)         return explicitProviderSource(config, services)
                        5af1e6234a (Martin Atkins 2020-04-21 16:28:59 -0700 36) }

    - Первое упоминание искомого диапазона строк в искомом файле - коммит 8c928e8358 (Martin Atkins 2020-04-02).

6. Найдите все коммиты в которых была изменена функция `globalPluginDirs`.

    Вариант 1.
    - Команда: `git log -L :globalPluginDirs:plugins.go` (plugins.go - файл, в котором содержится определение функции, найден полнотекстовым поиском средствами IDE)

    - Результат вывода: полный список всех коммитов, где происходили изменения функции в данном файле, с указанием добавленных и удаленных строк

    Вариант 2.
    - Команда: `git blame -L 12,31 plugins.go` (12-31 - диапазон строк тела функции)

    - Результат вывода: 8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 12) // globalPluginDirs returns directories that should be searched for
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 13) // globally-installed plugins (not specific to the current configuration).
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 14) //
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 15) // Earlier entries in this slice get priority over later when multiple copies
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 16) // of the same plugin version are found, but newer versions always override
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 17) // older versions where both satisfy the provider version constraints.
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 18) func globalPluginDirs() []string {
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 19)         var ret []string
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 20)         // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
                        78b1220558 (Pam Selle     2020-01-13 16:50:05 -0500 21)         dir, err := cliconfig.ConfigDir()
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 22)         if err != nil {
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 23)                 log.Printf("[ERROR] Error finding global config directory: %s", err)
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 24)         } else {
                        41ab0aef7a (James Bardin  2017-08-09 10:34:11 -0400 25)                 machineDir := fmt.Sprintf("%s_%s", runtime.GOOS, runtime.GOARCH)
                        52dbf94834 (James Bardin  2017-08-09 17:46:49 -0400 26)                 ret = append(ret, filepath.Join(dir, "plugins"))
                        41ab0aef7a (James Bardin  2017-08-09 10:34:11 -0400 27)                 ret = append(ret, filepath.Join(dir, "plugins", machineDir))
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 28)         }
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 29)
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 30)         return ret
                        8364383c35 (Martin Atkins 2017-04-13 18:05:58 -0700 31) }

7. Кто автор функции `synchronizedWriters`?

    Имя функции не находится поиском по исходным кодам репозитария.
    Команда `git grep 'synchronizedWriters'` также ничего не выдает (что логично, так как она тоже ищет по файлам репозитария).
    Очевидно, эта функция если и когда-нибудь существовала - была удалена в дальнейшем.

    - Команда: `git log -S 'synchronizedWriters' --oneline`

    - Результат вывода: bdfea50cc remove unused
                        fd4f7eb0b remove prefixed io
                        5ac311e2a main: synchronize writes to VT100-faker on Windows

    - Команда: `git show 5ac311e2a`

    - Результат вывода: commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5
                        Author: Martin Atkins <mart@degeneration.co.uk>

    - В выводе команды видим добавленные строки (со знаком +), которые и содержат искомую функцию.