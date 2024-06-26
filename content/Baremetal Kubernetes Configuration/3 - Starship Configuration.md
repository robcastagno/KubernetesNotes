These are the variables that we can make use of in our config:
Prompt -- Prompt-wide config options
_Battery_ --how charged device battery is
_Character_ -- Shows a character beside where the text is displayed in your terminal
Container -- container name if inside container
_Directory_ -- Path to current directory
Environmental Variable -- displays env variables
Git Branch -- active branch
Git Metrics -- number of changed lines
Git Status -- state of repo in directory
_Hostname_ -- hostname
_Kubernetes_ -- current context, namespace, user, cluster, and other details
Local IP -- IPv4 of primary network interface
Memory Usage -- current system memory
_OS_ -- shows current OS
_Shell_ -- Shows current Shell
Status -- Exit code of previous Command
_Sudo_ -- shows if Sudo credentials are cached
_Time_ -- Shows current local time
_Username_ -- username of current user
Custom Commands -- unique use cases
use () on variables that are optional

My config:
```
#start with a blank line to make terminal less claustrophobic
add_newline = true
format = """
[╭](fg:#a)\
$os\
$shell\
[ ](fg:#a bg:#b)\
$username\
$hostname\
[ ](fg:#b bg:#c)\
$directory\
[ ](fg:#c bg:#d)\
$git_branch\
$git_status\
$kubernetes\
$container\
[ ](fg:#d bg:#e)\
$time\
$battery\
$sudo\
[ ](fg:#e)
[╰─](fg:#a)\
$character\
"""

#First Background Color

[os]
format = '[$symbol](bg:#a fg:#x)'
disabled = false

[os.symbols]
Ubuntu = ''
Windows = ''

[shell]
disabled = false
format = '[ $indicator ](bg:#a fg:#x)'
bash_indicator = 'bsh'
zsh_indicator = 'zsh'
powershell_indicator = 'psh'

#Second Background Color

[username]
style_root = 'bg:#b fg:#y'
style_user = 'bg:#b fg:#x'
format = '[$user]($style bold)'
show_always = true

[hostname]
ssh_only = false
format = '[@$hostname ](bg:#b fg:#x)'

#Third Background Color

[directory]
format = '[$path ](bg:#c fg:#x)[$read_only ](bg:#c fg:#x)'
truncation_symbol = '.../'

#Fourth Background Color

[git_branch]
format = '[$symbol$branch(:$remote_branch)](bg:#d fg:#x)'

[git_status]
format = '([\[$all_status$ahead_behind\] ](bg:#d fg:#x) )'

[kubernetes]
format = '[$symbol$context( \($namespace\)) ](bg:#d fg:#x)'

[container]
format = '[$symbol \[$name\] ](bg:#d fg:#x)'

#Fifth Background Color

[time]
disabled = false
format = '[ $time](bg:#e fg:#x)'

[battery]
style = 'bg:#e fg:#x'

[sudo]
format = '[ $symbol](bg:#e fg:#x)'
symbol = '󰛄 '
disabled = false
```
Color Maps:
Purple:
```
a = #CA80E0
b = #B069E0
c = #944BE1
d = #7A2CE1
e = #6720E1

x = #FFFFFF
y = #000000
```
Indigo:
```
a = #1309F6
b = #1D2EF5
c = #2853F5
d = #3278F5
e = #3D9DF5

x = #FFFFFF
y = #000000
```
Red:
```
a = #B50A0A
b = #C71F1F
c = #DA3535
d = #EC4A4A
e = #FF6060

x = #FFFFFF
y = #000000
```
Blue:
```
a = #10A3EF
b = #10B6E6
c = #0FC9DE
d = #0FDCD5
e = #0FF0CD

x = #FFFFFF
y = #000000
```
Green:
```
a = #5EB023
b = #50C032
c = #42D041
d = #34E050
e = #26F05F

x = #FFFFFF
y = #000000
```
Orange:
```
a = #BD8A23
b = #C9972C
c = #D5A436
d = #E1B13F
e = #EEBF49

x = #FFFFFF
y = #000000
```
Yellow:
```
a = #B8A920
b = #C2BE18
c = #CCD410
d = #D6E908
e = #E1FF00

x = #FFFFFF
y = #000000
```