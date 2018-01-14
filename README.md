# Python Telegram Bot Template

[@python_telegram_bot_template](https://t.me/python_telegram_bot_template) | [GitHub](https://github.com/Nachtalb/python_telegram_bot_template)

<!-- toc -->

- [What I do](#what-i-do)
- [Commands](#commands)
- [Contributions](#contributions)
  * [Bug report / Feature request](#bug-report--feature-request)
  * [Code Contribution / Pull Requests](#code-contribution--pull-requests)
  * [Development](#development)
    + [Command Concept](#command-concept)

<!-- tocstop -->

## What I do
This bot is a template, which you can use for your own bots. I made this so my own bots are all built the same. You can
also use this README file for your bot, so you have to write less. The bot you see linked in here 
`python_telegram_bot_template` is not real and is used as an example.

## Commands
This python telegram bot templates already has some few commands predefined.
- `/start` Initializes the bot normally. The existing command does nothing atm.
- `/commands` Get a list of commands, for all the things I can do for you.
- `/unknown` This command is run when a user types in a command that does not exist. It returns an error message and is 
not listed in the available commands.
- `/restart` Restart this bot gracefully. This command can only be run by the admins defined in the settings.py. This 
command too is not listed in the available commands.
- More will eventually come. If you have any ideas or stuff you want: [Contribuions](#contributions)

## Contributions
### Bug report / Feature request
If you have found a bug or want a new feature, please file an issue on GitHub [Issues](https://github.com/Nachtalb/python_telegram_bot_template/issues)

### Code Contribution / Pull Requests
Please use a line length of 120 characters and [Google Style Python Docstrings](http://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html). 

### Development
Instead of the old `pip` with `requirements.txt`, I use the new and fancy [pipenv](https://github.com/pypa/pipfile), 
which works with [pipfile](https://docs.pipenv.org). You can get more information by following the links. If you read 
the pipenv documentation you will also be able to understand by yourself why I use, it instead of me talking about it here. 

With this info we now install our virtualenv with: 
```bash
pip install pipenv      # Install pipenv
pipenv install --three  # Create virtualeenv from your python3 installation and install the packages from the Pipfile
pipenv shell            # Spawn shell for your pipenv virtualenv
``` 

After this is complete, you have to get an API Token from Telegram. You can easily get one via the
[@BotFather](https://t.me/BotFather).

Now that you have your API Token, copy the `settings.example.py` to `settings.py` and paste in your API Token. Configure 
the rest of the settings.py and you are ready to go. Start the bot 
```bash
python run_bot.py
```

#### Command Concept
I am still working on how I want to make the commends to be used as easily as possible. At the moment this is how it 
works:

In the folder `python_telegram_bot_template/commands/` you'll find a `__init__.py`, `base.py` and `builtins.py`.
The `base.py` contains the base command, which is used for every other command. It has the following attributes:
- `all_commands`: This is a variable containing all the commands which you create with this class as Parent. If you 
override the `__init__` method you have to call super init otherwise, the command will not be added to this list. This 
list is used for adding the commands as handlers for telegram and for creating the commands list.
- `command_name`: This is what the user has to run So for the start command it would be `start`. If you do not define 
one yourself, the lowercase string of the name of your class is taken.
- `title` This is the title of your command. This does not have to be the same as the `command_name`. Your 
`command_name` could be eg. `desc` so the command would be `/desc`, but the title would be `Describe`. Like this, it is 
easier for the user to get the meaning of function from a command directly from the command list.
- `description`: As the name says, this is the description. It is shown on the command list. Describe what your command 
does in a few words.
- `handler`: This is the handler your command uses. This could be `MessageHandler`. `CommandHandler` or any other 
handler. By default, the `CommandHandler` is used. 
- `options`: This is a dict of arguments given to the handler. For the `CommandHandler`, these would be at least 
`command` and `callback`. These are given by default via the `__init__` method. The `command` is the `command_name` and 
the `callback` the `command` method.
- `hidden`: If the command is hidden from the command list. By default this is False.
- `args`: If you have args, you can write them here. Eg. a command like this: `/add_human Nick 20 male` your text would 
be like `NAME AGE GENDER`. This will be shown on the command list. 

Now we get to the methods. 
- `__init__`: Initializes the command. It adds the command to the `command_all` list, as well as sets the default for 
the `command_name` and `title` if it has to. It also sets the default `options`. If you override this function in one of 
your commands, do not forget to super init it. Otherwise, it will not be initialized and not added to the command list.
- `command`: The actual command function for your operation. This has at least the two arguments `bot` and `update`
which come from the `python-telegram-bot`. In here you define the actual logic of your code. If you do not implement 
this method in your command, you will get a `NotImplementedError`.

After you create your command, you have to call it at least once. It doesn't matter where you call it from, but I like 
to just call it directly after the class as you can see in the builtins.py. And do not forget that the file with the 
command must be loaded imported somewhere. I usually do this directly in the `__init__.py`.

#### Uploaders Concept
Like for the commands I tried to make it easier to use different kinds of file storage. You can find a configuration in 
the settings.py and the "uploaders" itself in the `python_telegram_bot_template/uploaaders/` folder. The goal is that
you can only change the configuration in the settings.py and your bot works without any further adjustment. So you could
use the local file system for local development and then switch to ssh for production, or something like this. 

You get the uploader by `from python_telegram_bot_template.uploaders import uploader`. If you use it you should always
start with `uploader.connect()` then upload / save whatever you want with `uploader.upload(...)` and finally close the 
connection with `uploader.close()`. You should even use this if you are using the file system. It is to prevent errors 
when you switch it someday in the future. 

Now to the attributes and so on: 
- `_mandatory_configuration`: It defines what must be in the configuration inside the settings.py.
E.g. for the file system this is
```python
{'path': str}
``` 
which means you have to define
```python
UPLOADER = {
    'uploader': python_telegram_bot_template.uploaders.file_system.FileSystemUploader,  # What uploader to use
    'configuration': {
        'path': '/some/path/to/your/uploads',
    }
}
```
If you are using the ssh uploader you have to define more:
```python
{'host': str, 'user': str, 'password': str, 'upload_dir': str}
```
```python
UPLOADER = {
    'uploader': 'python_telegram_bot_template.uploaders.ssh.SSHUploader',
    'configuration': {
        'host': '000.000.000.000',
        'user': 'chuck.norris',
        'password': 'i_am_immortal',
        'upload_dir': '/some/path/on/your/server/',
        'key_filename': '/home/chuck.norris/.ssh/id_rsa',  # This is not defined as mandatory because on most ssh 
        # servers you don't only use the ssh key as authentication, but if you do define this configuration as well.
    }
}
```
As you can see in the dict's above it is always a name as key and a type as value. This is checked when you initialize 
the uploader the first time.
- `configuration`: Filled in on the initialization from the uploader. It contains the configuration defined in the 
settings.py


Now to the methods: 
- `__init__`: As always this initializes the uploader. If you need to override it, don't forget to call super init 
otherwise, the configuration is not checked and applied.
- `connect`: Connect to the server / service or whatever. This method doesn't need to be implemented. E.g. the file 
system didn't need it. 
- `close`: Close the connection to the server / service ... This method too doesn't have to be implemented.
- `uplaod`: In here you define the actual logic of the uploader. If you do not implement this method in your custom 
uploader there will be an `NotImplementedError` raised, when used.

Thank you for using [@python_telegram_bot_template](https://t.me/python_telegram_bot_template).
