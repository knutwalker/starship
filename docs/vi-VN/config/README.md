# Cấu hình

Để bắt đầu cấu hình starship, tạo tập tin sau: `~/.config/starship.toml`.

```sh
mkdir -p ~/.config && touch ~/.config/starship.toml
```

Tất cả cấu hình của starship đã xong trong tập tin này: [TOML](https://github.com/toml-lang/toml):

```toml
# Chèn một dòng trắng vào giữa các dấu nhắc lệnh
add_newline = true

# Thay thế biểu tượng "❯" trong dấu nhắc lệnh bằng "➜"
[character]                            # Tên mô đun chúng ta đang cấu hình là "character"
success_symbol = "[➜](bold green)"     # đoạn "success_symbol" đươc thiết lập thành "➜" với màu "bold green"

#Vô hiệu mô đun package, ẩn nó hoàn toàn trong dấu nhắc lệnh
[package]
disabled = true
```

Bạn thay đổi địa chỉ tệp tin cấu hình mặc định bằng biến môi trường `STARSHIP_CONFIG`:

```sh
export STARSHIP_CONFIG=~/example/non/default/path/starship.toml
```

Tương đương trong PowerShell (Windows) sẽ được thêm dòng này vào `$PROFILE` của bạn:

```powershell
$ENV:STARSHIP_CONFIG = "$HOME\example\non\default\path\starship.toml"
```

Or for Cmd (Windows) would be adding this line to your `starship.lua`:

```lua
os.setenv('STARSHIP_CONFIG', 'C:\\Users\\user\\example\\non\\default\\path\\starship.toml')
```

### Logging

By default starship logs warnings and errors into a file named `~/.cache/starship/session_${STARSHIP_SESSION_KEY}.log`, where the session key is corresponding to a instance of your terminal. This, however can be changed using the `STARSHIP_CACHE` environment variable:

```sh
export STARSHIP_CACHE=~/.starship/cache
```

Tương đương trong PowerShell (Windows) sẽ được thêm dòng này vào `$PROFILE` của bạn:

```powershell
$ENV:STARSHIP_CACHE = "$HOME\AppData\Local\Temp"
```

Or for Cmd (Windows) would be adding this line to your `starship.lua`:

```lua
os.setenv('STARSHIP_CACHE', 'C:\\Users\\user\\AppData\\Local\\Temp')
```

### Thuật ngữ

**Module**: A component in the prompt giving information based on contextual information from your OS. For example, the "nodejs" module shows the version of Node.js that is currently installed on your computer, if your current directory is a Node.js project.

**Variable**: Smaller sub-components that contain information provided by the module. For example, the "version" variable in the "nodejs" module contains the current version of Node.js.

By convention, most modules have a prefix of default terminal color (e.g. `via` in "nodejs") and an empty space as a suffix.

### Định dạng các chuỗi

Format strings are the format that a module prints all its variables with. Most modules have an entry called `format` that configures the display format of the module. You can use texts, variables and text groups in a format string.

#### Biến

A variable contains a `$` symbol followed by the name of the variable. The name of a variable can only contain letters, numbers and `_`.

For example:

- `$version` là một đính dạng chuỗi với một biến đặt tên là `version`.
- `$git_branch$git_commit` là một định dạng chuỗi với hai biến named `git_branch` và `git_commit`.
- `$git_branch $git_commit` có hai biến phân cách bằng một khoảng trắng.

#### Nhóm văn bản

A text group is made up of two different parts.

The first part, which is enclosed in a `[]`, is a [format string](#format-strings). You can add texts, variables, or even nested text groups in it.

In the second part, which is enclosed in a `()`, is a [style string](#style-strings). This can be used to style the first part.

For example:

- `[on](red bold)` sẽ in một chuỗi `on` với chữ đậm tô màu đỏ.
- `[⌘ $version](bold green)` sẽ in một biểu tượng `⌘` theo sau là nội dung của biến `version`, với chữ in đậm màu xanh lá cây.
- `[a [b](red) c](green)` sẽ in `a b c` với `b` màu đỏ, `a` và `c` màu xanh lá cây.

#### Các chuỗi kiểu

Most modules in starship allow you to configure their display styles. This is done with an entry (usually called `style`) which is a string specifying the configuration. Here are some examples of style strings along with what they do. For details on the full syntax, consult the [advanced config guide](/advanced-config/).

- `"fg:green bg:blue"` thiết lập chữ màu xanh lá cây trên nền màu xanh nước biển
- `"bg:blue fg:bright-green"` thiết lập chữ màu xanh lá cây sáng trên nền màu canh nước biển
- `"bold fg:27"` thiết lập chữ đậm với [màu ANSI](https://i.stack.imgur.com/KTSQa.png) 27
- `"underline bg:#bf5700"` thiết lập chữ gạch chân trên một nền màu da cam
- `"bold italic fg:purple"` thiết lập chữa nghiêng đậm có màu tím
- `""` vô hiệu hoá tất cả các kiểu

Note that what styling looks like will be controlled by your terminal emulator. For example, some terminal emulators will brighten the colors instead of bolding text, and some color themes use the same values for the normal and bright colors. Also, to get italic text, your terminal must support italics.

#### Điều kiện định dạng chuỗi

A conditional format string wrapped in `(` and `)` will not render if all variables inside are empty.

For example:

- `(@$region)` will show nothing if the variable `region` is `None` or empty string, otherwise `@` followed by the value of region.
- `(một vài văn bản)` sẽ không hiển thị thứ gì khi không có những biến bọc trong các dấu ngoặc.
- Khi `$all` là một shortcut cho `\[$a$b\]`, `($all)` sẽ không hiển thị chỉ khi `$a` và `$b` đều là `None`. Cái này làm việc giống như `(\[$a$b\] )`.

#### Special characters

The following symbols have special usage in a format string and must be escaped: `$ \ [ ] ( )`.

Note that TOML has [both basic strings and literal strings](https://toml.io/en/v1.0.0#string). It is recommended to use a literal string (surrounded by single quotes) in your config. If you want to use a basic string (surrounded by double quotes), you must escape the backslash itself (i.e. use `\\`).

For example, when you want to print a `$` symbol on a new line, the following configs for `format` are equivalent:

```toml
# with basic string
format = "\n\\$"

# with multiline basic string
format = """

\\$"""

# with literal string
format = '''

\$'''
```

## Prompt

This is the list of prompt-wide configuration options.

### Các tuỳ chọn

| Tuỳ chọn          | Mặc định                       | Mô tả                                                                    |
| ----------------- | ------------------------------ | ------------------------------------------------------------------------ |
| `format`          | [link](#default-prompt-format) | Cấu hình định dạng của prompt.                                           |
| `right_format`    | `""`                           | See [Enable Right Prompt](/advanced-config/#enable-right-prompt)         |
| `scan_timeout`    | `30`                           | Timeout của starship cho việc quét các tập tin (tính theo milliseconds). |
| `command_timeout` | `500`                          | Timeout for commands executed by starship (in milliseconds).             |
| `add_newline`     | `true`                         | Chèn dòng trắng giữa các dấu nhắc lệnh.                                  |


### Ví dụ

```toml
# ~/.config/starship.toml

# Use custom format
format = """
[┌───────────────────>](bold green)
[│](bold green)$directory$rust$package
[└─>](bold green) """

# Wait 10 milliseconds for starship to check files under the current directory.
scan_timeout = 10

# Disable the blank line at the start of the prompt
add_newline = false
```

### Định dạng prompt mặc định

The default `format` is used to define the format of the prompt, if empty or no `format` is provided. The default is as shown:

```toml
format = "$all"

# Which is equivalent to
format = """
$username\
$hostname\
$shlvl\
$singularity\
$kubernetes\
$directory\
$vcsh\
$git_branch\
$git_commit\
$git_state\
$git_metrics\
$git_status\
$hg_branch\
$docker_context\
$package\
$cmake\
$cobol\
$dart\
$deno\
$dotnet\
$elixir\
$elm\
$erlang\
$golang\
$helm\
$java\
$julia\
$kotlin\
$lua\
$nim\
$nodejs\
$ocaml\
$perl\
$php\
$pulumi\
$purescript\
$python\
$rlang\
$red\
$ruby\
$rust\
$scala\
$swift\
$terraform\
$vlang\
$vagrant\
$zig\
$nix_shell\
$conda\
$memory_usage\
$aws\
$gcloud\
$openstack\
$azure\
$env_var\
$crystal\
$custom\
$sudo\
$cmd_duration\
$line_break\
$jobs\
$battery\
$time\
$status\
$shell\
$character"""
```

If you just want to extend the default format, you can use `$all`; modules you explicitly add to the format will not be duplicated. Eg.

```toml
# Move the directory to the second line
format="$all$directory$character"
```

## AWS

The `aws` module shows the current AWS region and profile. This is based on `AWS_REGION`, `AWS_DEFAULT_REGION`, and `AWS_PROFILE` env var with `~/.aws/config` file. This module also shows an expiration timer when using temporary credentials.

When using [aws-vault](https://github.com/99designs/aws-vault) the profile is read from the `AWS_VAULT` env var and the credentials expiration date is read from the `AWS_SESSION_EXPIRATION` env var.

When using [awsu](https://github.com/kreuzwerker/awsu) the profile is read from the `AWSU_PROFILE` env var.

When using [AWSume](https://awsu.me) the profile is read from the `AWSUME_PROFILE` env var and the credentials expiration date is read from the `AWSUME_EXPIRATION` env var.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                             | Mô tả                                                             |
| ------------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `format`            | `'on [$symbol($profile )(\($region\) )(\[$duration\])]($style)'` | Định dạng cho module.                                             |
| `symbol`            | `"☁️ "`                                                              | Kí hiệu sử dụng hiển thị trước profile AWS hiện tại.              |
| `region_aliases`    |                                                                      | Bảng của các region alias để hiển thị ngoài tên AWS.              |
| `style`             | `"bold yellow"`                                                      | Kiểu cho module.                                                  |
| `expiration_symbol` | `X`                                                                  | The symbol displayed when the temporary credentials have expired. |
| `disabled`          | `false`                                                              | Vô hiệu `AWS` module.                                             |

### Các biến

| Biến      | Ví dụ            | Mô tả                                       |
| --------- | ---------------- | ------------------------------------------- |
| region    | `ap-northeast-1` | Region AWS hiện tại                         |
| profile   | `astronauts`     | Profile AWS hiện tại                        |
| duration  | `2h27m20s`       | The temporary credentials validity duration |
| symbol    |                  | Giá trị ghi đè tuỳ chọn `symbol`            |
| style\* |                  | Giá trị ghi đè của `style`                  |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Các vị dụ

#### Hiển thị mọi thứ

```toml
# ~/.config/starship.toml

[aws]
format = 'on [$symbol($profile )(\($region\) )]($style)'
style = "bold blue"
symbol = "🅰 "
[aws.region_aliases]
ap-southeast-2 = "au"
us-east-1 = "va"
```

#### Hiển thị region

```toml
# ~/.config/starship.toml

[aws]
format = "on [$symbol$region]($style) "
style = "bold blue"
symbol = "🅰 "
[aws.region_aliases]
ap-southeast-2 = "au"
us-east-1 = "va"
```

#### Hiển thị profile

```toml
# ~/.config/starship.toml

[aws]
format = "on [$symbol$profile]($style) "
style = "bold blue"
symbol = "🅰 "
```

## Azure

The `azure` module shows the current Azure Subscription. This is based on showing the name of the default subscription, as defined in the `~/.azure/azureProfile.json` file.

### Các tuỳ chọn

| Biến       | Mặc định                                 | Mô tả                                      |
| ---------- | ---------------------------------------- | ------------------------------------------ |
| `format`   | `"on [$symbol($subscription)]($style) "` | The format for the Azure module to render. |
| `symbol`   | `"ﴃ "`                                   | The symbol used in the format.             |
| `style`    | `"blue bold"`                            | The style used in the format.              |
| `disabled` | `true`                                   | Disables the `azure` module.               |

### Ví dụ

```toml
# ~/.config/starship.toml

[azure]
disabled = false
format = "on [$symbol($subscription)]($style) "
symbol = "ﴃ "
style = "blue bold"
```

## Battery

The `battery` module shows how charged the device's battery is and its current charging status. The module is only visible when the device's battery is below 10%.

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                          | Mô tả                                                    |
| -------------------- | --------------------------------- | -------------------------------------------------------- |
| `full_symbol`        | `" "`                            | Kí hiệu cho biết khi pin đầy.                            |
| `charging_symbol`    | `" "`                            | Kí hiệu cho biết khi ping đang sạc.                      |
| `discharging_symbol` | `" "`                            | Kí hiệu cho biết khi pin đang không sạc.                 |
| `unknown_symbol`     | `" "`                            | Kí hiệu cho biết khi trạng thái pin không được xác định. |
| `empty_symbol`       | `" "`                            | Kí hiệu cho biết khi hết pin.                            |
| `format`             | `"[$symbol$percentage]($style) "` | Định dạng cho module.                                    |
| `display`            | [link](#battery-display)          | Ngưỡng hiển thị và kiểu cho module.                      |
| `disabled`           | `false`                           | Vô hiệu `battery` module.                                |

### Ví dụ

```toml
# ~/.config/starship.toml

[battery]
full_symbol = "🔋 "
charging_symbol = "⚡️ "
discharging_symbol = "💀 "
```

### Hiển thị pin

The `display` configuration option is used to define when the battery indicator should be shown (threshold), which symbol would be used (symbol), and what it would like (style). If no `display` is provided. The default is as shown:

```toml
[[battery.display]]
threshold = 10
style = "bold red"
```

The default value for the `charging_symbol` and `discharging_symbol` option is respectively the value of `battery`'s `charging_symbol` and `discharging_symbol` option.

#### Các tuỳ chọn

The `display` option is an array of the following table.

| Tuỳ chọn             | Mặc định   | Mô tả                                                                                                     |
| -------------------- | ---------- | --------------------------------------------------------------------------------------------------------- |
| `threshold`          | `10`       | Cận trên cho tuỳ chọn hiển thị.                                                                           |
| `style`              | `bold red` | Kiểu sử dụng nếu tuỳ chọn hiển thị được sử dụng bên trong.                                                |
| `charging_symbol`    | `-`        | Optional symbol displayed if display option is in use, defaults to battery's `charging_symbol` option.    |
| `discharging_symbol` | `-`        | Optional symbol displayed if display option is in use, defaults to battery's `discharging_symbol` option. |

#### Ví dụ

```toml
[[battery.display]]  # "bold red" style and discharging_symbol when capacity is between 0% and 10%
threshold = 10
style = "bold red"

[[battery.display]]  # "bold yellow" style and 💦 symbol when capacity is between 10% and 30%
threshold = 30
style = "bold yellow"
discharging_symbol = "💦"

# when capacity is over 30%, the battery indicator will not be displayed

```

## Character

The `character` module shows a character (usually an arrow) beside where the text is entered in your terminal.

The character will tell you whether the last command was successful or not. It can do this in two ways:

- thay đổi màu(`đỏ`/`xanh lá`)
- thay đổi hình dạng (`❯`/`✖`)

By default it only changes color. If you also want to change its shape take a look at [this example](#with-custom-error-shape).

::: cảnh báo

`error_symbol` is not supported on nu shell.

:::

::: cảnh báo

`vicmd_symbol` is only supported in cmd, fish and zsh.

:::

### Các tuỳ chọn

| Tuỳ chọn         | Mặc định            | Mô tả                                                                                |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------ |
| `format`         | `"$symbol "`        | Định dạng chuỗi sử dụng trước văn bản nhập vào.                                      |
| `success_symbol` | `"[❯](bold green)"` | Định dạng chuỗi sửa dụng trước văn bản nhập vào nếu câu lệnh trước đó đã thành công. |
| `error_symbol`   | `"[❯](bold red)"`   | Định dạng chuỗi sửa dụng trước văn bản nhập vào nếu câu lệnh trước đó đã thất bại.   |
| `vicmd_symbol`   | `"[❮](bold green)"` | Định dạng chuỗi sửa dụng trước văn bản nhập vào nếu shell trong chế độ vim normal.   |
| `disabled`       | `false`             | Vô hiệu module `character`.                                                          |

### Các biến

| Biến   | Ví dụ | Mô tả                                                                         |
| ------ | ----- | ----------------------------------------------------------------------------- |
| symbol |       | Một phản ánh của một trong `success_symbol`, `error_symbol` or `vicmd_symbol` |

### Các vị dụ

#### Có tuỳ chỉnh hình dạng lỗi

```toml
# ~/.config/starship.toml

[character]
success_symbol = "[➜](bold green) "
error_symbol = "[✗](bold red) "
```

#### Không có tuỳ chỉnh hình dạng lỗi

```toml
# ~/.config/starship.toml

[character]
success_symbol = "[➜](bold green) "
error_symbol = "[➜](bold red) "
```

#### Có tuỳ chỉnh hình dạng vim

```toml
# ~/.config/starship.toml

[character]
vicmd_symbol = "[V](bold green) "
```

## CMake

The `cmake` module shows the currently installed version of [CMake](https://cmake.org/). By default the module will be activated if any of the following conditions are met:

- Đường dẫn hiện tại chứa một tập tin `CmakeLists.txt`
- Đường dẫn hiện tại chứa một tập tin `CMakeCache.txt`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                               | Mô tả                                                                     |
| ------------------- | -------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`   | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                            | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"△ "`                                 | Kí hiệu sử dụng trước phiên bản của cmake.                                |
| `detect_extensions` | `[]`                                   | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này                        |
| `detect_files`      | `["CMakeLists.txt", "CMakeCache.txt"]` | Tên tệp nào sẽ kích hoạt mô-đun này                                       |
| `detect_folders`    | `[]`                                   | Thư mục nào sẽ kích hoạt mô-đun này                                       |
| `style`             | `"bold blue"`                          | Kiểu cho module.                                                          |
| `disabled`          | `false`                                | Vô hiệu module `cmake`.                                                   |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v3.17.3` | Phiên bản của cmake              |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

## COBOL / GNUCOBOL

The `cobol` module shows the currently installed version of COBOL. By default, the module will be shown if any of the following conditions are met:

- The current directory contains any files ending in `.cob` or `.COB`
- The current directory contains any files ending in `.cbl` or `.CBL`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `symbol`            | `"⚙️ "`                              | The symbol used before displaying the version of COBOL.                   |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `style`             | `"bold blue"`                        | Kiểu cho module.                                                          |
| `detect_extensions` | `["cbl", "cob", "CBL", "COB"]`       | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[]`                                 | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `disabled`          | `false`                              | Disables the `cobol` module.                                              |

### Các biến

| Biến      | Ví dụ      | Mô tả                            |
| --------- | ---------- | -------------------------------- |
| version   | `v3.1.2.0` | The version of `cobol`           |
| symbol    |            | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |            | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

## Command Duration

The `cmd_duration` module shows how long the last command took to execute. The module will be shown only if the command took longer than two seconds, or the `min_time` config value, if it exists.

::: warning Do not hook the DEBUG trap in Bash

If you are running Starship in `bash`, do not hook the `DEBUG` trap after running `eval $(starship init $0)`, or this module **will** break.

:::

Bash users who need preexec-like functionality can use [rcaloras's bash_preexec framework](https://github.com/rcaloras/bash-preexec). Simply define the arrays `preexec_functions` and `precmd_functions` before running `eval $(starship init $0)`, and then proceed as normal.

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                      | Mô tả                                                                  |
| -------------------- | ----------------------------- | ---------------------------------------------------------------------- |
| `min_time`           | `2_000`                       | Khoảng thời gian ngắn nhất để hiện thời gian (tính bằng milliseconds). |
| `show_milliseconds`  | `false`                       | Hiện milliseconds.                                                     |
| `format`             | `"took [$duration]($style) "` | Định dạng cho module.                                                  |
| `style`              | `"bold yellow"`               | Kiểu cho module.                                                       |
| `disabled`           | `false`                       | Vô hiệu module `cmd_duration`.                                         |
| `show_notifications` | `false`                       | Hiện thông báo desktop khi câu lệnh hoàn thành.                        |
| `min_time_to_notify` | `45_000`                      | Khoảng thời gian ngắn nhất để thông báo (tính bằng milliseconds).      |

::: tip

Showing desktop notifications requires starship to be built with `notify-rust` support. You check if your starship supports notifications by running `STARSHIP_LOG=debug starship module cmd_duration -d 60000` when `show_notifications` is set to `true`.

:::

### Các biến

| Biến      | Ví dụ    | Mô tả                                 |
| --------- | -------- | ------------------------------------- |
| duration  | `16m40s` | Thời gian nó lấy để thực thi câu lệnh |
| style\* |          | Giá trị ghi đè của `style`            |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[cmd_duration]
min_time = 500
format = "underwent [$duration](bold yellow)"
```

## Conda

The `conda` module shows the current [Conda](https://docs.conda.io/en/latest/) environment, if `$CONDA_DEFAULT_ENV` is set.

::: tip

This does not suppress conda's own prompt modifier, you may want to run `conda config --set changeps1 False`.

:::

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                               | Mô tả                                                                                                                                                                                                       |
| ------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `truncation_length` | `1`                                    | Số lượng đường dẫn của biến môi trường nên được cắt bớt, nếu biến môi trường được tạo thông qua via `conda create -p [path]`. `0` nghĩa là không cắt bớt. Cũng thấy trong module [`directory`](#directory). |
| `symbol`            | `"🅒 "`                                 | Kí hiệu sử dụng trước tên biến môi trường.                                                                                                                                                                  |
| `style`             | `"bold green"`                         | Kiểu cho module.                                                                                                                                                                                            |
| `format`            | `"via [$symbol$environment]($style) "` | Định dạng cho module.                                                                                                                                                                                       |
| `ignore_base`       | `true`                                 | Bỏ qua biến môi trường `base` khi đã kích hoạt.                                                                                                                                                             |
| `disabled`          | `false`                                | Vô hiệu module `conda`.                                                                                                                                                                                     |

### Các biến

| Biến        | Ví dụ        | Mô tả                              |
| ----------- | ------------ | ---------------------------------- |
| environment | `astronauts` | Biến môi trường hiện tại của conda |
| symbol      |              | Giá trị ghi đè tuỳ chọn `symbol`   |
| style\*   |              | Giá trị ghi đè của `style`         |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[conda]
format = "[$symbol$environment](dimmed green) "
```

## Crystal

The `crystal` module shows the currently installed version of [Crystal](https://crystal-lang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `shard.yml`
- Đường dẫn hiện tại chứa một tập tin `.cr`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `symbol`            | `"🔮 "`                               | Kí hiệu sử dụng trước phiên bản hiển thị của crystal.                     |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `style`             | `"bold red"`                         | Kiểu cho module.                                                          |
| `detect_extensions` | `["cr"]`                             | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["shard.yml"]`                      | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `disabled`          | `false`                              | Vô hiệu hoá module `crystal`.                                             |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v0.32.1` | Phiên bản của `crystal`          |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[crystal]
format = "via [✨ $version](bold blue) "
```

## Dart

The `dart` module shows the currently installed version of [Dart](https://dart.dev/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin với phần mở rộng `.dart`
- Đường dẫn hiện tại chứa một đường dẫn `.dart_tool`
- Đường dẫn hiện tại chứa một tệp tin `pubspec.yaml`, `pubspec.yml` hoặc `pubspec.lock`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                          | Mô tả                                                                     |
| ------------------- | ------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`              | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                       | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🎯 "`                                            | Một chuỗi định dạng hiển thị biểu tượng của Dart                          |
| `detect_extensions` | `["dart"]`                                        | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["pubspec.yaml", "pubspec.yml", "pubspec.lock"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[".dart_tool"]`                                  | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold blue"`                                     | Kiểu cho module.                                                          |
| `disabled`          | `false`                                           | Vô hiệu `dart` module.                                                    |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v2.8.4` | Phiên bản của `dart`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[dart]
format = "via [🔰 $version](bold red) "
```

## Deno

The `deno` module shows you your currently installed version of [Deno](https://deno.land/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:
- The current directory contains a `deno.json`, `deno.jsonc`, `mod.ts`, `mod.js`, `deps.ts` or `deps.js` file

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                                | Mô tả                                                                     |
| ------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`                                    | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                                             | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🦕 "`                                                                  | A format string representing the symbol of Deno                           |
| `detect_extensions` | `[]`                                                                    | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["deno.json", "deno.jsonc", "mod.ts", "mod.js", "deps.ts", "deps.js"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                                                    | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"green bold"`                                                          | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                                 | Disables the `deno` module.                                               |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v1.8.3` | The version of `deno`            |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

### Ví dụ

```toml
# ~/.config/starship.toml

[deno]
format = "via [🦕 $version](green bold) "
```

## Đường dẫn

The `directory` module shows the path to your current directory, truncated to three parent folders. Your directory will also be truncated to the root of the git repo that you're currently in.

When using the fish style pwd option, instead of hiding the path that is truncated, you will see a shortened name of each directory based on the number you enable for the option.

For example, given `~/Dev/Nix/nixpkgs/pkgs` where `nixpkgs` is the repo root, and the option set to `1`. You will now see `~/D/N/nixpkgs/pkgs`, whereas before it would have been `nixpkgs/pkgs`.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                           | Mô tả                                                                                   |
| ------------------- | -------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `truncation_length` | `3`                                                | Số lượng thư mục cha của thư mục hiện tại nên được rút gọn.                             |
| `truncate_to_repo`  | `true`                                             | Có hoặc không rút gọn đường dẫn gốc của git repo hiện tại của bạn.                      |
| `format`            | `"[$path]($style)[$read_only]($read_only_style) "` | Định dạng cho module.                                                                   |
| `style`             | `"bold cyan"`                                      | Kiểu cho module.                                                                        |
| `disabled`          | `false`                                            | Vô hiệu mô đun `directory`.                                                             |
| `read_only`         | `"🔒"`                                              | Biểu tượng để nhận biết thư mục hiện tại là chỉ đọc.                                    |
| `read_only_style`   | `"red"`                                            | Style cho biểu tượng chỉ đọc.                                                           |
| `truncation_symbol` | `""`                                               | Biểu tượng tiền tố cho các đường dẫn rút gọn. ví dụ: "…/"                               |
| `repo_root_style`   | `None`                                             | The style for the root of the git repo when `truncate_to_repo` option is set to false.  |
| `home_symbol`       | `"~"`                                              | Biểu tượng nhận biết thư mục home.                                                      |
| `use_os_path_sep`   | `true`                                             | Use the OS specific path seperator instead of always using `/` (e.g. `\` on Windows) |

<details>
<summary>This module has a few advanced configuration options that control how the directory is displayed.</summary>

| Tùy chọn nâng cao           | Mặc định | Mô tả                                                                                                                                                                  |
| --------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `substitutions`             |          | A table of substitutions to be made to the path.                                                                                                                       |
| `fish_style_pwd_dir_length` | `0`      | The number of characters to use when applying fish shell pwd path logic.                                                                                               |
| `use_logical_path`          | `true`   | If `true` render the logical path sourced from the shell via `PWD` or `--logical-path`. If `false` instead render the physical filesystem path with symlinks resolved. |

`substitutions` allows you to define arbitrary replacements for literal strings that occur in the path, for example long network prefixes or development directories (i.e. Java). Note that this will disable the fish style PWD.

```toml
[directory.substitutions]
"/Volumes/network/path" = "/net"
"src/com/long/java/path" = "mypath"
```

`fish_style_pwd_dir_length` interacts with the standard truncation options in a way that can be surprising at first: if it's non-zero, the components of the path that would normally be truncated are instead displayed with that many characters. For example, the path `/built/this/city/on/rock/and/roll`, which would normally be displayed as as `rock/and/roll`, would be displayed as `/b/t/c/o/rock/and/roll` with `fish_style_pwd_dir_length = 1`--the path components that would normally be removed are displayed with a single character. For `fish_style_pwd_dir_length = 2`, it would be `/bu/th/ci/on/rock/and/roll`.

</details>

### Các biến

| Biến      | Ví dụ                 | Mô tả                      |
| --------- | --------------------- | -------------------------- |
| path      | `"D:/Projects"`       | Đường dẫn thư mục hiện tại |
| style\* | `"black bold dimmed"` | Giá trị ghi đè của `style` |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[directory]
truncation_length = 8
truncation_symbol = "…/"
```

## Docker Context

The `docker_context` module shows the currently active [Docker context](https://docs.docker.com/engine/context/working-with-contexts/) if it's not set to `default` or if the `DOCKER_MACHINE_NAME`, `DOCKER_HOST` or `DOCKER_CONTEXT` environment variables are set (as they are meant to override the context in use).

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                      | Mô tả                                                                                    |
| ------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `format`            | `"via [$symbol$context]($style) "`                            | Định dạng cho module.                                                                    |
| `symbol`            | `"🐳 "`                                                        | Biểu tượng sử dụng để hiển thị trước Docker context.                                     |
| `only_with_files`   | `true`                                                        | Chỉ hiển thị khi có một tệp tin khớp                                                     |
| `detect_extensions` | `[]`                                                          | Các mở rộng nào nên kích hoạt mô đun này (cần `only_with_files` thiết lập là true).      |
| `detect_files`      | `["docker-compose.yml", "docker-compose.yaml", "Dockerfile"]` | Tên tệp tin nào nên kích hoạt mô đun này (cần `only_with_files` được thiết lập là true). |
| `detect_folders`    | `[]`                                                          | Thư mục nào nên kích hoạt mô đun này (cần `only_with_files` được thiết lập là true).     |
| `style`             | `"blue bold"`                                                 | Kiểu cho module.                                                                         |
| `disabled`          | `false`                                                       | Vô hiệu mô đun `docker_context`.                                                         |

### Các biến

| Biến      | Ví dụ          | Mô tả                            |
| --------- | -------------- | -------------------------------- |
| context   | `test_context` | Docker context hiện tại          |
| symbol    |                | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |                | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[docker_context]
format = "via [🐋 $context](blue bold)"
```

## Dotnet

The `dotnet` module shows the relevant version of the [.NET Core SDK](https://dotnet.microsoft.com/) for the current directory. If the SDK has been pinned in the current directory, the pinned version is shown. Otherwise the module shows the latest installed version of the SDK.

By default this module will only be shown in your prompt when one or more of the following files are present in the current directory:

- `global.json`
- `project.json`
- `Directory.Build.props`
- `Directory.Build.targets`
- `Packages.props`
- `*.csproj`
- `*.fsproj`
- `*.xproj`

You'll also need the .NET Core SDK installed in order to use it correctly.

Internally, this module uses its own mechanism for version detection. Typically it is twice as fast as running `dotnet --version`, but it may show an incorrect version if your .NET project has an unusual directory layout. If accuracy is more important than speed, you can disable the mechanism by setting `heuristic = false` in the module options.

The module will also show the Target Framework Moniker (<https://docs.microsoft.com/en-us/dotnet/standard/frameworks#supported-target-framework-versions>) when there is a csproj file in the current directory.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                                                                | Mô tả                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )(🎯 $tfm )]($style)"`                                                           | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                                                                             | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `".NET "`                                                                                               | Biểu tượng sử dụng để hiển thị trước phiên bản của dotnet.                |
| `heuristic`         | `true`                                                                                                  | Sử dụng phiên bản phát hiện thông minh hơn.                               |
| `detect_extensions` | `["csproj", "fsproj", "xproj"]`                                                                         | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["global.json", "project.json", "Directory.Build.props", "Directory.Build.targets", "Packages.props"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                                                                                    | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `style`             | `"bold blue"`                                                                                           | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                                                                 | Vô hiệu mô đun `dotnet`.                                                  |

### Các biến

| Biến      | Ví dụ            | Mô tả                                                         |
| --------- | ---------------- | ------------------------------------------------------------- |
| version   | `v3.1.201`       | Phiên bản của `dotnet` sdk                                    |
| tfm       | `netstandard2.0` | Target Framework Monike của dự án hiện tại đang được nhắm đến |
| symbol    |                  | Giá trị ghi đè tuỳ chọn `symbol`                              |
| style\* |                  | Giá trị ghi đè của `style`                                    |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[dotnet]
symbol = "🥅 "
style = "green"
heuristic = false
```

## Elixir

The `elixir` module shows the currently installed version of [Elixir](https://elixir-lang.org/) and [Erlang/OTP](https://erlang.org/doc/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `mix.exs`.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                    | Mô tả                                                                     |
| ------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `'via [$symbol($version \(OTP $otp_version\) )]($style)'` | Định dạng cho module elixir.                                              |
| `version_format`    | `"v${raw}"`                                                 | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"💧 "`                                                      | Kí hiệu sử dụng trước phiên bản hiển thị của Elixir/Erlang.               |
| `detect_extensions` | `[]`                                                        | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["mix.exs"]`                                               | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                                        | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `style`             | `"bold purple"`                                             | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                     | Vô hiệu mô đun `elixir`.                                                  |

### Các biến

| Biến        | Ví dụ   | Mô tả                            |
| ----------- | ------- | -------------------------------- |
| version     | `v1.10` | Phiên bản của `elixir`           |
| otp_version |         | Phiên bản otp của `elixir`       |
| symbol      |         | Giá trị ghi đè tuỳ chọn `symbol` |
| style\*   |         | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[elixir]
symbol = "🔮 "
```

## Elm

The `elm` module shows the currently installed version of [Elm](https://elm-lang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `elm.json`
- Đường dẫn hiện tại chứa một tập tin `elm-package.json`
- Đường dẫn hiện tại chứa một tệp tin `.elm-version`
- Đường dẫn hiện tại chứa một thư mục `elm-stuff`
- Đường dẫn hiện tại chứa một tập tin `*.elm`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                           | Mô tả                                                                     |
| ------------------- | -------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`               | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                        | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🌳 "`                                             | Một format string đại diện cho biểu tượng của Elm.                        |
| `detect_extensions` | `["elm"]`                                          | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["elm.json", "elm-package.json", ".elm-version"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `["elm-stuff"]`                                    | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `style`             | `"cyan bold"`                                      | Kiểu cho module.                                                          |
| `disabled`          | `false`                                            | Vô hiệu mô đun `elm`.                                                     |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v0.19.1` | Phiên bản của `elm`              |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[elm]
format = "via [ $version](cyan bold) "
```

## Biến môi trường

The `env_var` module displays the current value of a selected environment variables. The module will be shown only if any of the following conditions are met:

- Tùy chọn `variable` khớp với mootjj biến môi trường tồn tại
- Tùy chọn `variable` không được định nghĩa, nhưng tùy chọn `default` là


::: tip Multiple environmental variables can be displayed by using a `.`. (see example) If the `variable` configuration option is not set, the module will display value of variable under the name of text after the `.` character.

Example: following configuration will display value of USER environment variable
```toml
# ~/.config/starship.toml

[env_var.USER]
default = "unknown user"
```
:::

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định                       | Mô tả                                                                    |
| ---------- | ------------------------------ | ------------------------------------------------------------------------ |
| `symbol`   | `""`                           | Biểu tượng sử dụng để hiển thị trước giá trị của biến.                   |
| `variable` |                                | Biến môi trường được hiển thị.                                           |
| `default`  |                                | Giá trị mặc định được hiển thị khi biến được chọn không được định nghĩa. |
| `format`   | `"with [$env_value]($style) "` | Định dạng cho module.                                                    |
| `disabled` | `false`                        | Vô hiệu `env_var`.                                                       |

### Các biến

| Biến      | Ví dụ                                     | Mô tả                                           |
| --------- | ----------------------------------------- | ----------------------------------------------- |
| env_value | `Windows NT` (nếu _variable_ sẽ là `$OS`) | Giá trị biến môi trường của tùy chọn `variable` |
| symbol    |                                           | Giá trị ghi đè tuỳ chọn `symbol`                |
| style\* | `black bold dimmed`                       | Giá trị ghi đè của `style`                      |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[env_var]
variable = "SHELL"
default = "unknown shell"
```

Displaying multiple environmental variables:
```toml
# ~/.config/starship.toml

[env_var.SHELL]
variable = "SHELL"
default = "unknown shell"
[env_var.USER]
default = "unknown user"
```

## Erlang

The `erlang` module shows the currently installed version of [Erlang/OTP](https://erlang.org/doc/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `rebar.config`.
- Đường dẫn hiện tại chứa một tập tin `erlang.mk`.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `" "`                               | Biểu tượng sử dụng để hiển thị trước phiên bản của erlang.                |
| `style`             | `"bold red"`                         | Kiểu cho module.                                                          |
| `detect_extensions` | `[]`                                 | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["rebar.config", "elang.mk"]`       | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `disabled`          | `false`                              | Vô hiệu mô đun `erlang`.                                                  |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v22.1.3` | The version of `erlang`          |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[erlang]
format = "via [e $version](bold red) "
```

## Fill

The `fill` module fills any extra space on the line with a symbol. If multiple `fill` modules are present in a line they will split the space evenly between them. This is useful for aligning other modules.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định       | Mô tả                             |
| ---------- | -------------- | --------------------------------- |
| `symbol`   | `"."`          | The symbol used to fill the line. |
| `style`    | `"bold black"` | Kiểu cho module.                  |
| `disabled` | `false`        | Disables the `fill` module        |

### Ví dụ

```toml
# ~/.config/starship.toml
format="AA $fill BB $fill CC"

[fill]
symbol = "-"
style = "bold green"
```

Produces a prompt that looks like:

```
AA -------------------------------------------- BB -------------------------------------------- CC

```

## Google Cloud (`gcloud`)

The `gcloud` module shows the current configuration for [`gcloud`](https://cloud.google.com/sdk/gcloud) CLI. This is based on the `~/.config/gcloud/active_config` file and the `~/.config/gcloud/configurations/config_{CONFIG NAME}` file and the `CLOUDSDK_CONFIG` env var.

### Các tuỳ chọn

| Tuỳ chọn         | Mặc định                                                   | Mô tả                                                             |
| ---------------- | ---------------------------------------------------------- | ----------------------------------------------------------------- |
| `format`         | `'on [$symbol$account(@$domain)(\($region\))]($style) '` | Định dạng cho module.                                             |
| `symbol`         | `"☁️  "`                                                   | Kí hiệu sử dụng hiển thị trước profile GCP hiện tại.              |
| `region_aliases` |                                                            | Bảng ánh xạ của các bí danh của region để hiển thị ngoài tên GCP. |
| `style`          | `"bold blue"`                                              | Kiểu cho module.                                                  |
| `disabled`       | `false`                                                    | Vô hiệu mô đun `gcloud`.                                          |

### Các biến

| Biến      | Ví dụ         | Mô tả                                                                |
| --------- | ------------- | -------------------------------------------------------------------- |
| region    | `us-central1` | Region GCP hiện tại                                                  |
| account   | `foo`         | Profile hiện tại của GCP                                             |
| domain    | `example.com` | The current GCP profile domain                                       |
| project   |               | Dự án hiện tại của GCP                                               |
| active    | `default`     | Tên cấu hình có hiệu lực viết trong `~/.config/gcloud/active_config` |
| symbol    |               | Giá trị ghi đè tuỳ chọn `symbol`                                     |
| style\* |               | Giá trị ghi đè của `style`                                           |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Các vị dụ

#### Hiển thị tài khoản và dự án

```toml
# ~/.config/starship.toml

[gcloud]
format = 'on [$symbol$account(@$domain)(\($project\))]($style) '
```

#### Chỉ hiển thị tên cấu hình hiệu lực

```toml
# ~/.config/starship.toml

[gcloud]
format = "[$symbol$active]($style) "
style = "bold yellow"
```

#### Hiển thị tài khoản và bí danh khu vực

```toml
# ~/.config/starship.toml

[gcloud]
symbol = "️🇬️ "
[gcloud.region_aliases]
us-central1 = "uc1"
asia-northeast1 = "an1"
```

## Git Branch

The `git_branch` module shows the active branch of the repo in your current directory.

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                         | Mô tả                                                                                                 |
| -------------------- | -------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `always_show_remote` | `false`                          | Hiển thị tên nhánh remote tracking, thậm chí nếu nó bằng với tên nhánh local.                         |
| `format`             | `"on [$symbol$branch]($style) "` | Định dạng cho module. Sử dụng `"$branch"` để tham chiếu tới tên nhánh hiện tại.                       |
| `symbol`             | `" "`                           | Một chuỗi định dạng hiển thị biểu tượng của nhánh git.                                                |
| `style`              | `"bold purple"`                  | Kiểu cho module.                                                                                      |
| `truncation_length`  | `2^63 - 1`                       | Truncates a git branch to `N` graphemes.                                                              |
| `truncation_symbol`  | `"…"`                            | Biểu tượng sử dụng để nhận biết một tên nhánh được rút gọn. Bạn có thể sử dụng `""` để ẩn biểu tượng. |
| `only_attached`      | `false`                          | Only show the branch name when not in a detached `HEAD` state.                                        |
| `disabled`           | `false`                          | Vô hiệu mô đun `git_branch`.                                                                          |

### Các biến

| Biến          | Ví dụ    | Mô tả                                                                                                  |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------ |
| branch        | `master` | The current branch name, falls back to `HEAD` if there's no current branch (e.g. git detached `HEAD`). |
| remote_name   | `origin` | Tên remote.                                                                                            |
| remote_branch | `master` | Tên của nhánh đã theo dõi trên `remote_name`.                                                          |
| symbol        |          | Giá trị ghi đè tuỳ chọn `symbol`                                                                       |
| style\*     |          | Giá trị ghi đè của `style`                                                                             |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[git_branch]
symbol = "🌱 "
truncation_length = 4
truncation_symbol = ""
```

## Git Commit

The `git_commit` module shows the current commit hash and also the tag (if any) of the repo in your current directory.

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                           | Mô tả                                                     |
| -------------------- | ---------------------------------- | --------------------------------------------------------- |
| `commit_hash_length` | `7`                                | Độ dài của git commit hash được hiển thị.                 |
| `format`             | `"[\\($hash$tag\\)]($style) "` | Định dạng cho module.                                     |
| `style`              | `"bold green"`                     | Kiểu cho module.                                          |
| `only_detached`      | `true`                             | Only show git commit hash when in detached `HEAD` state   |
| `tag_disabled`       | `true`                             | Vô hiệu hiển thị thông tin tag trong mô đun `git_commit`. |
| `tag_symbol`         | `" 🏷 "`                            | Biểu tượng tag trước thông tin được hiển thị              |
| `disabled`           | `false`                            | Vô hiệu mô đun `git_commit`.                              |

### Các biến

| Biến      | Ví dụ     | Mô tả                      |
| --------- | --------- | -------------------------- |
| hash      | `b703eb3` | Git commit hash hiện tại   |
| style\* |           | Giá trị ghi đè của `style` |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[git_commit]
commit_hash_length = 4
tag_symbol = "🔖 "
```

## Git State

The `git_state` module will show in directories which are part of a git repository, and where there is an operation in progress, such as: _REBASING_, _BISECTING_, etc. If there is progress information (e.g., REBASING 3/10), that information will be shown too.

### Các tuỳ chọn

| Tuỳ chọn       | Mặc định                                                        | Mô tả                                                                              |
| -------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `rebase`       | `"REBASING"`                                                    | Một format sring hiển thị khi một `rebase` đang trong quá trình.                   |
| `merge`        | `"MERGING"`                                                     | Một format sring hiển thị khi một `merge` đang trong quá trình.                    |
| `revert`       | `"REVERTING"`                                                   | Một format sring hiển thị khi một `revert` đang trong quá trình.                   |
| `cherry_pick`  | `"CHERRY-PICKING"`                                              | Một format sring hiển thị khi một `cherry-pick` đang trong quá trình.              |
| `bisect`       | `"BISECTING"`                                                   | Một format sring hiển thị khi một `bisect` đang trong quá trình.                   |
| `am`           | `"AM"`                                                          | Một format sring hiển thị khi một `apply-mailbox` (`git am`) đang trong quá trình. |
| `am_or_rebase` | `"AM/REBASE"`                                                   | Một format sring hiển thị khi một `apply-mailbox` (`rebase`) đang trong quá trình. |
| `style`        | `"bold yellow"`                                                 | Kiểu cho module.                                                                   |
| `format`       | `'\([$state( $progress_current/$progress_total)]($style)\) '` | Định dạng cho module.                                                              |
| `disabled`     | `false`                                                         | Vô hiệu `git_state` module.                                                        |

### Các biến

| Biến             | Ví dụ      | Mô tả                             |
| ---------------- | ---------- | --------------------------------- |
| state            | `REBASING` | Trạng thái của repo hiện tại      |
| progress_current | `1`        | Trạng thái của quá trình hiện tại |
| progress_total   | `2`        | Tổng số các quá trình             |
| style\*        |            | Giá trị ghi đè của `style`        |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[git_state]
format = '[\($state( $progress_current of $progress_total)\)]($style) '
cherry_pick = "[🍒 PICKING](bold red)"
```

## Git Metrics

The `git_metrics` module will show the number of added and deleted lines in the current git repository.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                                                     | Mô tả                                 |
| -------------------- | ------------------------------------------------------------ | ------------------------------------- |
| `added_style`        | `"bold green"`                                               | The style for the added count.        |
| `deleted_style`      | `"bold red"`                                                 | The style for the deleted count.      |
| `only_nonzero_diffs` | `true`                                                       | Render status only for changed items. |
| `format`             | `'([+$added]($added_style) )([-$deleted]($deleted_style) )'` | Định dạng cho module.                 |
| `disabled`           | `true`                                                       | Disables the `git_metrics` module.    |

### Các biến

| Biến              | Ví dụ | Mô tả                                       |
| ----------------- | ----- | ------------------------------------------- |
| added             | `1`   | The current number of added lines           |
| deleted           | `2`   | The current number of deleted lines         |
| added_style\*   |       | Mirrors the value of option `added_style`   |
| deleted_style\* |       | Mirrors the value of option `deleted_style` |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[git_metrics]
added_style = "bold blue"
format = '[+$added]($added_style)/[-$deleted]($deleted_style) '
```

## Git Status

The `git_status` module shows symbols representing the state of the repo in your current directory.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                        | Mô tả                               |
| ------------------- | ----------------------------------------------- | ----------------------------------- |
| `format`            | `'([\[$all_status$ahead_behind\]]($style) )'` | Định dạng mặc định cho `git_status` |
| `conflicted`        | `"="`                                           | Nhánh này có nhiều merge conflicts. |
| `ahead`             | `"⇡"`                                           | Định dạng của `ahead`               |
| `behind`            | `"⇣"`                                           | Định dạng của `behind`              |
| `diverged`          | `"⇕"`                                           | Định dạng của `diverged`            |
| `up_to_date`        | `""`                                            | The format of `up_to_date`          |
| `untracked`         | `"?"`                                           | Định dạng của `untracked`           |
| `stashed`           | `"$"`                                           | Định dạng của `stashed`             |
| `modified`          | `"!"`                                           | Định dạng của `modified`            |
| `staged`            | `"+"`                                           | Định dạng của `modified`            |
| `renamed`           | `"»"`                                           | Định dạng của `renamed`             |
| `deleted`           | `"✘"`                                           | Định dạng của `deleted`             |
| `style`             | `"bold red"`                                    | Kiểu cho module.                    |
| `ignore_submodules` | `false`                                         | Ignore changes to submodules.       |
| `disabled`          | `false`                                         | Disables the `git_status` module.   |

### Các biến

The following variables can be used in `format`:

| Biến           | Mô tả                                                                                                         |
| -------------- | ------------------------------------------------------------------------------------------------------------- |
| `all_status`   | Shortcut cho `$conflicted$stashed$deleted$renamed$modified$staged$untracked`                                  |
| `ahead_behind` | Displays `diverged`, `ahead`, `behind` or `up_to_date` format string based on the current status of the repo. |
| `conflicted`   | Hiển thị `conflicted` khi nhánh này có merge conflicts.                                                       |
| `untracked`    | Hiển thị `untracked` khi có tệp tin untracked trong thư mục làm việc.                                         |
| `stashed`      | Hiển thị `stashed` khi một stash tồn tại trong local repository.                                              |
| `modified`     | Hiển thị `modified` khi có tệp tin được chỉnh sửa trong thư mục làm việc.                                     |
| `staged`       | Hiển thị `staged` khi một tệp tin mới được thêm vào staging area.                                             |
| `renamed`      | Hiển thị `renamed` khi một tệp tin đổi tên đã được thêm vào staging area.                                     |
| `deleted`      | Hiển thị `deleted` khi một tệp tin bị xóa đã được thêm vào staging area.                                      |
| style\*      | Giá trị ghi đè của `style`                                                                                    |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

The following variables can be used in `diverged`:

| Biến           | Mô tả                                         |
| -------------- | --------------------------------------------- |
| `ahead_count`  | Số lượng commit phía trước của nhánh tracking |
| `behind_count` | Số lượng commit phía sau nhánh tracking       |

The following variables can be used in `conflicted`, `ahead`, `behind`, `untracked`, `stashed`, `modified`, `staged`, `renamed` and `deleted`:

| Biến    | Mô tả                         |
| ------- | ----------------------------- |
| `count` | Hiển thị số lượng các tệp tin |

### Ví dụ

```toml
# ~/.config/starship.toml

[git_status]
conflicted = "🏳"
ahead = "🏎💨"
behind = "😰"
diverged = "😵"
up_to_date = "✓"
untracked = "🤷‍"
stashed = "📦"
modified = "📝"
staged = '[++\($count\)](green)'
renamed = "👅"
deleted = "🗑"
```

Show ahead/behind count of the branch being tracked

```toml
# ~/.config/starship.toml

[git_status]
ahead = "⇡${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
behind = "⇣${count}"
```

## Go

The `golang` module shows the currently installed version of [Go](https://golang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Thư mục hiện tại chứa một tập tin `go.mod`
- Đường dẫn hiện tại chứa một tập tin `go.sum`
- Thư mục hiện tại chứa một tập tin `glide.yaml`
- Thư mục hiện tại chứa một tập tin `Gopkg.yml`
- Đường dẫn hiện tại chứa một tập tin `Gopkg.lock`
- Thư mục hiện tại chứa một tệp tin `.go-version`
- Thư mục hiện tại chứa một thư mục `Godeps`
- Thư mục hiện tại chứa một tệp tin với phần mở rộng `.go`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                                       | Mô tả                                                                     |
| ------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`                                           | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                                                    | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🐹 "`                                                                         | Một format string đại diện cho biểu tượng của Go.                         |
| `detect_extensions` | `["go"]`                                                                       | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["go.mod", "go.sum", "glide.yaml", "Gopkg.yml", "Gopkg.lock", ".go-version"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `["Godeps"]`                                                                   | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold cyan"`                                                                  | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                                        | Vô hiệu `golang` module.                                                  |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v1.12.1` | Phiên bản của `go`               |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[golang]
format = "via [🏎💨 $version](bold cyan) "
```

## Helm

The `helm` module shows the currently installed version of [Helm](https://helm.sh/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `helmfile.yaml`
- Thư mục hiện tại chứa một tập tin `Chart.yaml`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `detect_extensions` | `[]`                                 | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["helmfile.yaml", "Chart.yaml"]`    | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `symbol`            | `"⎈ "`                               | Một format string đại diện cho biểu tượng của Helm.                       |
| `style`             | `"bold white"`                       | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Vô hiệu `helm` module.                                                    |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v3.1.1` | Phiên bản của `helm`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[helm]
format = "via [⎈ $version](bold white) "
```

## Hostname

The `hostname` module shows the system hostname.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định                    | Mô tả                                                                                                                            |
| ---------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `ssh_only` | `true`                      | Chỉ hiển thị hostname khi được kết nối tới một phiên SSH.                                                                        |
| `trim_at`  | `"."`                       | Chuỗi mà hostname được cắt ngắn, sau khi khớp lần đầu tiên. `"."` sẽ dừng sau dấu chấm đầu tiên. `""` sẽ vô hiệu mọi sự cắt ngắn |
| `format`   | `"[$hostname]($style) in "` | Định dạng cho module.                                                                                                            |
| `style`    | `"bold dimmed green"`       | Kiểu cho module.                                                                                                                 |
| `disabled` | `false`                     | Vô hiệu `hastname` module.                                                                                                       |

### Các biến

| Biến      | Ví dụ      | Mô tả                        |
| --------- | ---------- | ---------------------------- |
| hostname  | `computer` | The hostname of the computer |
| style\* |            | Giá trị ghi đè của `style`   |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[hostname]
ssh_only = false
format =  "on [$hostname](bold red) "
trim_at = ".companyname.com"
disabled = false
```

## Java

The `java` module shows the currently installed version of [Java](https://www.oracle.com/java/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Thư mục hiện tại chứa một tệp tin `pom.xml`, `build.gradle.kts`, `build.sbt`, `.java-version`, `.deps.edn`, `project.clj`, or `build.boot`
- Thư mục hiện tại chứa một tệp tin với phần mở rộng `.java`, `.class`, `.gradle`, `.jar`, `.clj`, or `.cljc`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                                                                  | Mô tả                                                                     |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [${symbol}(${version} )]($style)"`                                                                  | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                                                                               | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `detect_extensions` | `["java", "class", "gradle", "jar", "cljs", "cljc"]`                                                      | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["pom.xml", "build.gradle.kts", "build.sbt", ".java-version", ".deps.edn", "project.clj", "build.boot"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                                                                                      | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `symbol`            | `"☕ "`                                                                                                    | Một format string đại diện cho biểu tượng Java                            |
| `style`             | `"red dimmed"`                                                                                            | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                                                                   | Vô hiệu `java` module.                                                    |

### Các biến

| Biến      | Ví dụ | Mô tả                            |
| --------- | ----- | -------------------------------- |
| version   | `v14` | Phiên bản của `java`             |
| symbol    |       | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |       | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[java]
symbol = "🌟 "
```

## Jobs

The `jobs` module shows the current number of jobs running. The module will be shown only if there are background jobs running. The module will show the number of jobs running if there are at least 2 jobs, or more than the `number_threshold` config value, if it exists. The module will show a symbol if there is at least 1 job, or more than the `symbol_threshold` config value, if it exists. You can set both values to 0 in order to *always* show the symbol and number of jobs, even if there are 0 jobs running.

The default functionality is:

- 0 jobs -> Nothing is shown.
- 1 job -> `symbol` is shown.
- 2 jobs or more -> `symbol` + `number` are shown.

::: cảnh báo

This module is not supported on tcsh and nu.

:::

::: cảnh báo

The `threshold` option is deprecated, but if you want to use it, the module will show the number of jobs running if there is more than 1 job, or more than the `threshold` config value, if it exists. If `threshold` is set to 0, then the module will also show when there are 0 jobs running.

:::

### Các tuỳ chọn

| Tuỳ chọn           | Mặc định                      | Mô tả                                                                    |
| ------------------ | ----------------------------- | ------------------------------------------------------------------------ |
| `threshold`\*    | `1`                           | Cho biết số lượng jobs nếu nó vượt quá.                                  |
| `symbol_threshold` | `1`                           | Show `symbol` if the job count is at least `symbol_threshold`.           |
| `number_threshold` | `2`                           | Show the number of jobs if the job count is at least `number_threshold`. |
| `format`           | `"[$symbol$number]($style) "` | Định dạng cho module.                                                    |
| `symbol`           | `"✦"`                         | The string used to represent the `symbol` variable.                      |
| `style`            | `"bold blue"`                 | Kiểu cho module.                                                         |
| `disabled`         | `false`                       | Vô hiệu `jobs` module.                                                   |
 \*: This option is deprecated, please use the 

`number_threshold` and `symbol_threshold` options instead.

### Các biến

| Biến      | Ví dụ | Mô tả                            |
| --------- | ----- | -------------------------------- |
| number    | `1`   | Số lượng job                     |
| symbol    |       | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |       | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[jobs]
symbol = "+ "
number_threshold = 4
symbol_threshold = 0
```

## Julia

The `julia` module shows the currently installed version of [Julia](https://julialang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Thư mục hiện tại chứa một tệp tin `Project.toml`
- Thư mục hiện tại chứa một tập tin `Manifest.toml`
- Thư mục hiện tại chứa một tệp tin với phần mở rộng `.jl`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `detect_extensions` | `["jl"]`                             | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Project.toml", "Manifest.toml"]`  | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `symbol`            | `"ஃ "`                               | Một format string đại diện cho biếu tượng của Julia.                      |
| `style`             | `"bold purple"`                      | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Vô hiệu `julia` module.                                                   |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v1.4.0` | Phiên bản của `julia`            |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[julia]
symbol = "∴ "
```

## Kotlin

The `kotlin` module shows the currently installed version of [Kotlin](https://kotlinlang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Thư mục hiện tại chứa một tệp tin `.kt` hoặc một tệp tin `.kts`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `detect_extensions` | `["kt", "kts"]`                      | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[]`                                 | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `symbol`            | `"🅺 "`                               | Một format string đại diện cho biết tượng của Kotllin.                    |
| `style`             | `"bold blue"`                        | Kiểu cho module.                                                          |
| `kotlin_binary`     | `"kotlin"`                           | Cấu hình kotlin nhị phân mà Starship thực thi khi lấy phiên bản.          |
| `disabled`          | `false`                              | Vô hiệu `kotlin` module.                                                  |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v1.4.21` | Phiên bản của `kotlin`           |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[kotlin]
symbol = "🅺 "
```

```toml
# ~/.config/starship.toml

[kotlin]
# Uses the Kotlin Compiler binary to get the installed version
kotlin_binary = "kotlinc"
```

## Kubernetes

Displays the current [Kubernetes context](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#context) name and, if set, the namespace from the kubeconfig file. The namespace needs to be set in the kubeconfig file, this can be done via `kubectl config set-context starship-cluster --namespace astronaut`. If the `$KUBECONFIG` env var is set the module will use that if not it will use the `~/.kube/config`.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn          | Mặc định                                             | Mô tả                                                                 |
| ----------------- | ---------------------------------------------------- | --------------------------------------------------------------------- |
| `symbol`          | `"☸ "`                                               | A format string representing the symbol displayed before the Cluster. |
| `format`          | `'[$symbol$context( \($namespace\))]($style) in '` | Định dạng cho module.                                                 |
| `style`           | `"cyan bold"`                                        | Kiểu cho module.                                                      |
| `context_aliases` |                                                      | Table of context aliases to display.                                  |
| `disabled`        | `true`                                               | Disables the `kubernetes` module.                                     |

### Các biến

| Biến      | Ví dụ                | Mô tả                                    |
| --------- | -------------------- | ---------------------------------------- |
| context   | `starship-cluster`   | The current kubernetes context           |
| namespace | `starship-namespace` | If set, the current kubernetes namespace |
| symbol    |                      | Giá trị ghi đè tuỳ chọn `symbol`         |
| style\* |                      | Giá trị ghi đè của `style`               |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[kubernetes]
format = 'on [⛵ $context \($namespace\)](dimmed green) '
disabled = false
[kubernetes.context_aliases]
"dev.local.cluster.k8s" = "dev"
".*/openshift-cluster/.*" = "openshift"
"gke_.*_(?P<cluster>[\\w-]+)" = "gke-$cluster"
```

#### Regex Matching

Additional to simple aliasing, `context_aliases` also supports extended matching and renaming using regular expressions.

The regular expression must match on the entire kube context, capture groups can be referenced using `$name` and `$N` in the replacement. This is more explained in the [regex crate](https://docs.rs/regex/1.5.4/regex/struct.Regex.html#method.replace) documentation.

Long and automatically generated cluster names can be identified and shortened using regular expressions:

```toml
[kubernetes.context_aliases]
# OpenShift contexts carry the namespace and user in the kube context: `namespace/name/user`:
".*/openshift-cluster/.*" = "openshift"
# Or better, to rename every OpenShift cluster at once:
".*/(?P<cluster>[\\w-]+)/.*" = "$cluster"

# Contexts from GKE, AWS and other cloud providers usually carry additional information, like the region/zone.
# The following entry matches on the GKE format (`gke_projectname_zone_cluster-name`)
# and renames every matching kube context into a more readable format (`gke-cluster-name`):
"gke_.*_(?P<cluster>[\\w-]+)" = "gke-$cluster"
```

## Line Break

The `line_break` module separates the prompt into two lines.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định | Mô tả                                                              |
| ---------- | -------- | ------------------------------------------------------------------ |
| `disabled` | `false`  | Disables the `line_break` module, making the prompt a single line. |

### Ví dụ

```toml
# ~/.config/starship.toml

[line_break]
disabled = true
```

## Lua

The `lua` module shows the currently installed version of [Lua](http://www.lua.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `.lua-version` file
- The current directory contains a `lua` directory
- The current directory contains a file with the `.lua` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                      |
| ------------------- | ------------------------------------ | -------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                      |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch`  |
| `symbol`            | `"🌙 "`                               | A format string representing the symbol of Lua.                            |
| `detect_extensions` | `["lua"]`                            | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                        |
| `detect_files`      | `[".lua-version"]`                   | Những tên tệp nào sẽ kích hoạt mô-đun này.                                 |
| `detect_folders`    | `["lua"]`                            | Những thư mục nào sẽ kích hoạt mô-đun này.                                 |
| `style`             | `"bold blue"`                        | Kiểu cho module.                                                           |
| `lua_binary`        | `"lua"`                              | Configures the lua binary that Starship executes when getting the version. |
| `disabled`          | `false`                              | Disables the `lua` module.                                                 |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v5.4.0` | The version of `lua`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[lua]
format = "via [🌕 $version](bold blue) "
```

## Memory Usage

The `memory_usage` module shows current system memory and swap usage.

By default the swap usage is displayed if the total system swap is non-zero.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn    | Mặc định                                        | Mô tả                                                    |
| ----------- | ----------------------------------------------- | -------------------------------------------------------- |
| `threshold` | `75`                                            | Hide the memory usage unless it exceeds this percentage. |
| `format`    | `"via $symbol [${ram}( \| ${swap})]($style) "` | Định dạng cho module.                                    |
| `symbol`    | `"🐏"`                                           | The symbol used before displaying the memory usage.      |
| `style`     | `"bold dimmed white"`                           | Kiểu cho module.                                         |
| `disabled`  | `true`                                          | Disables the `memory_usage` module.                      |

### Các biến

| Biến             | Ví dụ         | Mô tả                                                              |
| ---------------- | ------------- | ------------------------------------------------------------------ |
| ram              | `31GiB/65GiB` | The usage/total RAM of the current system memory.                  |
| ram_pct          | `48%`         | The percentage of the current system memory.                       |
| swap\*\*     | `1GiB/4GiB`   | The swap memory size of the current system swap memory file.       |
| swap_pct\*\* | `77%`         | The swap memory percentage of the current system swap memory file. |
| symbol           | `🐏`           | Giá trị ghi đè tuỳ chọn `symbol`                                   |
| style\*        |               | Giá trị ghi đè của `style`                                         |

\*: This variable can only be used as a part of a style string \*\*: The SWAP file information is only displayed if detected on the current system

### Ví dụ

```toml
# ~/.config/starship.toml

[memory_usage]
disabled = false
threshold = -1
symbol = " "
style = "bold dimmed green"
```

## Mercurial Branch

The `hg_branch` module shows the active branch of the repo in your current directory.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                         | Mô tả                                                                                        |
| ------------------- | -------------------------------- | -------------------------------------------------------------------------------------------- |
| `symbol`            | `" "`                           | The symbol used before the hg bookmark or branch name of the repo in your current directory. |
| `style`             | `"bold purple"`                  | Kiểu cho module.                                                                             |
| `format`            | `"on [$symbol$branch]($style) "` | Định dạng cho module.                                                                        |
| `truncation_length` | `2^63 - 1`                       | Truncates the hg branch name to `N` graphemes                                                |
| `truncation_symbol` | `"…"`                            | Biểu tượng sử dụng để nhận biết một tên nhánh được rút gọn.                                  |
| `disabled`          | `true`                           | Disables the `hg_branch` module.                                                             |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| branch    | `master` | The active mercurial branch      |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[hg_branch]
format = "on [🌱 $branch](bold purple)"
truncation_length = 4
truncation_symbol = ""
```

## Nim

The `nim` module shows the currently installed version of [Nim](https://nim-lang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `nim.cfg`
- The current directory contains a file with the `.nim` extension
- The current directory contains a file with the `.nims` extension
- The current directory contains a file with the `.nimble` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module                                                      |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"👑 "`                               | The symbol used before displaying the version of Nim.                     |
| `detect_extensions` | `["nim", "nims", "nimble"]`          | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["nim.cfg"]`                        | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold yellow"`                      | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `nim` module.                                                |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v1.2.0` | The version of `nimc`            |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[nim]
style = "yellow"
symbol = "🎣 "
```

## Nix-shell

The `nix_shell` module shows the [nix-shell](https://nixos.org/guides/nix-pills/developing-with-nix-shell.html) environment. The module will be shown when inside a nix-shell environment.

### Các tuỳ chọn

| Tuỳ chọn     | Mặc định                                       | Mô tả                                                 |
| ------------ | ---------------------------------------------- | ----------------------------------------------------- |
| `format`     | `'via [$symbol$state( \($name\))]($style) '` | Định dạng cho module.                                 |
| `symbol`     | `"❄️ "`                                        | A format string representing the symbol of nix-shell. |
| `style`      | `"bold blue"`                                  | Kiểu cho module.                                      |
| `impure_msg` | `"impure"`                                     | A format string shown when the shell is impure.       |
| `pure_msg`   | `"pure"`                                       | A format string shown when the shell is pure.         |
| `disabled`   | `false`                                        | Disables the `nix_shell` module.                      |

### Các biến

| Biến      | Ví dụ   | Mô tả                            |
| --------- | ------- | -------------------------------- |
| state     | `pure`  | The state of the nix-shell       |
| name      | `lorri` | The name of the nix-shell        |
| symbol    |         | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |         | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[nix_shell]
disabled = true
impure_msg = "[impure shell](bold red)"
pure_msg = "[pure shell](bold green)"
format = 'via [☃️ $state( \($name\))](bold blue) '
```

## Node.js

The `nodejs` module shows the currently installed version of [Node.js](https://nodejs.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `package.json`
- The current directory contains a `.node-version` file
- The current directory contains a `.nvmrc` file
- The current directory contains a `node_modules` directory
- The current directory contains a file with the `.js`, `.mjs` or `.cjs` extension
- The current directory contains a file with the `.ts` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                                                 |
| ------------------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                                                 |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch`                             |
| `symbol`            | `" "`                               | A format string representing the symbol of Node.js.                                                   |
| `detect_extensions` | `["js", "mjs", "cjs", "ts"]`         | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                                                   |
| `detect_files`      | `["package.json", ".node-version"]`  | Những tên tệp nào sẽ kích hoạt mô-đun này.                                                            |
| `detect_folders`    | `["node_modules"]`                   | Những thư mục nào sẽ kích hoạt mô-đun này.                                                            |
| `style`             | `"bold green"`                       | Kiểu cho module.                                                                                      |
| `disabled`          | `false`                              | Disables the `nodejs` module.                                                                         |
| `not_capable_style` | `bold red`                           | The style for the module when an engines property in package.json does not match the Node.js version. |

### Các biến

| Biến      | Ví dụ      | Mô tả                            |
| --------- | ---------- | -------------------------------- |
| version   | `v13.12.0` | The version of `node`            |
| symbol    |            | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |            | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[nodejs]
format = "via [🤖 $version](bold green) "
```

## OCaml

The `ocaml` module shows the currently installed version of [OCaml](https://ocaml.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a file with `.opam` extension or `_opam` directory
- The current directory contains a `esy.lock` directory
- The current directory contains a `dune` or `dune-project` file
- The current directory contains a `jbuild` or `jbuild-ignore` file
- The current directory contains a `.merlin` file
- The current directory contains a file with `.ml`, `.mli`, `.re` or `.rei` extension

### Các tuỳ chọn

| Tuỳ chọn                  | Mặc định                                                                   | Mô tả                                                                     |
| ------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`                  | `"via [$symbol($version )(\($switch_indicator$switch_name\) )]($style)"` | The format string for the module.                                         |
| `version_format`          | `"v${raw}"`                                                                | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`                  | `"🐫 "`                                                                     | The symbol used before displaying the version of OCaml.                   |
| `global_switch_indicator` | `""`                                                                       | The format string used to represent global OPAM switch.                   |
| `local_switch_indicator`  | `"*"`                                                                      | The format string used to represent local OPAM switch.                    |
| `detect_extensions`       | `["opam", "ml", "mli", "re", "rei"]`                                       | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`            | `["dune", "dune-project", "jbuild", "jbuild-ignore", ".merlin"]`           | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`          | `["_opam", "esy.lock"]`                                                    | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`                   | `"bold yellow"`                                                            | Kiểu cho module.                                                          |
| `disabled`                | `false`                                                                    | Disables the `ocaml` module.                                              |

### Các biến

| Biến             | Ví dụ        | Mô tả                                                             |
| ---------------- | ------------ | ----------------------------------------------------------------- |
| version          | `v4.10.0`    | The version of `ocaml`                                            |
| switch_name      | `my-project` | The active OPAM switch                                            |
| switch_indicator |              | Mirrors the value of `indicator` for currently active OPAM switch |
| symbol           |              | Giá trị ghi đè tuỳ chọn `symbol`                                  |
| style\*        |              | Giá trị ghi đè của `style`                                        |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[ocaml]
format = "via [🐪 $version]($style) "
```

## OpenStack

The `openstack` module shows the current OpenStack cloud and project. The module only active when the `OS_CLOUD` env var is set, in which case it will read `clouds.yaml` file from any of the [default locations](https://docs.openstack.org/python-openstackclient/latest/configuration/index.html#configuration-files). to fetch the current project in use.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định                                            | Mô tả                                                          |
| ---------- | --------------------------------------------------- | -------------------------------------------------------------- |
| `format`   | `"on [$symbol$cloud(\\($project\\))]($style) "` | Định dạng cho module.                                          |
| `symbol`   | `"☁️ "`                                             | The symbol used before displaying the current OpenStack cloud. |
| `style`    | `"bold yellow"`                                     | Kiểu cho module.                                               |
| `disabled` | `false`                                             | Disables the `openstack` module.                               |

### Các biến

| Biến      | Ví dụ  | Mô tả                            |
| --------- | ------ | -------------------------------- |
| cloud     | `corp` | The current OpenStack cloud      |
| project   | `dev`  | The current OpenStack project    |
| symbol    |        | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |        | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[openstack]
format = "on [$symbol$cloud(\\($project\\))]($style) "
style = "bold yellow"
symbol = "☁️ "
```

## Package Version

The `package` module is shown when the current directory is the repository for a package, and shows its current version. The module currently supports `npm`, `nimble`, `cargo`, `poetry`, `composer`, `gradle`, `julia`, `mix`, `helm`, `shards` and `dart` packages.

- [**npm**](https://docs.npmjs.com/cli/commands/npm) – The `npm` package version is extracted from the `package.json` present in the current directory
- [**Cargo**](https://doc.rust-lang.org/cargo/) – The `cargo` package version is extracted from the `Cargo.toml` present in the current directory
- [**Nimble**](https://github.com/nim-lang/nimble) - The `nimble` package version is extracted from the `*.nimble` file present in the current directory with the `nimble dump` command
- [**Poetry**](https://python-poetry.org/) – The `poetry` package version is extracted from the `pyproject.toml` present in the current directory
- [**Python**](https://www.python.org) - The `python` package version is extracted from the `setup.cfg` present in the current directory
- [**Composer**](https://getcomposer.org/) – The `composer` package version is extracted from the `composer.json` present in the current directory
- [**Gradle**](https://gradle.org/) – The `gradle` package version is extracted from the `build.gradle` present
- [**Julia**](https://docs.julialang.org/en/v1/stdlib/Pkg/) - The package version is extracted from the `Project.toml` present
- [**Mix**](https://hexdocs.pm/mix/) - The `mix` package version is extracted from the `mix.exs` present
- [**Helm**](https://helm.sh/docs/helm/helm_package/) - The `helm` chart version is extracted from the `Chart.yaml` present
- [**Maven**](https://maven.apache.org/) - The `maven` package version is extracted from the `pom.xml` present
- [**Meson**](https://mesonbuild.com/) - The `meson` package version is extracted from the `meson.build` present
- [**Shards**](https://crystal-lang.org/reference/the_shards_command/index.html) - The `shards` package version is extracted from the `shard.yml` present
- [**V**](https://vlang.io) - The `vlang` package version is extracted from the `v.mod` present
- [**SBT**](https://scala-sbt.org) - The `sbt` package version is extracted from the `build.sbt` present in the current directory
- [**Dart**](https://pub.dev/) - The `dart` package version is extracted from the `pubspec.yaml` present in the current directory

> ⚠️ The version being shown is that of the package whose source code is in your current directory, not your package manager.

### Các tuỳ chọn

| Tuỳ chọn          | Mặc định                          | Mô tả                                                                     |
| ----------------- | --------------------------------- | ------------------------------------------------------------------------- |
| `format`          | `"is [$symbol$version]($style) "` | Định dạng cho module.                                                     |
| `symbol`          | `"📦 "`                            | The symbol used before displaying the version the package.                |
| `version_format`  | `"v${raw}"`                       | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `style`           | `"bold 208"`                      | Kiểu cho module.                                                          |
| `display_private` | `false`                           | Enable displaying version for packages marked as private.                 |
| `disabled`        | `false`                           | Disables the `package` module.                                            |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v1.0.0` | The version of your package      |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[package]
format = "via [🎁 $version](208 bold) "
```

## Perl

The `perl` module shows the currently installed version of [Perl](https://www.perl.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `Makefile.PL` or `Build.PL` file
- The current directory contains a `cpanfile` or `cpanfile.snapshot` file
- The current directory contains a `META.json` file or `META.yml` file
- The current directory contains a `.perl-version` file
- The current directory contains a `.pl`, `.pm` or `.pod`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                                                                                 | Mô tả                                                                     |
| ------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`                                                                     | The format string for the module.                                         |
| `version_format`    | `"v${raw}"`                                                                                              | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🐪 "`                                                                                                   | The symbol used before displaying the version of Perl                     |
| `detect_extensions` | `["pl", "pm", "pod"]`                                                                                    | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Makefile.PL", "Build.PL", "cpanfile", "cpanfile.snapshot", "META.json", "META.yml", ".perl-version"]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                                                                                     | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold 149"`                                                                                             | Kiểu cho module.                                                          |
| `disabled`          | `false`                                                                                                  | Disables the `perl` module.                                               |

### Các biến

| Biến      | Ví dụ     | Mô tả                            |
| --------- | --------- | -------------------------------- |
| version   | `v5.26.1` | The version of `perl`            |
| symbol    |           | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |           | Giá trị ghi đè của `style`       |

### Ví dụ

```toml
# ~/.config/starship.toml

[perl]
format = "via [🦪 $version]($style) "
```

## PHP

The `php` module shows the currently installed version of [PHP](https://www.php.net/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `composer.json`
- The current directory contains a `.php-version` file
- The current directory contains a `.php` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🐘 "`                               | The symbol used before displaying the version of PHP.                     |
| `detect_extensions` | `["php"]`                            | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["composer.json", ".php-version"]`  | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"147 bold"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `php` module.                                                |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v7.3.8` | The version of `php`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[php]
format = "via [🔹 $version](147 bold) "
```

## Pulumi

The `pulumi` module shows the currently selected [Pulumi Stack](https://www.pulumi.com/docs/intro/concepts/stack/) and version.

::: tip

By default the Pulumi version is not shown, since it takes an order of magnitude longer to load then most plugins (~70ms). If you still want to enable it, [follow the example shown below](#with-pulumi-version).

:::

Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains either `Pulumi.yaml` or `Pulumi.yml`
- A parent directory contains either `Pulumi.yaml` or `Pulumi.yml`

### Các tuỳ chọn

| Tuỳ chọn         | Mặc định                         | Mô tả                                                                     |
| ---------------- | -------------------------------- | ------------------------------------------------------------------------- |
| `format`         | `"via [$symbol$stack]($style) "` | The format string for the module.                                         |
| `version_format` | `"v${raw}"`                      | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`         | `" "`                           | A format string shown before the Pulumi stack.                            |
| `style`          | `"bold 5"`                       | Kiểu cho module.                                                          |
| `disabled`       | `false`                          | Disables the `pulumi` module.                                             |

### Các biến

| Biến      | Ví dụ      | Mô tả                            |
| --------- | ---------- | -------------------------------- |
| version   | `v0.12.24` | The version of `pulumi`          |
| stack     | `dev`      | The current Pulumi stack         |
| symbol    |            | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |            | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

#### With Pulumi Version

```toml
# ~/.config/starship.toml

[pulumi]
format = "[🛥 ($version )$stack]($style) "
```

#### Without Pulumi version

```toml
# ~/.config/starship.toml
[pulumi]
symbol = "🛥 "
format = "[$symbol$stack]($style) "

```

## PureScript

The `purescript` module shows the currently installed version of [PureScript](https://www.purescript.org/) version. Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- Đường dẫn hiện tại chứa một tập tin `spago.dhall`
- The current directory contains a file with the `.purs` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"<=> "`                       | The symbol used before displaying the version of PureScript.              |
| `detect_extensions` | `["purs"]`                           | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["spago.dhall"]`                    | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold white"`                       | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `purescript` module.                                         |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `0.13.5` | The version of `purescript`      |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[purescript]
format = "via [$symbol$version](bold white)"
```

## Python

The `python` module shows the currently installed version of [Python](https://www.python.org/) and the current [Python virtual environment](https://docs.python.org/tutorial/venv.html) if one is activated.

If `pyenv_version_name` is set to `true`, it will display the pyenv version name. Otherwise, it will display the version number from `python --version`.

Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `.python-version` file
- The current directory contains a `Pipfile` file
- The current directory contains a `__init__.py` file
- Đường dẫn hiện tại chứa một tập tin `pyproject.toml`
- Đường dẫn hiện tại chứa một tập tin `requirements.txt`
- Đường dẫn hiện tại chứa một tập tin `setup.py`
- Đường dẫn hiện tại chứa một tập tin `tox.ini`
- The current directory contains a file with the `.py` extension.
- A virtual environment is currently activated

### Các tuỳ chọn

| Tuỳ chọn             | Mặc định                                                                                                     | Mô tả                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- |
| `format`             | `'via [${symbol}${pyenv_prefix}(${version} )(\($virtualenv\) )]($style)'`                                  | Định dạng cho module.                                                                  |
| `version_format`     | `"v${raw}"`                                                                                                  | The version format. Available vars are `raw`, `major`, `minor`, & `patch`              |
| `symbol`             | `"🐍 "`                                                                                                       | A format string representing the symbol of Python                                      |
| `style`              | `"yellow bold"`                                                                                              | Kiểu cho module.                                                                       |
| `pyenv_version_name` | `false`                                                                                                      | Use pyenv to get Python version                                                        |
| `pyenv_prefix`       | `pyenv`                                                                                                      | Prefix before pyenv version display, only used if pyenv is used                        |
| `python_binary`      | `["python", "python3", "python2"]`                                                                           | Configures the python binaries that Starship should executes when getting the version. |
| `detect_extensions`  | `["py"]`                                                                                                     | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này                                     |
| `detect_files`       | `[".python-version", "Pipfile", "__init__.py", "pyproject.toml", "requirements.txt", "setup.py", "tox.ini"]` | Tên tệp nào sẽ kích hoạt mô-đun này                                                    |
| `detect_folders`     | `[]`                                                                                                         | Thư mục nào sẽ kích hoạt mô-đun này                                                    |
| `disabled`           | `false`                                                                                                      | Disables the `python` module.                                                          |

::: tip

The `python_binary` variable accepts either a string or a list of strings. Starship will try executing each binary until it gets a result. Note you can only change the binary that Starship executes to get the version of Python not the arguments that are used.

The default values and order for `python_binary` was chosen to first identify the Python version in a virtualenv/conda environments (which currently still add a `python`, no matter if it points to `python3` or `python2`). This has the side effect that if you still have a system Python 2 installed, it may be picked up before any Python 3 (at least on Linux Distros that always symlink `/usr/bin/python` to Python 2). If you do not work with Python 2 anymore but cannot remove the system Python 2, changing this to `"python3"` will hide any Python version 2, see example below.

:::

### Các biến

| Biến         | Ví dụ           | Mô tả                                      |
| ------------ | --------------- | ------------------------------------------ |
| version      | `"v3.8.1"`      | The version of `python`                    |
| symbol       | `"🐍 "`          | Giá trị ghi đè tuỳ chọn `symbol`           |
| style        | `"yellow bold"` | Giá trị ghi đè của `style`                 |
| pyenv_prefix | `"pyenv "`      | Mirrors the value of option `pyenv_prefix` |
| virtualenv   | `"venv"`        | The current `virtualenv` name              |

### Ví dụ

```toml
# ~/.config/starship.toml

[python]
symbol = "👾 "
pyenv_version_name = true
```

```toml
# ~/.config/starship.toml

[python]
# Only use the `python3` binary to get the version.
python_binary = "python3"
```

```toml
# ~/.config/starship.toml

[python]
# Don't trigger for files with the py extension
detect_extensions = []
```

```toml
# ~/.config/starship.toml

[python]
# Display the version of python from inside a local venv.
#
# Note this will only work when the venv is inside the project and it will only
# work in the directory that contains the venv dir but maybe this is ok?
python_binary = ["./venv/bin/python", "python", "python3", "python2"]
```

## R

The `rlang` module shows the currently installed version of [R](https://www.r-project.org/). The module will be shown if any of the following conditions are met:

- The current directory contains a file with the `.R` extension.
- The current directory contains a file with the `.Rd` extension.
- The current directory contains a file with the `.Rmd` extension.
- The current directory contains a file with the `.Rproj` extension.
- The current directory contains a file with the `.Rsx` extension.
- The current directory contains a `.Rprofile` file
- The current directory contains a `.Rproj.user` folder

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"📐"`                                | A format string representing the symbol of R.                             |
| `style`             | `"blue bold"`                        | Kiểu cho module.                                                          |
| `detect_extensions` | `["R", "Rd", "Rmd", "Rproj", "Rsx"]` | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này                        |
| `detect_files`      | `[".Rprofile"]`                      | Tên tệp nào sẽ kích hoạt mô-đun này                                       |
| `detect_folders`    | `[".Rproj.user"]`                    | Thư mục nào sẽ kích hoạt mô-đun này                                       |
| `disabled`          | `false`                              | Disables the `r` module.                                                  |

### Các biến

| Biến    | Ví dụ         | Mô tả                            |
| ------- | ------------- | -------------------------------- |
| version | `v4.0.5`      | The version of `R`               |
| symbol  |               | Giá trị ghi đè tuỳ chọn `symbol` |
| style   | `"blue bold"` | Giá trị ghi đè của `style`       |

### Ví dụ

```toml
# ~/.config/starship.toml

[rlang]
format = "with [📐 $version](blue bold) "
```

## Red

By default the `red` module shows the currently installed version of [Red](https://www.red-lang.org/). Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a file with `.red` or `.reds` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🔺 "`                               | A format string representing the symbol of Red.                           |
| `detect_extensions` | `["red"]`                            | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[]`                                 | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"red bold"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `red` module.                                                |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v2.5.1` | The version of `red`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[red]
symbol = "🔴 "
```

## Ruby

By default the `ruby` module shows the currently installed version of [Ruby](https://www.ruby-lang.org/). Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `Gemfile` file
- The current directory contains a `.ruby-version` file
- The current directory contains a `.rb` file
- The environment variables `RUBY_VERSION` or `RBENV_VERSION` are set

Starship gets the current Ruby version by running `ruby -v`.

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"💎 "`                               | A format string representing the symbol of Ruby.                          |
| `detect_extensions` | `["rb"]`                             | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Gemfile", ".ruby-version"]`       | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `detect_variables`  | `["RUBY_VERSION", "RBENV_VERSION"]`  | Which environment variables should trigger this module.                   |
| `style`             | `"bold red"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `ruby` module.                                               |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v2.5.1` | The version of `ruby`            |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[ruby]
symbol = "🔺 "
```

## Rust

By default the `rust` module shows the currently installed version of [Rust](https://www.rust-lang.org/). Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `Cargo.toml` file
- The current directory contains a file with the `.rs` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🦀 "`                               | A format string representing the symbol of Rust                           |
| `detect_extensions` | `["rs"]`                             | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Cargo.toml"]`                     | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold red"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `rust` module.                                               |

### Các biến

| Biến      | Ví dụ             | Mô tả                            |
| --------- | ----------------- | -------------------------------- |
| version   | `v1.43.0-nightly` | The version of `rustc`           |
| symbol    |                   | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |                   | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[rust]
format = "via [⚙️ $version](red bold)"
```

## Scala

The `scala` module shows the currently installed version of [Scala](https://www.scala-lang.org/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `build.sbt`, `.scalaenv` or `.sbtenv` file
- The current directory contains a file with the `.scala` or `.sbt` extension
- The current directory contains a directory named `.metals`

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                 | Mô tả                                                                     |
| ------------------- | ---------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [${symbol}(${version} )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                              | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `detect_extensions` | `["sbt", "scala"]`                       | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[".scalaenv", ".sbtenv", "build.sbt"]`  | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[".metals"]`                            | Những thư mục nào nên kích hoạt các mô đun này.                           |
| `symbol`            | `"🆂 "`                                   | A format string representing the symbol of Scala.                         |
| `style`             | `"red dimmed"`                           | Kiểu cho module.                                                          |
| `disabled`          | `false`                                  | Disables the `scala` module.                                              |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `2.13.5` | The version of `scala`           |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[scala]
symbol = "🌟 "
```

## Shell

The `shell` module shows an indicator for currently used shell.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn               | Mặc định                  | Mô tả                                                        |
| ---------------------- | ------------------------- | ------------------------------------------------------------ |
| `bash_indicator`       | `bsh`                     | A format string used to represent bash.                      |
| `fish_indicator`       | `fsh`                     | A format string used to represent fish.                      |
| `zsh_indicator`        | `zsh`                     | A format string used to represent zsh.                       |
| `powershell_indicator` | `psh`                     | A format string used to represent powershell.                |
| `ion_indicator`        | `ion`                     | A format string used to represent ion.                       |
| `elvish_indicator`     | `esh`                     | A format string used to represent elvish.                    |
| `tcsh_indicator`       | `tsh`                     | A format string used to represent tcsh.                      |
| `xonsh_indicator`      | `xsh`                     | A format string used to represent xonsh.                     |
| `cmd_indicator`        | `cmd`                     | A format string used to represent cmd.                       |
| `nu_indicator`         | `nu`                      | A format string used to represent nu.                        |
| `unknown_indicator`    |                           | The default value to be displayed when the shell is unknown. |
| `format`               | `"[$indicator]($style) "` | Định dạng cho module.                                        |
| `style`                | `"white bold"`            | Kiểu cho module.                                             |
| `disabled`             | `true`                    | Disables the `shell` module.                                 |

### Các biến

| Biến      | Mặc định | Mô tả                                                      |
| --------- | -------- | ---------------------------------------------------------- |
| indicator |          | Mirrors the value of `indicator` for currently used shell. |
| style\* |          | Giá trị ghi đè của `style`.                                |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Các vị dụ

```toml
# ~/.config/starship.toml

[shell]
fish_indicator = ""
powershell_indicator = "_"
unknown_indicator = "mystery shell"
style = "cyan bold"
disabled = false
```

## SHLVL

The `shlvl` module shows the current [`SHLVL`](https://tldp.org/LDP/abs/html/internalvariables.html#SHLVLREF) ("shell level") environment variable, if it is set to a number and meets or exceeds the specified threshold.

### Các tuỳ chọn

| Tuỳ chọn    | Mặc định                     | Mô tả                                                         |
| ----------- | ---------------------------- | ------------------------------------------------------------- |
| `threshold` | `2`                          | Display threshold.                                            |
| `format`    | `"[$symbol$shlvl]($style) "` | Định dạng cho module.                                         |
| `symbol`    | `"↕️  "`                     | The symbol used to represent the `SHLVL`.                     |
| `repeat`    | `false`                      | Causes `symbol` to be repeated by the current `SHLVL` amount. |
| `style`     | `"bold yellow"`              | Kiểu cho module.                                              |
| `disabled`  | `true`                       | Disables the `shlvl` module.                                  |

### Các biến

| Biến      | Ví dụ | Mô tả                            |
| --------- | ----- | -------------------------------- |
| shlvl     | `3`   | The current value of `SHLVL`     |
| symbol    |       | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |       | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[shlvl]
disabled = false
format = "$shlvl level(s) down"
threshold = 3
```

## Singularity

The `singularity` module shows the current [Singularity](https://sylabs.io/singularity/) image, if inside a container and `$SINGULARITY_NAME` is set.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định                         | Mô tả                                            |
| ---------- | -------------------------------- | ------------------------------------------------ |
| `format`   | `'[$symbol\[$env\]]($style) '` | Định dạng cho module.                            |
| `symbol`   | `""`                             | A format string displayed before the image name. |
| `style`    | `"bold dimmed blue"`             | Kiểu cho module.                                 |
| `disabled` | `false`                          | Disables the `singularity` module.               |

### Các biến

| Biến      | Ví dụ        | Mô tả                            |
| --------- | ------------ | -------------------------------- |
| env       | `centos.img` | The current Singularity image    |
| symbol    |              | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |              | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[singularity]
format = '[📦 \[$env\]]($style) '
```

## Status

The `status` module displays the exit code of the previous command. The module will be shown only if the exit code is not `0`.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

::: warning This module is not supported on nu shell. :::

### Các tuỳ chọn

| Tuỳ chọn                | Mặc định                                                                             | Mô tả                                                   |
| ----------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------- |
| `format`                | `"[$symbol$status]($style) "`                                                        | The format of the module                                |
| `symbol`                | `"✖"`                                                                                | The symbol displayed on program error                   |
| `success_symbol`        | `"✔️"`                                                                               | The symbol displayed on program success                 |
| `not_executable_symbol` | `"🚫"`                                                                                | The symbol displayed when file isn't executable         |
| `not_found_symbol`      | `"🔍"`                                                                                | The symbol displayed when the command can't be found    |
| `sigint_symbol`         | `"🧱"`                                                                                | The symbol displayed on SIGINT (Ctrl + c)               |
| `signal_symbol`         | `"⚡"`                                                                                | The symbol displayed on any signal                      |
| `style`                 | `"bold red"`                                                                         | Kiểu cho module.                                        |
| `recognize_signal_code` | `true`                                                                               | Enable signal mapping from exit code                    |
| `map_symbol`            | `false`                                                                              | Enable symbols mapping from exit code                   |
| `pipestatus`            | `false`                                                                              | Enable pipestatus reporting                             |
| `pipestatus_separator`  | `|`                                                                                  | The symbol that separate in pipe program exit codes     |
| `pipestatus_format`     | `\\[$pipestatus\\] => [$symbol$common_meaning$signal_name$maybe_int]($style)` | The format of the module when the command is a pipeline |
| `disabled`              | `true`                                                                               | Disables the `status` module.                           |

### Các biến

| Biến           | Ví dụ   | Mô tả                                                                                       |
| -------------- | ------- | ------------------------------------------------------------------------------------------- |
| status         | `127`   | The exit code of the last command                                                           |
| hex_status     | `0x7F`  | The exit code of the last command in hex                                                    |
| int            | `127`   | The exit code of the last command                                                           |
| common_meaning | `ERROR` | Meaning of the code if not a signal                                                         |
| signal_number  | `9`     | Signal number corresponding to the exit code, only if signalled                             |
| signal_name    | `KILL`  | Name of the signal corresponding to the exit code, only if signalled                        |
| maybe_int      | `7`     | Contains the exit code number when no meaning has been found                                |
| pipestatus     |         | Rendering of in pipeline programs's exit codes, this is only available in pipestatus_format |
| symbol         |         | Giá trị ghi đè tuỳ chọn `symbol`                                                            |
| style\*      |         | Giá trị ghi đè của `style`                                                                  |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml

# ~/.config/starship.toml

[status]
style = "bg:blue"
symbol = "🔴"
format = '[\[$symbol $common_meaning$signal_name$maybe_int\]]($style) '
map_symbol = true
disabled = false

```

## Sudo

The `sudo` module displays if sudo credentials are currently cached. The module will only be shown if credentials are cached.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn        | Mặc định                | Mô tả                                                   |
| --------------- | ----------------------- | ------------------------------------------------------- |
| `format`        | `[as $symbol]($style)"` | The format of the module                                |
| `symbol`        | `"🧙 "`                  | The symbol displayed when credentials are cached        |
| `style`         | `"bold blue"`           | Kiểu cho module.                                        |
| `allow_windows` | `false`                 | Since windows has no default sudo, default is disabled. |
| `disabled`      | `true`                  | Disables the `sudo` module.                             |

### Các biến

| Biến      | Ví dụ | Mô tả                            |
| --------- | ----- | -------------------------------- |
| symbol    |       | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |       | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml

# ~/.config/starship.toml

[sudo]
style = "bold green"
symbol = "👩‍💻 "
disabled = false
```

```toml
# On windows
# $HOME\.starship\config.toml

[sudo]
allow_windows = true
disabled = false
```

## Swift

By default the `swift` module shows the currently installed version of [Swift](https://swift.org/). Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `Package.swift` file
- The current directory contains a file with the `.swift` extension

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"🐦 "`                               | A format string representing the symbol of Swift                          |
| `detect_extensions` | `["swift"]`                          | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Package.swift"]`                  | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold 202"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `swift` module.                                              |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v5.2.4` | The version of `swift`           |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[swift]
format = "via [🏎  $version](red bold)"
```

## Terraform

The `terraform` module shows the currently selected [Terraform workspace](https://www.terraform.io/docs/language/state/workspaces.html) and version.

::: tip

By default the Terraform version is not shown, since this is slow for current versions of Terraform when a lot of plugins are in use. If you still want to enable it, [follow the example shown below](#with-terraform-version).

:::

Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `.terraform` folder
- Current directory contains a file with the `.tf`, `.tfplan` or `.tfstate` extensions

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol$workspace]($style) "` | The format string for the module.                                         |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"💠"`                                | A format string shown before the terraform workspace.                     |
| `detect_extensions` | `["tf", "tfplan", "tfstate"]`        | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[]`                                 | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[".terraform"]`                     | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"bold 105"`                         | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `terraform` module.                                          |

### Các biến

| Biến      | Ví dụ      | Mô tả                            |
| --------- | ---------- | -------------------------------- |
| version   | `v0.12.24` | The version of `terraform`       |
| workspace | `default`  | The current Terraform workspace  |
| symbol    |            | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |            | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

#### With Terraform Version

```toml
# ~/.config/starship.toml

[terraform]
format = "[🏎💨 $version$workspace]($style) "
```

#### Without Terraform version

```toml
# ~/.config/starship.toml

[terraform]
format = "[🏎💨 $workspace]($style) "
```

## Thời gian

The `time` module shows the current **local** time. The `format` configuration value is used by the [`chrono`](https://crates.io/crates/chrono) crate to control how the time is displayed. Take a look [at the chrono strftime docs](https://docs.rs/chrono/0.4.7/chrono/format/strftime/index.html) to see what options are available.

::: tip

This module is disabled by default. To enable it, set `disabled` to `false` in your configuration file.

:::

### Các tuỳ chọn

| Tuỳ chọn          | Mặc định                | Mô tả                                                                                                                              |
| ----------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `format`          | `"at [$time]($style) "` | The format string for the module.                                                                                                  |
| `use_12hr`        | `false`                 | Enables 12 hour formatting                                                                                                         |
| `time_format`     | see below               | The [chrono format string](https://docs.rs/chrono/0.4.7/chrono/format/strftime/index.html) used to format the time.                |
| `style`           | `"bold yellow"`         | The style for the module time                                                                                                      |
| `utc_time_offset` | `"local"`               | Sets the UTC offset to use. Range from -24 &lt; x &lt; 24. Allows floats to accommodate 30/45 minute timezone offsets. |
| `disabled`        | `true`                  | Disables the `time` module.                                                                                                        |
| `time_range`      | `"-"`                   | Sets the time range during which the module will be shown. Times must be specified in 24-hours format                              |

If `use_12hr` is `true`, then `time_format` defaults to `"%r"`. Otherwise, it defaults to `"%T"`. Manually setting `time_format` will override the `use_12hr` setting.

### Các biến

| Biến      | Ví dụ      | Mô tả                      |
| --------- | ---------- | -------------------------- |
| thời gian | `13:08:10` | The current time.          |
| style\* |            | Giá trị ghi đè của `style` |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[time]
disabled = false
format = '🕙[\[ $time \]]($style) '
time_format = "%T"
utc_time_offset = "-5"
time_range = "10:00:00-14:00:00"
```

## Username

The `username` module shows active user's username. Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current user is root
- The current user isn't the same as the one that is logged in
- The user is currently connected as an SSH session
- The variable `show_always` is set to true

::: tip

SSH connection is detected by checking environment variables `SSH_CONNECTION`, `SSH_CLIENT`, and `SSH_TTY`. If your SSH host does not set up these variables, one workaround is to set one of them with a dummy value.

:::

### Các tuỳ chọn

| Tuỳ chọn      | Mặc định                | Mô tả                                 |
| ------------- | ----------------------- | ------------------------------------- |
| `style_root`  | `"bold red"`            | The style used when the user is root. |
| `style_user`  | `"bold yellow"`         | The style used for non-root users.    |
| `format`      | `"[$user]($style) in "` | Định dạng cho module.                 |
| `show_always` | `false`                 | Always shows the `username` module.   |
| `disabled`    | `false`                 | Disables the `username` module.       |

### Các biến

| Biến    | Ví dụ        | Mô tả                                                                                       |
| ------- | ------------ | ------------------------------------------------------------------------------------------- |
| `style` | `"red bold"` | Mirrors the value of option `style_root` when root is logged in and `style_user` otherwise. |
| `user`  | `"matchai"`  | The currently logged-in user ID.                                                            |

### Ví dụ

```toml
# ~/.config/starship.toml

[username]
style_user = "white bold"
style_root = "black bold"
format = "user: [$user]($style) "
disabled = false
show_always = true
```

## Vagrant

The `vagrant` module shows the currently installed version of [Vagrant](https://www.vagrantup.com/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `Vagrantfile` file

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"⍱ "`                               | A format string representing the symbol of Vagrant.                       |
| `detect_extensions` | `[]`                                 | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["Vagrantfile"]`                    | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"cyan bold"`                        | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `vagrant` module.                                            |

### Các biến

| Biến      | Ví dụ            | Mô tả                            |
| --------- | ---------------- | -------------------------------- |
| version   | `Vagrant 2.2.10` | The version of `Vagrant`         |
| symbol    |                  | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |                  | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[vagrant]
format = "via [⍱ $version](bold white) "
```

## V

The `vlang` module shows you your currently installed version of [V](https://vlang.io/). Mặc định module sẽ được hiển thị nếu có bất kì điều kiện nào dưới đây thoả mãn:
- The current directory contains a file with `.v` extension
- The current directory contains a `v.mod`, `vpkg.json` or `.vpkg-lock.json` file

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                                     | Mô tả                                                                     |
| ------------------- | -------------------------------------------- | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"`         | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                                  | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"V "`                                       | A format string representing the symbol of V                              |
| `detect_extensions` | `["v"]`                                      | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `["v.mod", "vpkg.json", ".vpkg-lock.json" ]` | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                         | Những thư mục nào sẽ kích hoạt mô-đun này.                                |
| `style`             | `"blue bold"`                                | Kiểu cho module.                                                          |
| `disabled`          | `false`                                      | Disables the `vlang` module.                                              |

### Các biến

| Biến      | Ví dụ  | Mô tả                            |
| --------- | ------ | -------------------------------- |
| version   | `v0.2` | The version of `v`               |
| symbol    |        | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |        | Giá trị ghi đè của `style`       |

### Ví dụ

```toml
# ~/.config/starship.toml
[vlang]
format = "via [V $version](blue bold) "
```

## VCSH

The `vcsh` module displays the current active [VCSH](https://github.com/RichiH/vcsh) repository. The module will be shown only if a repository is currently in use.

### Các tuỳ chọn

| Tuỳ chọn   | Mặc định                         | Mô tả                                                  |
| ---------- | -------------------------------- | ------------------------------------------------------ |
| `symbol`   |                                  | The symbol used before displaying the repository name. |
| `style`    | `"bold yellow"`                  | Kiểu cho module.                                       |
| `format`   | `"vcsh [$symbol$repo]($style) "` | Định dạng cho module.                                  |
| `disabled` | `false`                          | Disables the `vcsh` module.                            |

### Các biến

| Biến      | Ví dụ                                       | Mô tả                            |
| --------- | ------------------------------------------- | -------------------------------- |
| repo      | `dotfiles` if in a VCSH repo named dotfiles | The active repository name       |
| symbol    |                                             | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* | `black bold dimmed`                         | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[vcsh]
format = "[🆅 $repo](bold blue) "
```

## Zig

By default the the `zig` module shows the currently installed version of [Zig](https://ziglang.org/). Module cho sẽ được hiện nếu bất kì điều kiện nào dưới đây thoả mãn:

- The current directory contains a `.zig` file

### Các tuỳ chọn

| Tuỳ chọn            | Mặc định                             | Mô tả                                                                     |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| `format`            | `"via [$symbol($version )]($style)"` | Định dạng cho module.                                                     |
| `version_format`    | `"v${raw}"`                          | The version format. Available vars are `raw`, `major`, `minor`, & `patch` |
| `symbol`            | `"↯ "`                               | The symbol used before displaying the version of Zig.                     |
| `style`             | `"bold yellow"`                      | Kiểu cho module.                                                          |
| `disabled`          | `false`                              | Disables the `zig` module.                                                |
| `detect_extensions` | `["zig"]`                            | Những tiện ích mở rộng nào sẽ kích hoạt mô-đun này.                       |
| `detect_files`      | `[]`                                 | Những tên tệp nào sẽ kích hoạt mô-đun này.                                |
| `detect_folders`    | `[]`                                 | Những thư mục nào sẽ kích hoạt mô-đun này.                                |

### Các biến

| Biến      | Ví dụ    | Mô tả                            |
| --------- | -------- | -------------------------------- |
| version   | `v0.6.0` | The version of `zig`             |
| symbol    |          | Giá trị ghi đè tuỳ chọn `symbol` |
| style\* |          | Giá trị ghi đè của `style`       |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

### Ví dụ

```toml
# ~/.config/starship.toml

[zig]
symbol = "⚡️ "
```

## Custom commands

The `custom` modules show the output of some arbitrary commands.

These modules will be shown if any of the following conditions are met:

- The current directory contains a file whose name is in `files`
- The current directory contains a directory whose name is in `directories`
- The current directory contains a file whose extension is in `extensions`
- The `when` command returns 0
- The current Operating System (std::env::consts::OS) matchs with `os` field if defined.

::: tip

Multiple custom modules can be defined by using a `.`.

:::

::: tip

The order in which custom modules are shown can be individually set by including `${custom.foo}` in the top level `format` (as it includes a dot, you need to use `${...}`). By default, the `custom` module will simply show all custom modules in the order they were defined.

:::

::: tip

[Issue #1252](https://github.com/starship/starship/discussions/1252) contains examples of custom modules. If you have an interesting example not covered there, feel free to share it there!

:::

::: warning Command output is printed unescaped to the prompt

Whatever output the command generates is printed unmodified in the prompt. This means if the output contains special sequences that are interpreted by your shell they will be expanded when displayed. These special sequences are shell specific, e.g. you can write a command module that writes bash sequences, e.g. `\h`, but this module will not work in a fish or zsh shell.

Format strings can also contain shell specific prompt sequences, e.g. [Bash](https://www.gnu.org/software/bash/manual/html_node/Controlling-the-Prompt.html), [Zsh](https://zsh.sourceforge.io/Doc/Release/Prompt-Expansion.html).

:::

### Các tuỳ chọn

| Tuỳ chọn      | Mặc định                        | Mô tả                                                                                                                                                                         |
| ------------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `command`     | `""`                            | The command whose output should be printed. The command will be passed on stdin to the shell.                                                                                 |
| `when`        |                                 | A shell command used as a condition to show the module. The module will be shown if the command returns a `0` status code.                                                    |
| `shell`       |                                 | [See below](#custom-command-shell)                                                                                                                                            |
| `mô tả`       | `"<custom module>"`       | The description of the module that is shown when running `starship explain`.                                                                                                  |
| `files`       | `[]`                            | The files that will be searched in the working directory for a match.                                                                                                         |
| `directories` | `[]`                            | The directories that will be searched in the working directory for a match.                                                                                                   |
| `extensions`  | `[]`                            | The extensions that will be searched in the working directory for a match.                                                                                                    |
| `symbol`      | `""`                            | The symbol used before displaying the command output.                                                                                                                         |
| `style`       | `"bold green"`                  | Kiểu cho module.                                                                                                                                                              |
| `format`      | `"[$symbol($output )]($style)"` | Định dạng cho module.                                                                                                                                                         |
| `disabled`    | `false`                         | Disables this `custom` module.                                                                                                                                                |
| `os`          |                                 | Operating System name on which the module will be shown (unix, linux, macos, windows, ... ) [See possible values](https://doc.rust-lang.org/std/env/consts/constant.OS.html). |

### Các biến

| Biến      | Mô tả                                  |
| --------- | -------------------------------------- |
| output    | The output of shell command in `shell` |
| symbol    | Giá trị ghi đè tuỳ chọn `symbol`       |
| style\* | Giá trị ghi đè của `style`             |

\*: Biến này có thể chỉ được sử dụng như một phần của style string

#### Custom command shell

`shell` accepts a non-empty list of strings, where:

- The first string is the path to the shell to use to execute the command.
- Other following arguments are passed to the shell.

If unset, it will fallback to STARSHIP_SHELL and then to "sh" on Linux, and "cmd /C" on Windows.

The `command` will be passed in on stdin.

If `shell` is not given or only contains one element and Starship detects PowerShell will be used, the following arguments will automatically be added: `-NoProfile -Command -`. This behavior can be avoided by explicitly passing arguments to the shell, e.g.

```toml
shell = ["pwsh", "-Command", "-"]
```

::: warning Make sure your custom shell configuration exits gracefully

If you set a custom command, make sure that the default Shell used by starship will properly execute the command with a graceful exit (via the `shell` option).

For example, PowerShell requires the `-Command` parameter to execute a one liner. Omitting this parameter might throw starship into a recursive loop where the shell might try to load a full profile environment with starship itself again and hence re-execute the custom command, getting into a never ending loop.

Parameters similar to `-NoProfile` in PowerShell are recommended for other shells as well to avoid extra loading time of a custom profile on every starship invocation.

Automatic detection of shells and proper parameters addition are currently implemented, but it's possible that not all shells are covered. [Please open an issue](https://github.com/starship/starship/issues/new/choose) with shell details and starship configuration if you hit such scenario.

:::

### Ví dụ

```toml
# ~/.config/starship.toml

[custom.foo]
command = "echo foo"  # shows output of command
files = ["foo"]       # can specify filters but wildcards are not supported
when = """ test "$HOME" == "$PWD" """
format = " transcending [$output]($style)"

[custom.time]
command = "time /T"
extensions = ["pst"]  # filters *.pst files
shell = ["pwsh.exe", "-NoProfile", "-Command", "-"]
```
